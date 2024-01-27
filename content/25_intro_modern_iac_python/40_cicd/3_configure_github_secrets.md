+++
title = "3.3 Configure Repository Secrets"
chapter = false
weight = 32
+++

Now that you’ve got the common workflows defined, you’ll need to configure your secrets. Secrets are exposed as environment variables to the GitHub Actions runtime environment

## . Configuring Your Secrets

With your workflow files committed and pushed to GitHub, head on over to your repo’s Settings tab, where you’ll find the new Secrets area:

![github-action-secrets](https://www.pulumi.com/images/docs/reference/gh-actions-secrets.png)



First, create a new Pulumi Access Token, then submit that token as a new secret named named PULUMI_ACCESS_TOKEN. This enables your GitHub Action to communicate with the Pulumi Cloud on your behalf.

Next, add secrets for your cloud credentials, just as you did PULUMI_ACCESS_TOKEN above, based on your provider of choice. For example:

AWS_ACCESS_KEY_ID, AWS_REGION and AWS_SECRET_ACCESS_KEY and AWS_REGION

## Try It Out!

To try things out, make a change in your lab-02 pulumi program,   commit it and push it to the remote repository,  and  now you will see these new actions showing up in the usual GitHub Checks dialog, with a green checkmark if everything went as planned:

![run-action](https://www.pulumi.com/images/docs/reference/gh-actions-checks.png)
