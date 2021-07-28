# RiSolve AWS CloudFormation Infrastructure Architecture

This repository is a fork of [AWS QuickStart HIPAA Compliance](https://github.com/aws-quickstart/quickstart-compliance-hipaa) with modifications and additions required to deploy RiSolve app in a HIPAA compliant AWS infrastructure.

More details on AWS QuickStart HIPAA Compliance:

For architectural details, step-by-step instructions, and customization options, see the [deployment guide](https://fwd.aws/vd5pn?).

## Table of contents

- [Prerequisites](#prerequisites)
- [Templates](#templates)
- [Usage](#miscellaneous)
- [Miscellaneous](#miscellaneous)
- [TODO](#todo)

## Prerequisites

In order to use these templates [aws-cli](https://aws.amazon.com/cli/) is required to be installed and configured with credentials.

## Templates

Templates are written in YAML files and here they are listed in the order of how they should be deployed:

1. `compliance-hipaa-entrypoint.template.yaml`

    a. ConfigStack: `compliance-hipaa-config.template.yaml`

    b. LogStack: `compliance-hipaa-logging.template.yaml`

    c. StgStack: `compliance-hipaa-stg.template.yaml`

    d. ProdStack: `compliance-hipaa-prod.template.yaml`

    e. MgmtStack: `compliance-hipaa-management.template.yaml`

    f. TgwStack: `compliance-hipaa-transit-gateway.template.yaml`

2. `bastion.template.yaml`

3. `web.template.yaml`

4. `aurora-postgresql-db.template.yaml`

5. `elasticache-redis.template.yaml`

## Usage

For ease of use, the repository provides 3 helpers scripts in `bin/` to manage the CloudFormation stacks: `create-stack`, `update-stack`, `delete-stack`.

The status and progress of the CloudFormation stack can be monitored from AWS management console.

### create-stack

Arguments:

- `-s` stack_name
- `-t` template
- `-p` parameters
- `-c` capabilities
- `-i` iam_account

Example:

    bin/create-stack -s "RiSolve-HIPAA-Infra" -t compliance-hipaa-entrypoint.template.yaml -p compliance-hipaa-stack.parameters.json -i <iam-user>

### update-stack

Arguments:

- `-s` stack_name
- `-t` template
- `-p` parameters
- `-c` capabilities
- `-i` iam_account

Example:

    bin/update-stack -s "RiSolve-HIPAA-Infra" -t compliance-hipaa-entrypoint.template.yaml -p compliance-hipaa-stack.parameters.json -i <iam-user>

### delete-stack

Arguments:

- `-s` stack_name
- `-i` iam_account

Example:

    bin/delete-stack -s "RiSolve-Staging-Redis" -i <iam-user>

## Miscellaneous

1. `compliance-hipaa-entrypoint.template.yaml` is a CloudFormation stack built from multiple Stacks, as seen above. This stack relies on the existence of an S3 bucket where all the dependent stack templates are located. The bucket configuration can be provided with the parameters `QSS3BucketName` and `QSS3BucketRegion`.

2. VPC's IP CIDR blocks have certain restrictions imposed by AWS in order to use TargetGroup with IP targets for LoadBalancers. More details about this and the list of allowed IP blocks can be found [here](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-target-groups.html#target-type).

3. Parameters have to be in JSON format because `aws-cli` does not support YAML parameters files. More info [here](https://github.com/aws/aws-cli/issues/2275)

## TODO

1. Improve stack resources tagging

2. Email group for SNS topics alarm notifications

3. Auto deregister EC2 instances from TargetGroups before they are terminated. (Currently has to be done manually in the case of instance failure)

4. Improve BastionHost access logging

5. Improve ApplicationLoadBalancer access logging
