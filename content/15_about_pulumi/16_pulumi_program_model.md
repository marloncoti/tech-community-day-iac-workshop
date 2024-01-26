---
title: "Pulumi Program Model"
chapter: false
weight: 16
---



![What Is Pulumi](https://www.pulumi.com/images/docs/pulumi-programming-model-diagram.svg)


Programs reside in a project, which is a directory that contains source code for the program and metadata on how to run the program. After writing your program, you run the Pulumi CLI command pulumi up from within your project directory. This command creates an isolated and configurable instance of your program, known as a stack. Stacks are similar to different deployment environments that you use when testing and rolling out application updates. For instance, you can have distinct development, staging, and production stacks that you create and test against.