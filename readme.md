# SAM VPC & Lambda Project

- This project uses the AWS Serverless Application Model (SAM) to deploy a complete, secure, and production-ready network architecture in AWS.
---

## Architecture Overview

This template will create the following resources:

- **1 VPC** (`my-vpc`)
- **1 Internet Gateway** (`my-igw`) to allow internet access
- **2 Public Subnets** (e.g., `public-subnet-a`, `public-subnet-b`) in different Availability Zones
- **1 Public Route Table** (`public-rt`) that directs internet traffic (`0.0.0.0/0`) to the Internet Gateway
- **2 Private Subnets** (e.g., `private-subnet-a`, `private-subnet-b`) in different Availability Zones
- **1 NAT Gateway** (`my-nat-gw`) placed in one of the public subnets
- **1 Private Route Table** (`private-rt`) used by both private subnets that directs all internet traffic (`0.0.0.0/0`) to the single NAT Gateway
- **1 Lambda Function** (`my-test-lambda`) with a "hello world" Python script
- **1 IAM Role** (`my-lambda-role`) for the Lambda function
- **1 Security Group** (`my-lambda-sg`) for the Lambda function

---

## Prerequisites

Before you begin, you must have the following installed and configured:

- **AWS CLI**: [Install the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)
- **AWS Account & Credentials**: Run `aws configure` to set up your credentials
- **AWS SAM CLI**: [Install the SAM CLI](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/serverless-sam-cli-install.html)
- **New Relic Account** and **New Relic API key**

---

## Deployment Steps

### Step 1: Set Up Your Project Files

Ensure your project has the following file structure. The `app.py` file must be inside a folder named `hello_world`.

```
/your-project-folder
    |
    +--- template.yaml
    |
    +--- /hello_world
          |
          +--- app.py
```

### Step 2: Add Your New Relic Secrets

- Open `template.yaml` and find the `MyLambdaFunction` resource. Update the Environment variables with your real keys:

```yaml
# ... (inside MyLambdaFunction Properties)
      Environment:
        Variables:
          NEW_RELIC_ACCOUNT_ID: "<Nr-Account-id>"  # <-- Enter NR-Accunt-ID
          NEW_RELIC_EXTENSION_LOG_LEVEL: "DEBUG"
          # ... (other variables) ...
          NEW_RELIC_LICENSE_KEY: "<License-key>"      # <-- Enter NR-API-key
          # ... (other variables) ...
```

### Step 3: Build the Application

The `sam build` command prepares your Lambda function's code for deployment.

```bash
sam build
```

This will create a `.aws-sam/build` directory with the packaged application.

### Step 4: Deploy the Stack

The `sam deploy --guided` command will walk you through deploying your application to the AWS cloud.

```bash
sam deploy --guided
```

You will be prompted for a few inputs:

- **Stack Name**: A name for your project, e.g., `my-vpc-stack`
- **AWS Region**: The region to deploy to, e.g., `us-west-1`
- **Confirm changes before deploy**: Press `y`
- **Allow SAM CLI IAM role creation**: Press `y`
- **Capabilities**: The console will show `[['CAPABILITY_IAM']]`. Just press Enter to accept
- **Save arguments to configuration file**: Press `y`

---

## How to Test Your Deployment

Once the deployment is **Successfully created/updated stack**, you can test that everything works.

1. Go to the **AWS Lambda Console**
2. Find your function, `my-test-lambda`, and click on it
3. Go to the **Test** tab
4. You don't need to change the event body. Just click the **Test** button
5. Check in NRUI to ensure your lambda function is instrumented


---

## Cleanup

When you are finished, you can delete the entire stack and all the resources it created using one command.

```bash
# Replace 'my-vpc-stack' with the Stack Name you provided
sam delete --stack-name my-vpc-stack
```

