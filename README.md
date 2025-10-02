# AWS-CloudFormation-Secure-S3-Bucket
CloudFormation YAML template and instructions to deploy secure S3 buckets (restricts public access, encrypted with AES-256)

# Initial Set-up
You'll need your own AWS account and the AWS CLI. You can use the AWS Management Console (GUI) to accomplish the same actions, but the AWS CLI will allow you to deploy resources in a way that is quick, efficient, and (eventually) intuitive.

If you have an AWS account and you've configured the AWS CLI, you can proceed to Step 1.

## Create an AWS Account
Follow the instructions here: https://aws.amazon.com/resources/create-account/

## Configure AWS CLI on your device.
Go here for instructions: https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html

# Deployment Instructions

## Step 1 - DOWNLOAD THE TEMPLATE
You'll need to download the CloudFormation template. Download the 'secure-bucket.yml' file from this repository.

## Step 2 - DEPLOY THE TEMPLATE
You'll need to create a stack in your AWS tenant that serves as a template for creating S3 bucket with the configuration described in the key-value pairs contained in the YAML file. Use the command below in your Windows Command Prompt or Mac Terminal:
<br/>
<br/>
```C:\>aws cloudformation deploy --template-file C:\File-Path\secure-bucket.yml --stack-name my-secure-bucket-stack --capabilities CAPABILITY_NAMED_IAM```
<br/>
<br/>
NOTE: Replace 'C:\File-Path\secure-bucket.yml' to reflect the file path where the file is saved.

## Step 3 - CREATE A SECURE S3 BUCKET USING THE TEMPLATE
Enter the command below in your Windows Command Prompt / Mac Terminal:
```aws s3 mb s3://<bucket-name> [--options]```


VERIFY
### Verify that the Bucket was Deployed
Use the AWS CLI to enter the following command:
<br/>
<br/>
```C:\>aws s3api list-buckets```
<br/>
<br/>
You should see something like the output shown below.

```{                                                                               ```<br/>
```    "Buckets": [                                                                  ```<br/>
```        {                                                                         ```<br/>
```            "Name": "cf-templates-27gzb5pdre1g-us-west-1",                        ```<br/>
```            "CreationDate": "2025-09-28T01:56:41+00:00"                           ```<br/>
```        },                                                                        ```<br/>
```        {                                                                         ```<br/>
```            "Name": "securebucket-cybertrav",                                     ```<br/>
```            "CreationDate": "2025-09-28T22:30:08+00:00"                           ```<br/>
```        }                                                                         ```<br/>
```    ],                                                                            ```<br/>
```    "Owner": {                                                                    ```<br/>
```        "ID": "9999999999999999999999999999999999999999999999999999999999999999"```<br/>
```    },                                                                            ```<br/>
```    "Prefix": null                                                                ```<br/>
```}                                                                               ```<br/>

### Verify that the Bucket is Securely Configured
