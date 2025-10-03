# AWS-CloudFormation-Secure-S3-Bucket
This repository contains a CloudFormation YAML template and instructions to deploy secure S3 buckets (restricts public access, encrypted with AES-256).

Secure storage is an important part of a larger security program. The steps in this README ensure that:<br/>
✅ Public access blocked (prevents accidental data exposure)  
✅ Encryption enabled (protects data at rest)  
✅ CloudFormation deployment (infrastructure-as-code for repeatability/consistency)

Insecurely configured storage presents a critical vulnerability that has resulted in multiple high-profile security breaches. Publicly accessible cloud storage is an open door for your company's sensitive data. All S3 buckets that could contain sensitive/private/proprietary data should only be accessible by authorized individuals.

IaC establishes repeatable deployments that maintain the same configuration every time they're deployed.

This template shows how infrastructure-as-code (IaC) can be used to ensure security baselines at scale, support automated compliance validation, and reduce the risk of configuration drift--these are all elemnts of a mature vulnerability management program. 

To demonstrate true program maturity, your environment should continuously monitor resource configurations, identify policy violations, detect threats, scan for vulnerabilities, log any changes in the environment, have tools for monitoring and aggregating security info (i.e., a security dashboard), and tools for providing reports/information to auditors.

I may create more repos in the future that demonstrate how one or two of these concepts support each other.

---

# Step 1 - Initial Set-up

```diff
- Read this section before proceeding.
```

You'll need your own AWS account and the AWS CLI. You can use the AWS Management Console (GUI) in a web browser to accomplish the same actions, but the AWS CLI will allow you to deploy resources in a way that is quick, efficient, and (eventually) intuitive.

## Create an AWS Account
Follow the instructions here: https://aws.amazon.com/resources/create-account/

## Configure AWS CLI on your device.
Go here for instructions: https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html

Once you've signed up for an AWS account and configured the AWS CLI, you can proceed to Step 2.

# Step 2 - DOWNLOAD TEMPLATE
Download the [secure-bucket.yml](https://github.com/TravDunc/AWS-CloudFormation-Secure-S3-Bucket/blob/main/secure-bucket.yml) file from this repository.
The information in this file will let AWS know how you want to configure your S3 bucket.

# Step 3 - DEPLOY SECURE S3 BUCKET
Each line of the YAML file contains information (in key-value pairs) that tell AWS how the resource will be configured and deployed.

Before proceeding, make sure you have properly [configured your AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html). If you've configured your tech stack correctly, you should be able to use the AWS CLI to create an S3 bucket from your YAML template file.

In your Windows Command Prompt or Mac Terminal, enter the command below.
**Note 01:** Replace `C:\File-Path` with the folder path where you've stored the YAML template.
**Note 02:** **You will need to choose a unique name for your S3 bucket!** Replace `securebucket-cybertrav` with your own bucket name. **Remember that S3 bucket names must be GLOBALLY unique--you cannot create a bucket if the name is the same as an existing S3 bucket.**

```diff
C:\>aws cloudformation deploy --template-file C:\File-Path\secure-bucket.yml --stack-name my-secure-bucket-stack --capabilities CAPABILITY_NAMED_IAM
```

<br/>
<br/>

# Step 4 - VERIFY DEPLOYMENT
After deploying a resource, we should have a mechanism in place to ensure that it was deployed properly. AWS has services that can be used to detect and remediate non-compliant configurations. However, for the purposes of this lab, we will just use manual AWS CLI commands. 

## Verify that the S3 Bucket was Deployed

Use the AWS CLI to enter the following command:

```diff
C:\>aws s3api list-buckets
```

Example Output:
```json
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

Look for your bucket name (e.g., `securebucket-cybertrav`) in the output to confirm it was created.

## Verify Public Access Block Configuration
To verify that public access is properly blocked (as configured in the template), run:

```
C:\>aws s3api get-public-access-block --bucket securebucket-cybertrav
```

Expected output:
```json
{
  "PublicAccessBlockConfiguration": {
    "BlockPublicAcls": true,
    "IgnorePublicAcls": true,
    "BlockPublicPolicy": true,
    "RestrictPublicBuckets": true
  }
}
```

**What to verify:**
- All four settings should be `true`
- This confirms the bucket is protected from public access

## Verify Bucket Encryption
To verify that server-side encryption is enabled with AES-256 (as configured in the template), run:

```
C:\>aws s3api get-bucket-encryption --bucket securebucket-cybertrav
```

Expected output:
```json
{
  "ServerSideEncryptionConfiguration": {
    "Rules": [
      {
        "ApplyServerSideEncryptionByDefault": {
          "SSEAlgorithm": "AES256"
        },
        "BucketKeyEnabled": false
      }
    ]
  }
}
```

**What to verify:**
- `SSEAlgorithm` should be `"AES256"`
- This confirms all objects uploaded to the bucket will be encrypted at rest

## Comprehensive Bucket Configuration Check
For a complete overview of your bucket's configuration, you can also run:

```
C:\>aws s3api get-bucket-location --bucket securebucket-cybertrav
```

This shows the AWS region where your bucket is deployed.

---

**SUMMARY OF STEPS**
✅ Installed necessary tools, acquired cloud account
✅ Used AWS CLI to **deploy** a CloudFormation template from a YAML template
✅ Used AWS CLI to **verify** secure configuration -- this provided assurance that the storage was deployed with the correct security attributes!
