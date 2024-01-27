
# Pulumi Modern Infrastructure as Code

This workshop teaches you Modern Infrastructure as Code (IaC) concepts through a series of hands-on labs, using [Pulumi](http://pulumi.com/).
Topics covered include IaC fundamentals, in addition to application architectures and how to use IaC to create, update, and manage them.

## Building the Website

This site is built with Hugo, so you'll need it [installed](https://gohugo.io/getting-started/quick-start/#step-1-install-hugo)

First, clone this repo:

```bash
git clone https://github.com/marloncoti/tech-community-day-iac-workshop-guide.git
```

Ensure you've also cloned the submodules:

```bash
git submodule init
git submodule update
```

Then serve the website with Hugo:

```bash
hugo server
```

### Learning Objectives

- Getting started with Pulumi
- Deploy a website on Amazon S3 (First stack, package providers)
- Deploy Web Server using Amazon Ec2 (Understanding Stacks , Stack References)
- version control and CICD for pulumi projects



