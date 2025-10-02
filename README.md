# AWS-CloudFormation-Secure-S3-Bucket
This repository contains a CloudFormation YAML template and instructions to deploy secure S3 buckets (restricts public access, encrypted with AES-256).
<br/>
### Attribution - YAML Template
The YAML template was copied from AJ Yawn's "GRC Engineering for AWS" repository.
<br/>
User Name: [ajy0127](https://github.com/ajy0127)
<br/>
Repository: https://github.com/ajy0127/thegrcengineeringbook
<br/>
File in Original Repository: [secure-bucket.yml](https://github.com/ajy0127/thegrcengineeringbook/blob/master/chapter-5/secure-bucket.yml)
<br/>
<br/>
*The YAML file has been modified--it now includes comments explaining elements of the template. 
<br/>
<br/>
# Initial Set-up
```diff
- Read this section before proceeding.
```
You'll need your own AWS account and the AWS CLI. You can use the AWS Management Console (GUI) in a web browser to accomplish the same actions, but the AWS CLI will allow you to deploy resources in a way that is quick, efficient, and (eventually) intuitive.

If you have an AWS account and you've configured the AWS CLI, you can proceed to Step 1.

## Create an AWS Account
Follow the instructions here: https://aws.amazon.com/resources/create-account/

## Configure AWS CLI on your device.
Go here for instructions: https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html
<br/>
<br/>
# Deployment Instructions
## Step 1 - OBTAIN TEMPLATE
Download the [secure-bucket.yml](https://github.com/TravDunc/AWS-CloudFormation-Secure-S3-Bucket/blob/main/secure-bucket.yml) file from this repository.
This file will be used to let AWS know how you want to configure your S3 bucket.
## Step 2 - DEPLOY A SECURE S3 BUCKET
Each line of the YAML file contains information (in key-value pairs) that tell AWS how the resource will be configured and deployed.
<br/>
<br/>
Before proceeding, make sure you have properly [configured your AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html). If you've configured your tech stack correctly, you should be able to use the AWS CLI to create an S3 bucket from your YAML template file.
<br/>
<br/>
In your Windows Command Prompt or Mac Terminal, enter the command below. NOTE: Replace "C:\File-Path" in the command above with the folder path where you've stored the YAML template:
<br/>

```diff
C:\>aws cloudformation deploy --template-file C:\File-Path\secure-bucket.yml --stack-name my-secure-bucket-stack --capabilities CAPABILITY_NAMED_IAM
```

<br/>

## Step 3 - VERIFY
<br/>
After we've deployed a resource, we should have a mechanism in place to ensure that it was deployed properly. AWS has services that can be used to detect and remediate non-compliant configurations. However, for the purposes of this lab, we will  just use manual AWS CLI commands. 
### Verify that the Bucket was Deployed
Use the AWS CLI to enter the following command:
<br/>
<br/>
```diff
C:\>aws s3api list-buckets
```
<br/>
<br/>
You should see something like the output shown below.

```diff
{
    "Buckets": [
        {
            "Name": "cf-templates-27gzb5pdre1g-us-west-1",
            "CreationDate": "2025-09-28T01:56:41+00:00"
        },
        {
            "Name": "securebucket-cybertrav",
            "CreationDate": "2025-09-28T22:30:08+00:00"
        }
    ],
    "Owner": {
        "ID": "9999999999999999999999999999999999999999999999999999999999999999"
    },
    "Prefix": null
}
```

### Verify that the Bucket is Securely Configured
