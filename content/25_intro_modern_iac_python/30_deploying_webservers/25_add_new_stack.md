+++
title = "2.4  Add new Stack"
chapter = false
weight = 20
+++

Every Pulumi program is deployed to a stack. A stack is an isolated, independently configurable instance of a Pulumi program. Stacks are commonly used to denote different phases of development (such as development, staging, and production) or feature branches (such as feature-x-dev).

A project can have as many stacks as you need. By default, Pulumi creates a stack for you when you start a new project using the pulumi new command.

## Step 1 &mdash; Create new stack

To create a new stack, use `pulumi stack init --copy-config-from dev stackName`. This creates an empty stack stackName with the same configuration properties defined in dev stack. The project that the stack is associated with is determined by finding the nearest Pulumi.yaml file.  

The stack name must be unique within a project. Stack names may only contain alphanumeric characters, hyphens, underscores, or periods.

```bash
pulumi stack init --copy-config-from dev staging

```

### Listing stacks

To see the list of stacks associated with the current project (the nearest Pulumi.yaml file), use pulumi stack ls.

```bash
$ pulumi stack ls 

NAME      LAST UPDATE     RESOURCE COUNT  URL
dev       16 minutes ago  5               https://app.pulumi.com/tcd2024-iac/lab-01/dev
staging*  n/a             n/a             https://app.pulumi.com/tcd2024-iac/lab-01/staging

```
### Select a stack

The top-level `pulumi` operations `config`, `preview`, `update` and `destroy` operate on the active stack. To change the active stack, run `pulumi stack select`.

```bash
$pulumi stack select dev

NAME     LAST UPDATE     RESOURCE COUNT  URL
dev*     20 minutes ago  5               https://app.pulumi.com/tcd2024-iac/lab-01/dev
staging  n/a             n/a             https://app.pulumi.com/tcd2024-iac/lab-01/staging

```


### View stack resources
To view details of the currently selected stack, run pulumi stack with no arguments. This displays the metadata, resources and output properties associated with the stack.

```bash
$ pulumi stack
Current stack is dev:
    Owner: tcd2024-iac
    Last updated: 18 minutes ago (2024-01-26 05:20:13.278631168 +0000 UTC)
    Pulumi version used: v3.103.1
Current stack resources (5):
    TYPE                                    NAME
    pulumi:pulumi:Stack                     lab-01-dev
    ├─ aws:ec2/securityGroup:SecurityGroup  web-secgrp
    ├─ aws:ec2/instance:Instance            web-server
    ├─ pulumi:providers:aws                 default
    └─ pulumi:providers:aws                 default_6_18_2

Current stack outputs (2):
    OUTPUT    VALUE
    hostname  ec2-35-168-22-227.compute-1.amazonaws.com
    ip        35.168.22.227

More information at: https://app.pulumi.com/tcd2024-iac/lab-01/dev

Use `pulumi stack select` to change stack; `pulumi stack ls` lists known ones
```

Destroy a stack

Before deleting a stack, if the stack still resources associated with it, they must first be deleted via pulumi destroy. This command uses the latest configuration values, rather than the ones that were last used when the program was deployed.

