# Setting up the Gitpod Environment
In this workshop, I am using Gitpod to save costs and I already have a Gitpod account linked to my GitHub account with my AWS Credentials already populated.

For the workshop, we need the following tools:
- AWSCLI
- kubectl
- eksctl

The following are also needed but are installed automatically on Gitpod; Git, Python, pip, bash-completion, jq.

Now we write a `gitpod.yml` file that installs the unavailable tools and recursively does so anytime we start up the environment. This file should be in the root directory.