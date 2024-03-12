# Threeport SDK Introduction

The Threeport SDK is a command line tool to enable platform engineers to rapidly
develop extensions to Threeport.

## How It Works

A platform engineer uses the Threeport SDK to build software that provides
custom abstractions and functionality for their development teams to use.

Once the requirements are well understood, the process to build a custom
Threeport extension is as follows:

1. Initialize a Threeport extension project.  The platform engineer starts a new
   project with its own git repo and uses the `threeport-sdk` CLI tool to
   initialize the repository which scaffolds all the directories and code needed
   to compile their software.
1. Create one or more new API objects.  The engineer designs the data model
   based on the requirements for the extensions.  The platform engineer uses the
   `threeport-sdk` to create new API objects.  The boilerplate for the source
   code is created by the SDK.  Then the engineer adds the fields that define
   the objects attributes to meet the project's requirements.
1. The boilerplate and scaffolding provided includes functions to add the
   functionality for the reconcilers that will manage the state declared by
   users in the API objects made available.  This reconciliation logic will be
   compiled into one or more custom controllers that will be added to the
   Threeport control plane.
1. The platform engineer then compiles the controller and extended Threeport API
   server into binaries and builds container images using build-in utilities
   provided by the SDK.
1. The platform engineer deploys the custom Threeport control plane using
   build-in utilities and makes it available for testing and feedback.

> Organizations that support open source and community engagement can make their
> extensions publicly available in the case they may be applicable to other use
> cases.

## Coming Soon

Detailed guides on how to build Threeport extensions for different use cases
will be coming soon.

