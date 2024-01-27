+++
title = "2.4 Making a stack configurable"
chapter = false
weight = 24
+++


One of the main reasons to use stacks is to have different configurations between them. In this example, we will set a configuration that varies between our dev and staging stacks and set it programmatically.

First, we need to define the configuration. We have already set this in the dev stack in the Fundamentals tutorial. Let’s take a look! Make sure the dev stack is active:

```bash
$ pulumi stack select dev
```
Now, run the following command to get the values for this stack’s configuration:

```bash
$ pulumi config
KEY          VALUE
aws:region   us-east-1
pulumi:tags  {"pulumi:template":"aws-python"}

```


Now comes the fun part! Let’s add a little code to pull in the values from project stacks, based on the corresponding environment.

## Step 1 –  Add this code to the __main__.py

```python

config = pulumi.Config()

project = pulumi.get_project()
stack = pulumi.get_stack()
resource_prefix = f'{project}-{stack}'

size = config.require('instance_size')
instance_count = config.require_int('instance_count')
instance_ami = config.require('ami')

```

The, size, quantity and ami variables are new, as is the stack_ref declaration.  That declaration sets up an instance of the StackReference class, which needs the fully qualified name of the stack as an input.

## Step 2 - Set the missing stack references 

Set the  instance_size, quantity and ami,  configuration variables using the pulumi commnad.   `pulumi config set`
execute the following commands

```bash 
    pulumi config set instance_size t2.micro
    pulumi config set instance_count 1
    pulumi config set ami 'amzn2-ami-hvm-*-x86_64-ebs'

```
### Step 3 - Replace hardcoded values by stack reference values

Edit the following code blocks 

1.   Ami Reference

```python

# Get the Latest AMI
ami = aws.ec2.get_ami(
    most_recent=True,
    owners=["amazon"],
    filters=[aws.ec2.GetAmiFilterArgs(name="name", values=[instance_ami])], # uses stack reference value
)
```

2.  Replace resource names to prevent conflict resources identifiers , 

A good practice is to standardize resource identifiers. so in this workshop we will define the following approach [project]-[stack]-resource-suffix

changing The Security Group resource name


```python
#Provision SG
group = aws.ec2.SecurityGroup(
    f'{resource_prefix}-wb-sg',  #use a dynamic resource name based on the current project and stack
    description='Enable HTTP, HTTPS, SSH access',
    ingress=[
        {'protocol': 'tcp', 'from_port': 80, 'to_port': 80, 'cidr_blocks': ['0.0.0.0/0']},
        {'protocol': 'tcp', 'from_port': 443, 'to_port': 443, 'cidr_blocks': ['0.0.0.0/0']},
        {'protocol': 'tcp', 'from_port': 22, 'to_port': 22, 'cidr_blocks': ['0.0.0.0/0']},
    ],
    egress=[
        {'protocol': '-1', 'from_port': 0, 'to_port': 0, 'cidr_blocks': ['0.0.0.0/0']},
    ])
```

2.1 Update the instances to be deployed based on stack reference and naming it dynamically

```python 
# Create multiple EC2 instances
instances = []
ips = []
hostnames = []
for i in range(instance_count):
    
    instance = aws.ec2.Instance(f'{resource_prefix}-ec2-server-{i}',
        instance_type=size,  # instance type can be changed based on requirements
        ami=ami.id,
        vpc_security_group_ids=[group.id],
        user_data="""#!/bin/bash
echo "Hello, World!" > index.html
nohup python -m SimpleHTTPServer 80 &""",
        tags={'Name': f'{resource_prefix}-ec2-{i}'})

    instances.append(instance.id)    
    ips.append(instance.public_ip)
    hostnames.append(instance.public_dns)
    

```

2.2  Update the stack ouput based on the number of instaces

```python

# Export the IDs and public IPs of the instances
pulumi.export('instance_ids',instances)
pulumi.export('instance_public_ips', ips)
pulumi.export('instance_public_dns',hostnames)

```

> :white_check_mark: After this change, your `__main__.py` should look like this:

```python
# Tech Communit day pulumi program workshop

import pulumi
import pulumi_aws as aws

config = pulumi.Config()

project = pulumi.get_project()
stack = pulumi.get_stack()
resource_prefix = f'{project}-{stack}'

size = config.require('instance_size')
instance_count = config.require_int('instance_count')
instance_ami = config.require('ami')


# Get the Latest AMI
ami = aws.ec2.get_ami(
    most_recent=True,
    owners=["amazon"],
    filters=[aws.ec2.GetAmiFilterArgs(name="name", values=[instance_ami])],
)

#Provision SG
group = aws.ec2.SecurityGroup(
    f'{resource_prefix}-wb-sg',  #use a dynamic resource name based on the current project and stack
    description='Enable HTTP, HTTPS, SSH access',
    ingress=[
        {'protocol': 'tcp', 'from_port': 80, 'to_port': 80, 'cidr_blocks': ['0.0.0.0/0']},
        {'protocol': 'tcp', 'from_port': 443, 'to_port': 443, 'cidr_blocks': ['0.0.0.0/0']},
        {'protocol': 'tcp', 'from_port': 22, 'to_port': 22, 'cidr_blocks': ['0.0.0.0/0']},
    ],
    egress=[
        {'protocol': '-1', 'from_port': 0, 'to_port': 0, 'cidr_blocks': ['0.0.0.0/0']},
    ])


# Create multiple EC2 instances
instances = []
ips = []
hostnames = []
for i in range(instance_count):
    
    instance = aws.ec2.Instance(f'{resource_prefix}-ec2-server-{i}',
        instance_type=size,  # instance type can be changed based on requirements
        ami=ami.id,
        vpc_security_group_ids=[group.id],
        user_data="""#!/bin/bash
echo "Hello, World!" > index.html
nohup python -m SimpleHTTPServer 80 &""",
        tags={'Name': f'{resource_prefix}-ec2-{i}'})

    instances.append(instance.id)    
    ips.append(instance.public_ip)
    hostnames.append(instance.public_dns)
    
    
    
# Export the IDs and public IPs of the instances
pulumi.export('instance_ids',instances)
pulumi.export('instance_public_ips', ips)
pulumi.export('instance_public_dns',hostnames)



```
Now run a command to preview your stack with the new resource definitions:

```bash
pulumi preview
```

You will see output like the following:

```
Previewing update (dev)

View in Browser (Ctrl+O): https://app.pulumi.com/tcd2024-iac/lab-01/dev/previews/d34f182d-7b87-4bb7-b904-91355e28dcf8

     Type                      Name                    Plan       
     pulumi:pulumi:Stack       lab-01-dev                         
 +   ├─ aws:ec2:Instance       lab-01-devec2-server-0  create     
 +   ├─ aws:ec2:SecurityGroup  lab-01-dev-web-sg       create     
 -   ├─ aws:ec2:Instance       web-server-www          delete     
 -   └─ aws:ec2:SecurityGroup  web-secgrp              delete     

Outputs:
  + instance_ids       : output<string>
  + instance_public_ips: output<string>
  - public_dns         : "ec2-107-21-55-128.compute-1.amazonaws.com"
  - public_ip          : "107.21.55.128"

Resources:
    + 2 to create
    - 2 to delete
    4 changes. 1 unchanged

```

Notice that your original server was deleted and new ones created in its place, because its name changed.

deploy changes:

```bash
pulumi preview
```

To test the changes, curl any of the resulting IP addresses or hostnames:

```bash
curl $(pulumi stack output instance_public_dns | jq -r ".[${1}]")
```


