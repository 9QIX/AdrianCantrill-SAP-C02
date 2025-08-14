# Cross Account Access to S3 - ACL - STAGE1

## Overview

This part of the demo series focuses on **granting cross-account access to Amazon S3 buckets using Access Control Lists (ACLs)**.
It is demonstrated between two AWS accounts:

- **Production Account**: Hosts the S3 buckets.
- **General Account**: Needs access to one of these buckets.

ACLs are **legacy S3 permissions mechanisms** and not recommended for new deployments (bucket policies are preferred).
However, understanding ACLs is important because they are still found in existing infrastructures.

## Resources Provided

1. **CloudFormation Template (Production Account Deployment)**
   [Production Account 1-Click Deployment](https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/create/review?templateURL=https://learn-cantrill-labs.s3.amazonaws.com/awscoursedemos/0039-aws-mixed-cross-account-s3/prod_bucketsandrole.yaml&stackName=buckets)

2. **Bucket Images**
   [Bucket Images ZIP](https://learn-cantrill-labs.s3.amazonaws.com/awscoursedemos/0039-aws-mixed-cross-account-s3/bucket_images.zip)

3. **Bucket Policy JSON**
   [Bucket Policy](https://learn-cantrill-labs.s3.amazonaws.com/awscoursedemos/0039-aws-mixed-cross-account-s3/petpics_policy.json)

## CloudFormation Template Explanation

```yaml
AWSTemplateFormatVersion: "2010-09-09" # Defines the CloudFormation template version
Description: >
  petpics1, 2 and 3
  IAM Role
Parameters:
  AccountToTrust: # Parameter to accept the trusted AWS Account ID
    Description: "A4LManagement (General) AccountID"
    Type: String

Resources:
  petpics1:
    Type: AWS::S3::Bucket # Creates S3 bucket petpics1
  petpics2:
    Type: AWS::S3::Bucket # Creates S3 bucket petpics2
  petpics3:
    Type: AWS::S3::Bucket # Creates S3 bucket petpics3

  CrossAccountS3Role:
    Type: "AWS::IAM::Role" # IAM Role for cross-account access
    Properties:
      AssumeRolePolicyDocument: # Trust policy for cross-account role assumption
        Version: 2012-10-17
        Statement:
          - Effect: Allow
            Principal:
              AWS:
                - !Join ["", ["arn:aws:iam::", !Ref AccountToTrust, ":root"]]
            Action:
              - "sts:AssumeRole"
      Path: /
      Policies:
        - PolicyName: root
          PolicyDocument:
            Version: 2012-10-17
            Statement:
              - Effect: Allow
                Action: "s3:ListAllMyBuckets"
                Resource: "arn:aws:s3:::*"
              - Effect: Allow
                Action: ["s3:ListBucket", "s3:GetBucketLocation"]
                Resource: !GetAtt petpics3.Arn
              - Effect: Allow
                Action: ["s3:GetObject", "s3:PutObject"]
                Resource: !Join ["", [!GetAtt petpics3.Arn, "/*"]]

Outputs:
  Bucket1URL:
    Value: !Join ["", ["https://us-east-1.console.aws.amazon.com/s3/buckets/", !Ref petpics1, "?region=us-east-1&tab=objects"]]
  Bucket2URL:
    Value: !Join ["", ["https://us-east-1.console.aws.amazon.com/s3/buckets/", !Ref petpics2, "?region=us-east-1&tab=objects"]]
  Bucket3URL:
    Value: !Join ["", ["https://us-east-1.console.aws.amazon.com/s3/buckets/", !Ref petpics3, "?region=us-east-1&tab=objects"]]
  Role:
    Value: !Ref CrossAccountS3Role
```

### Key Points:

- Creates **3 S3 buckets** (`petpics1`, `petpics2`, `petpics3`).
- Creates an **IAM Role** allowing a trusted AWS account (defined by `AccountToTrust`) to:

  - List all buckets.
  - List and get location of `petpics3`.
  - Get and put objects in `petpics3`.

- Outputs provide console URLs for the buckets and the created IAM role.

## Bucket Policy Explanation

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::REPLACEMEMANAGEMENTACCOUNTID:user/iamadmin"
      },
      "Action": ["s3:GetObject", "s3:PutObject", "s3:PutObjectAcl", "s3:ListBucket"],
      "Resource": ["arn:aws:s3:::REPLACEME_BUCKETNAME/*", "arn:aws:s3:::REPLACEME_BUCKETNAME"]
    }
  ]
}
```

### Key Points:

- Grants an **IAM user in another AWS account** (`iamadmin`) permission to:

  - Read objects (`GetObject`)
  - Upload objects (`PutObject`)
  - Modify object ACLs (`PutObjectAcl`)
  - List the bucket (`ListBucket`)

- `Resource` specifies both:

  - The bucket itself.
  - All objects within it.

## Step-by-Step Learning from the Demo

### 1. Initial Setup Verification

- After deploying in the **production account**, verify all three S3 buckets (`petpics1`, `petpics2`, `petpics3`) are accessible when logged in as the **OrganizationAccountAccessRole** (admin permissions).

### 2. Testing Cross-Account Access Without Permissions

- Switching to the **general account** and accessing bucket URLs results in **"Insufficient Permissions"** because:

  - No trust relationship exists.
  - Buckets do not allow access to external account identities.

### 3. Introducing ACLs (Legacy Method)

- ACLs identify accounts using **Canonical User IDs** (not account IDs or IAM ARNs).
- Retrieve the **Canonical User ID** for the general account from **Security Credentials**.

### 4. Granting Access via ACL

- Switch back to the **production account**.
- Open `petpics1` → Permissions → Access Control List → **Add Grantee**:

  - Enter the **general account’s canonical ID**.
  - Grant **all permissions** (read/write/list).

- This allows the general account to upload objects.

### 5. Uploading from General Account

- From the general account, upload `Ginny.jpeg` to `petpics1`.
- **Important Security Concept**:

  - **Uploader’s account owns the object**, not the bucket owner.
  - Even though the production account owns the bucket, it **cannot access** `Ginny.jpeg` without additional permissions.

### 6. Cleaning Up

- Delete `Ginny.jpeg` from the **general account** (object owner).
- This ensures the bucket can be deleted later (buckets must be empty before deletion).

## Key Takeaways

1. **ACLs**:

   - Are a **legacy** permission model in S3.
   - Use **Canonical User IDs** for cross-account access.
   - Still exist for backwards compatibility.

2. **Ownership Rules**:

   - Bucket owner does not automatically own uploaded objects.
   - Object ownership is determined by the account that uploads it.

3. **Best Practice**:

   - Use **bucket policies** for cross-account permissions.
   - Reserve ACL usage for niche, legacy scenarios.
