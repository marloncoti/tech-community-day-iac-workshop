+++
title = "3.2 Creating a Workflow"
chapter = false
weight = 31
+++


Although the full power of the Pulumi CLI is available to use with GitHub Actions, we recommend starting with our standard workflow, which consists of two workflow files, each triggered by common GitHub events:

     - Pulumi Preview runs pulumi preview in response to a Pull Request, showing a preview of the changes to the target branch when the PR gets merged.
     - Pulumi Up runs pulumi up on the target branch, in response to a commit on that branch.


## Step 1: Create a Workflow file
In the root of your GitHub repository, create a new folder called .github if it doesn't exist.

```bash
mkdir .github

```
Inside the .github folder, create a new folder called workflows.
```bash
mkdir .github/workflows

```
Add a new file to your Pulumi project repository at .github/workflows/pulumi-iac-pr.yml containing the following workflow definition, which instructs GitHub Actions to run pulumi preview in response to all pull_request events:

Set the Name  and  Trigger Workflow in this case , Push Events

```yaml
name: pulumi-iac-pr
on:
  push:
    branches:
      - 'main'
    paths:
      - 'lab-02/**'

```

A workflow run is made up of one or more jobs, Each job runs in a runner environment specified by runs-on.


```yaml
jobs:
  deploy-dev-stack:
    name: deploy-infra
    runs-on: ubuntu-latest

```

A job is a set of steps in a workflow that is executed on the same runner. Each step is either a shell script that will be executed, or an action that will be run. Steps are executed in order and are dependent on each other

to deploying iac project the following steps (actions) are needed

1.  Checkout the repository inside runner

```yaml 
    steps:
      - name: Checkout Repository
        uses: actions/checkout@v2

```
2. setup python runtime inside runner

```yaml

      - name: Configure Python runtime
        uses: actions/setup-python@v2
        with:
          python-version: 3.10.11

```

3. Setup AWS Credentials

```yaml
      - name: Configure AWS Credentials
        uses: aws-actions/configure-aws-credentials@v1
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY }}
          aws-region: ${{ secrets.AWS_REGION }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_KEY }}

```
4. Install python dependencies

```yaml

      - name: Installing dependencies 📦️
        working-directory: ./lab-02
        run: |
          python -m venv venv
          source venv/bin/activate
          pip install -r requirements.txt

```

5.  Run Pulumi Preview Command

```yaml

      - name: Pulumi preview
        uses: pulumi/actions@v4
        with:
          work-dir: ./lab-02
          command: preview
          stack-name: dev # When using an individual account, only use stack-name.
        env:
          PULUMI_ACCESS_TOKEN: ${{ secrets.PULUMI_ACCESS_TOKEN }}


```

6.  Deploy Infrastructure

```yaml 

      - name: Deploy infrastructure 🚀
        uses: pulumi/actions@v4
        with:
          work-dir: ./lab-02
          command: up
          stack-name: dev # When using an individual account, only use stack-name.
        env:
          PULUMI_ACCESS_TOKEN: ${{ secrets.TICKETASA_GT_PULUMI_ACCESS_TOKEN }}


```


> :white_check_mark: After this change, your `workflow` should look like this:

```yaml 

name: pulumi-iac-pr.yml
on:
  push:
    branches:
      - 'main'
    paths:
      - 'lab-02/**'


jobs:
  deploy-dev-stack:
    name: deploy-infra
    runs-on: ubuntu-latest
    steps:
      - name: Checkout 🛎️
        uses: actions/checkout@v3

      - name: Setup Python ✨
        uses: actions/setup-python@v4
        with:
          python-version: 3.10.11

      - name: Configure AWS Credentials
        uses: aws-actions/configure-aws-credentials@v1
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY }}
          aws-region: ${{ secrets.AWS_REGION }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_KEY }}
      
      - name: Installing dependencies 📦️
        working-directory: ./lab-02
        run: |
          python -m venv venv
          source venv/bin/activate
          pip install -r requirements.txt
        
      - name: Pulumi preview
        uses: pulumi/actions@v4
        with:
          comment-on-pr: true
          work-dir: ./lab-02
          command: preview
          stack-name: prd # When using an individual account, only use stack-name.
        env:
          PULUMI_ACCESS_TOKEN: ${{ secrets.PULUMI_ACCESS_TOKEN }}


      - name: Deploy infrastructure 🚀
        uses: pulumi/actions@v4
        with:
          work-dir: ./lab-02
          command: up
          stack-name: prd # When using an individual account, only use stack-name.
        env:
          PULUMI_ACCESS_TOKEN: ${{ secrets.PULUMI_ACCESS_TOKEN }}


```


7. Finally Push your workflow to the remote repository

```bash
$ git add .
$ git commit -m 'added cicd workflow'
$ git push origin master 
```

