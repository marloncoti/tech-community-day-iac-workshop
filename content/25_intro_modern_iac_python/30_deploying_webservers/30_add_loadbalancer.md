+++
title = "2.5 Deploy staging new Stack"
chapter = false
weight = 25
+++

Now our project uses stack references,  We are ready to deploy to a new environment 

## Step 1 &mdash; change to the staging stack created previously

remember , to see project stacks we can run the command 
```bash
pulumi stack ls 
```

to switch the project stack we can run the following command. 

```bash
$ pulumi stack select staging 

NAME      LAST UPDATE  RESOURCE COUNT  URL
dev       n/a          n/a             https://app.pulumi.com/marloncoti/iac-workshop-webservers/dev
staging*  n/a          n/a             https://app.pulumi.com/marloncoti/iac-workshop-webservers/staging
```


## Step 2 Set the stack references values for staging stack 

Set the  instance_size, instance_count and ami  configuration variables using the pulumi commnad.   `pulumi config set`
execute the following commands


```bash 
    pulumi config set instance_size t3.micro # 
    pulumi config set instance_count 2
    pulumi config set ami 'amzn2-ami-hvm-*-x86_64-ebs'

```

So in practice different environments generally are  deployed on different aws accounts, in this workshp we'll use a different region to deploy our staging environment. 

set the default aws-region for the staging environment.

```bash 
    pulumi config set aws:region us-east-2 # 
```

That's it,  Now we can deploy our new environment.
Notice that the main code did not change

Deploy these updates:

```bash
pulumi up
```

Notice that  new resources are being identified with the corresponding stack `lab-01-staging-wb-sg`

```bash
Previewing update (staging)

View in Browser (Ctrl+O): https://app.pulumi.com/tcd2024-iac/lab-01/staging/previews/0242505b-f2a1-408d-b367-27c10b0f031b

     Type                      Name                         Plan       
 +   pulumi:pulumi:Stack       lab-01-staging               create     
 +   ├─ aws:ec2:SecurityGroup  lab-01-staging-wb-sg         create     
 +   ├─ aws:ec2:Instance       lab-01-staging-ec2-server-0  create     
 +   └─ aws:ec2:Instance       lab-01-staging-ec2-server-1  create     

Outputs:
    instance_ids       : [
        [0]: output<string>
        [1]: output<string>
    ]
    instance_public_dns: [
        [0]: output<string>
        [1]: output<string>
    ]
    instance_public_ips: [
        [0]: output<string>
        [1]: output<string>
    ]

Resources:
    + 4 to create

Do you want to perform this update? yes
```

## Step 5 &mdash; Verify 

Now we can curl the load balancer:

```bash
for i in {0..10}; do curl $(pulumi stack output url); done
```

Observe that the resulting text changes based on where the request is routed:

```
Hello, World -- from us-west-2a!
Hello, World -- from us-west-2a!
Hello, World -- from us-west-2d!
Hello, World -- from us-west-2b!
Hello, World -- from us-west-2b!
Hello, World -- from us-west-2c!
Hello, World -- from us-west-2b!
Hello, World -- from us-west-2a!
Hello, World -- from us-west-2c!
Hello, World -- from us-west-2d!
Hello, World -- from us-west-2a!
```

## Step 6 &mdash; Destroy Everything

Finally, destroy the resources and the stack itself:

```
pulumi destroy
pulumi stack rm
```
