# AWS-CloudFormation-Secure-S3-Bucket
This repository contains a CloudFormation YAML template and instructions to deploy secure S3 buckets (restricts public access, encrypted with AES-256).

Secure storage is an important part of a larger security program. The steps in this README ensure that:<br/>
✅ Public access is blocked (prevents accidental data exposure)  
✅ Encryption is enabled (protects data at rest)  
✅ CloudFormation is used to standardize deployment (infrastructure-as-code for repeatability/consistency)

Insecurely configured storage presents a critical vulnerability that has resulted in multiple high-profile security breaches. Publicly accessible cloud storage is an open door for your company's sensitive data. All S3 buckets that could contain sensitive/private/proprietary data should only be accessible by authorized individuals.

IaC establishes repeatable deployments that maintain the same configuration every time they're deployed.

This template shows how infrastructure-as-code (IaC) can be used to ensure security baselines at scale, support automated compliance validation, and reduce the risk of configuration drift--these are all elements of a mature vulnerability management program. 

To demonstrate true program maturity, your environment should include:
- **Continuous monitoring** of resource configurations and policy violations
- **Threat detection and vulnerability scanning** across the infrastructure
- **Change logging** to maintain audit trails
- **Security dashboard** for aggregating and visualizing security posture
- **Compliance reporting** tools for auditors and stakeholders

**Future Enhancements:**
This repository demonstrates one element of cloud security. I plan to create additional repositories showing how these concepts integrate:
- Automated vulnerability scanning → remediation workflow
- Security baseline enforcement with AWS Config/Security Hub
- Compliance dashboard aggregating multi-account security posture

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

## BONUS VERIFICATION -- REGION CHECK
For a complete overview of your bucket's configuration, you can also run this command:

```
C:\>aws s3api get-bucket-location --bucket securebucket-cybertrav
```

This shows the AWS region where your bucket is deployed. A cloud provider's "regions" are based on the locations of their various data centers.

You won't see an S3 bucket if it's unexpectedly deployed in a separate region. So this is a good troubleshooting step if you deploy your S3 bucket but cannot locate it with the AWS CLI commands we used to verify deployment.

From a security perspective, it's VERY important to know where your resources are deployed. If an S3 bucket is unexpectedly deployed in a separate region, it might not have the cloud storage controls you've established. Presenting a risk through exposed data or unexpected costs. **You can't protect something if you don't know it exists.**

<b>Example Scenario</b>

> Your security program protects resources in us-east-1 (data centers in Northern Virginia) and us-east-2 (Ohio). A cloud admin manually provisions an S3 bucket in us-west-1 (Northern California) without using your secure CloudFormation template. The bucket is publicly accessible and unencrypted.
> Unused AWS regions haven't been disabled yet and the security team is not aware of the new S3 bucket.
> An unauthorized user accesses the public bucket and downloads proprietary information that gave your business a competitive edge. After the breach, the company loses that advantage and profits decline.

Strive to know about your assets. Keep track of what you have, where it is, and how it's configured.

---

# SUMMARY OF STEPS<br/>
✅ **PREPARED** our tools by acquiring an AWS account and installing the AWS CLI<br/>
✅ **DEPLOYED** a CloudFormation template using the AWS CLI and a YAML file<br/>
✅ **VERIFIED** secure configuration using the AWS CLI -- this last step provided assurance that our storage was deployed with our intended secure configuration!
