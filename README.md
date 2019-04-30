# cloudformation-portal-core

Templates for Jungo's Portal Project

## Contents

The templates and scripts in this repository create Jungo's Portal application.

The top level directory contains a number of scripts used by cftool to create the various services and infrastructure used by Portal.  

* codebuild/ - Contains the spec file used by cftool to validate and build the required stacks.
* codepipeline/ - Contains a script and the stacks required to build an AWS CodeBuild Project and CodePipeline. The latter actually creates the stacks using the ChangeSet and Execute ChangeSet stack procedures.
* shared - Cloudformation templates for the creation of the infrastructure and services. 

## Operational Overview

Most of the stacks in the Portal application are created by CodePipeline using cftool. CodePipeline executes cftool via a special ECS container called shared-code-build. This container is created earlier on by the stackset shared-codepipeline-base. All the actions performed by cftool are defined in codebuild/buildspec.yml.

This file defines which cf files should be used to build the stacks. Temporary credentials are acquired using the aws sts command so that that the configuration repository can be cloned. The files are then validated using jsonlint. cftool then compiles, or expands, the .cf files into complete CloudFormation stacks, known as artifacts,  which in turn are validated using ``aws cloudformation validate-template``

If all these processes complete successfully CodePipeline creates a Stack ChangeSet then executes the ChangeSet to create the required stack in the environment's account.  

## Prerequisites

Before attempting to create any of these stacks the stacks and stacksets from the jungo-core-cloudformation repository must be created first:

* Access9apps
* generic-base
* StackSet-generic-roles-and-policies
* StackSet-generic-alarmtopics
* StackSet-generic-cloudtrail
* StackSet-shared-vpc
* StackSet-shared-bastion
* portal-codepipeline-cross-account-role
* StackSet-shared-codepipeline-base

Both AWS environments must have read only access to Jungo's GitHub repository (PoweredByTheCrowd). All the environment variables listed in Appendix A must be available in the AWS Secrets Manager.

Make a clone of this repository to your Mac:
```
git clone https://git-codecommit.eu-west-1.amazonaws.com/v1/repos/jungo-portal-cloudformation
```

## Usage

When the above stacks and stacksets have been completed, the Development environment may be created thus:


```
export AWS_PROFILE=jungo-dev
cd codepipeline
./create-or-update-stack.sh portal-cf-codepipeline-dev create
```

Create a database for Development called ``portal`` in the Dev RDS instance using the database username and password defined in the Secrets Manager of the jungo-dev account.

The Production environment is created after the Development subject to the prequisites specified above:

```
export AWS_PROFILE=jungo-prd
cd codepipeline
./create-or-update-stack.sh portal-ecr create
./create-or-update-stack.sh portal-cf-codepipeline-prd create
```

Create a database for Production called ``portal`` in the Prd RDS instance using the database username and password defined in the Secrets Manager of the jungo-prd account.

### Appendix A

API_DOMAIN
APP_ENV
ASPNETCORE_ENVIRONMENT
BASIC_AUTH_PASS
CANOPY_INTEREST_RATE
COOKIE_DOMAIN
COOKIE_SECRET
DOMAIN
FUNDATION_URL
HOST
INTERCOM_KEY
INTERCOM_TOKEN
INVESTMENT_MINUTES_TO_EXPIRE
JUNGO_DB_PASS
JUNGO_DB_NAME
JUNGO_DB_UNAME
LOG_LEVEL
MANDRILL_KEY
MOLLIE_KEY
NODE_ENV
POSTCODEAPI_KEY
POSTCODEAPI_SECRET
TOPICUS_REMOTE_ADDRESS
TOPICUS_AUTH_TOKEN




