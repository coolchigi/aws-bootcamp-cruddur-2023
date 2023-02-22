# Week 0 — Billing and Architecture
## **GitPod**
For this Cloud BootCamp we are going to be using GitPod as our dev environment. This is to satisfy the requirement:
> The fractional CTO has asked that everything be developed in Gitpod, (a Cloud Developer Environment). This will allow the CTO at a press of a button launch the developer environment in a clean state to help with any tricky or emergency implementations, and ensure developer accountability.

The first thing you'll need to do is install the AWS CLI. We are going to be running commands to create sns topics, cloudwatch alarms as well as budget and billing alarms so you need to configure your Gitpod workspace. 

### Install AWS CLI

- We are going to install the AWS CLI when our Gitpod enviroment lanuches.
- We are are going to set AWS CLI to use partial autoprompt mode to make it easier to debug CLI commands.
- The bash commands we are using are the same as the [AWS CLI Install Instructions]https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html

Update our `.gitpod.yml` to include the following task.

```sh
tasks:
  - name: aws-cli
    env:
        AWS_CLI_AUTO_PROMPT: on-partial
    init: |
      cd /workspace
      curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
      unzip awscliv2.zip
      sudo ./aws/install
      cd $THEIA_WORKSPACE_ROOT
vscode:
  extensions:
    - 42Crunch.vscode-openapi
```

### Set Env Vars

We will set these credentials for the current bash terminal. The following resets your credentials which allows you to set it yourself.
```
export AWS_ACCESS_KEY_ID=""
export AWS_SECRET_ACCESS_KEY=""
export AWS_DEFAULT_REGION=us-east-1
```

We'll tell Gitpod to remember these credentials if we relaunch our workspaces
```
gp env AWS_ACCESS_KEY_ID=""
gp env AWS_SECRET_ACCESS_KEY=""
gp env AWS_DEFAULT_REGION=us-east-1
```

### Check that the AWS CLI is working and you are the expected user

```sh
aws sts get-caller-identity
```

### Get the account and save it as a env var in Gitpod

```sh
aws sts get-caller-identity --query Account --output text
```

## Creating a Billing Alarm
There are 2 ways to set the billing alerts.

- Using Budget.
- Using Cloudwatch Alarm. In this case, you need to create an alarm on us-east-1 region (since it is the only region you can create an alarm). You can create up to 10 free cloudwatch alarm

Those 2 alarms will be helpful to identify if you are underspending/overspending.
The command used to create an alarm and sns subscription

The content of the files referenced can be found here [AWS Create Budget](https://docs.aws.amazon.com/cli/latest/reference/budgets/create-budget.html) [AWS SNS Subscribe](https://docs.aws.amazon.com/cli/latest/reference/sns/subscribe.html) 
```
aws budgets create-budget \
    --account-id $AWS_ACCOUNT_ID \
    --budget file://aws/json/budget.json \
    --notifications-with-subscribers file://aws/json/notifications-with-subscribers.json
```

```
aws sns subscribe \
    --topic-arn "arn:aws:sns:us-east-1:$ACCOUNT_ID:billing-alarm" \
    --protocol email \
    --notification-endpoint email@email.com
```

## Napkin Diagram & Architectural Diagram
![Cruddur Napkin Diagram!](/journal/img/Cruddur%20-%20Conceptual%20Diagram.jpeg)
![Cruddur Architectural Diagram!](/journal/img/diagram.png)




