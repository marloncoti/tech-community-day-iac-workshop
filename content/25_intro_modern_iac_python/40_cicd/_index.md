+++
title = "Module 03: Continuous Delivery Iac Pulumi "
chapter = true
weight = 40
+++

Pulumi’s approach to infrastructure as code is great for continuous delivery, because it uses source code to model cloud resources. This means updates to your cloud infrastructure can be reviewed, validated, and tested using the same process that you have today. For example, doing code reviews via Pull Requests, running code through linters or static analysis tools, and running unit and integration tests as appropriate. It all “just works” for your cloud infrastructure the same way it would for your application code.

In this guide, we will configure GitHub and GitHub Actions to automatically deploy infrastructure created with Pulumi, using the official Pulumi Action for AWS with Python-written IaC code.

# Prerequisites:
- Have completed the previous modules of IaC with Pulumi
- Have an account on [GitHub](https://github.com/).
- Have an account on [Pulumi](https://www.pulumi.com/).


This guide also assumes you’ve reviewed the [GitHub Actions documentation](!https://help.github.com/en/categories/automating-your-workflow-with-github-actions) and are generally familiar with its concepts and syntax.


{{% children showhidden="false" %}}{{% /children %}}
