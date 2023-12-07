# Deploy Workload on AWS

In this guide, we're going to deploy a sample Wordpress app and use Threeport to
manage some dependencies for it.

First, create a workspace on your local filesystem:

```bash
mkdir threeport-test
cd threeport-test
```

Download a sample workload config as follows:

```bash
curl -O https://raw.githubusercontent.com/threeport/threeport/main/samples/wordpress-workload-remote.yaml
```

You now have the workload config on your local filesystem.  If you open the file you'll
see it has the following fields:

```yaml
Workload:
  Name: "wordpress"
  YAMLDocument: "wordpress-manifest-remote.yaml"
  AwsRelationalDatabase:
    Name: wordpress-db
    AwsAccountName: default-account
    Engine: mariadb
    EngineVersion: "10.11"
    DatabaseName: wordpress
    DatabasePort: 3306
    StorageGb: 20
    MachineSize: XSmall
    WorkloadSecretName: wordpress-db-conn
  DomainName:
    Domain: example.com             # update this value
    Zone: Public
    AdminEmail: admin@example.com   # update this value
  Gateway:
    TCPPort: 443
    Path: /
    TLSEnabled: true
    ServiceName: getting-started-wordpress
    SubDomain: www
```

The `Name` field is an arbitrary user-defined name that must be unique, i.e. no
other workload may use the same name.

The `YAMLDocument` field refers to another file with the Kubernetes resource
manifests.  Download that file as well:

```bash
curl -O https://raw.githubusercontent.com/threeport/threeport/main/samples/wordpress-manifest-remote.yaml
```

The `AwsRelationalDatabase` field includes the specification for an AWS RDS
instance that will be used for the Wordpress database.  Threeport will spin up
that RDS instance for the sample app and connect it.  Also, when you delete your
app, Threeport will clean up the RDS instance as well.

The `DomainName` name field provides a config for managing a DNS record for the
sample app.  This will only work if you are managing a domain with Route53 in
AWS with a Hosted Zone.  If you aren't using Route53, comment out the
`DomainName` section (lines 14-17) and we'll use an AWS load balancer endpoint
to connect to the sample app.

The `Gateway` field includes a config to set up ingress to our sample app from
the public internet and terminate TLS.

### Create Workload

Once you have made the necessary changes to the workload config, we can create
the workload as follows:

```bash
tptctl create workload --config wordpress-workload-remote.yaml
```

Threeport will now do the following:

* Install the Wordpress app.
* Spin up an RDS database for your app.
* If you specified a `DomainName` config, it will install external-dns on your
  EKS cluster and instruct it to configure Route53.
* Install gloo-edge for gateway ingress and configure it for your app.
* Install cert-manager to provision and rotate TLS certificates for the sample app.
  Note: the [Let's Encrypt](https://letsencrypt.org/) staging environment will
  be used for this guide.  This means the certificate issued will not be
  publicly trusted - you will have to tell your browser to trust it.  When the
  production environment is used, it will be publicly trusted.

## Validate

It will take 10 minutes or so for AWS to spin up the RDS database instance.
You can check the RDS console in AWS to track it's progress if you like.
Shortly after the database is up, Threeport will create a secret to provide
database connection credentials to the sample app and it will begin running.

If you have [kubectl](https://kubernetes.io/docs/tasks/tools/#kubectl) and the
[AWS CLI](https://aws.amazon.com/cli/) installed, you can check the progress of
the app as follows.

Update your kubeconfig:

```bash
aws eks update-kubeconfig --name threeport-test
```

Then, view the pods in the remote Kubernetes cluster:

```bash
kubectl get pods -A
```

Once your Wordpress app pods show Status: Running, your app is ready to visit.

Remember, you will need to tell your browser to trust the connection as we're
using the Let's Encrypt staging environment.

In your browser, visit: `https://www.example.com`.  Change the domain to the one you used,
or repace it with the AWS load balancer endpoint which can be found in the AWS
EC2 console.

Note: This example does not implement HTTP redirects to HTTPS so you'll need to
include the `https://` protocol.

## Clean Up

Threeport will not delete a Kubernetes cluster with workload instance running.

View the workload instances with:

```bash
tptctl get workload-instances
```

Delete the workload instances:

```bash
tptctl delete workload-instance -n wordpress
tptctl delete workload-instance -n gloo-edge
tptctl delete workload-instance -n external-dns  # if you used the DomainName config
```

Uninstall Threeport:

```bash
tptctl down control-plane -n test
```

Remove the test configs from you filesystem:

```bash
cd ../
rm -rf threeport-test
```

