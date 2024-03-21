# Deploy Workload with Secret on AWS

In this guide we're going to deploy a sample Nginx workload on AWS and expose a
secret to that workload from a secret stored in AWS Secret Manager.

## Prerequisites

You'll need a Threeport control plane for this guide.  You have two options:

1. Install a [Local Threeport](../../install/install-threeport-local) instance and
   then provision a [Remote Kubernetes
   Runtime](../../kubernetes-runtime/remote-kubernetes-runtime) for your workload.
1. Install a [Remote Threeport](../../install/install-threeport-aws) instance
   on AWS and use the Kubernetes instance that is used to host Threeport to deploy
   your workload.

## Create Secret Definition

TODO

