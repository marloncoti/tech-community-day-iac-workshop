+++
title = "3.1 Create a Repository on Github"
chapter = false
weight = 31
+++

## Step 1: Create a New Repository on GitHub
Log in to your GitHub account.
Create a new repository by clicking the "New" button on the GitHub homepage.
Assign a name to the repository and provide a brief description.
Click "Create repository" to create the repository.

## Link Your Local Repository to the Remote Repository

Assuming you already have a local Git repository:

Open a terminal or command prompt.
Navigate to your local repository's root directory using the cd command.

```bash
cd /path/to/your/local/repository

```
Initialize the repository if you haven't already:

```bash
git init

```

Add the remote repository URL. Replace your-username and your-repository with your GitHub username and repository name.

```bash 
git remote add origin https://github.com/your-username/your-repository.git

```

Verify that the remote has been added:

```bash

git remote -v

```

## Step 3: Push Your Code to the Remote Repository
Commit your changes locally:

```bash

git add .
git commit -m "Initial commit"

```

Push your code to the remote repository:

```bash
git push -u origin main
```