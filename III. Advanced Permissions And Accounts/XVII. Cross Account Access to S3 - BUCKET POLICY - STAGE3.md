# Cross Account Access to S3 - BUCKET POLICY - STAGE4

## Overview

This lesson demonstrates how to grant **cross-account access** to an S3 bucket using a **bucket policy**.  
The setup involves:

- A **Production AWS Account** (owns the S3 buckets).
- A **General AWS Account** (needs access to specific bucket(s)).
- AWS S3 **bucket policy** to allow controlled access.
- Understanding object ownership implications in cross-account setups.

## Resources Used

- **1-Click Deployment – Production Account**  
  [CloudFormation Template Deployment](https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/create/review?templateURL=https://learn-cantrill-labs.s3.amazonaws.com/awscoursedemos/0039-aws-mixed-cross-account-s3/prod_bucketsandrole.yaml&stackName=buckets)
- **Bucket Images**  
  [Download Images](https://learn-cantrill-labs.s3.amazonaws.com/awscoursedemos/0039-aws-mixed-cross-account-s3/bucket_images.zip)
- **Bucket Policy Template**  
  [Download Policy JSON](https://learn-cantrill-labs.s3.amazonaws.com/awscoursedemos/0039-aws-mixed-cross-account-s3/petpics_policy.json)

## CloudFormation Template Explanation

### Template

```yaml
AWSTemplateFormatVersion: "2010-09-09" # Defines the CloudFormation template version.
Description: > # Human-readable description of the stack.
  petpics1, 2 and 3
  IAM Role

Parameters:
  AccountToTrust:
    Description: "A4LManagement (General) AccountID" # Parameter for General AWS Account ID.
    Type: String # Must be a string value.

Resources:
  petpics1:
    Type: AWS::S3::Bucket # Creates S3 bucket petpics1.
  petpics2:
    Type: AWS::S3::Bucket # Creates S3 bucket petpics2.
  petpics3:
    Type: AWS::S3::Bucket # Creates S3 bucket petpics3.

  CrossAccountS3Role:
    Type: "AWS::IAM::Role" # IAM Role for cross-account access.
    Properties:
      AssumeRolePolicyDocument:
        Version: 2012-10-17
        Statement:
          - Effect: Allow
            Principal:
              AWS:
                - !Join ["", ["arn:aws:iam::", !Ref AccountToTrust, ":root"]] # Trusts the General AWS Account root.
            Action:
              - "sts:AssumeRole" # Allows the trusted account to assume this role.

      Path: /
      Policies:
        - PolicyName: root
          PolicyDocument:
            Version: 2012-10-17
            Statement:
              - Effect: Allow
                Action: "s3:ListAllMyBuckets" # Can list all buckets.
                Resource: "arn:aws:s3:::*"
              - Effect: Allow
                Action: ["s3:ListBucket", "s3:GetBucketLocation"] # Can view bucket details for petpics3.
                Resource: !GetAtt petpics3.Arn
              - Effect: Allow
                Action: ["s3:GetObject", "s3:PutObject"] # Can read/write objects in petpics3.
                Resource: !Join ["", [!GetAtt petpics3.Arn, "/*"]]

Outputs:
  Bucket1URL: # Console link for petpics1.
    Value: !Join ["", ["https://us-east-1.console.aws.amazon.com/s3/buckets/", !Ref petpics1, "?region=us-east-1&tab=objects"]]
  Bucket2URL: # Console link for petpics2.
    Value: !Join ["", ["https://us-east-1.console.aws.amazon.com/s3/buckets/", !Ref petpics2, "?region=us-east-1&tab=objects"]]
  Bucket3URL: # Console link for petpics3.
    Value: !Join ["", ["https://us-east-1.console.aws.amazon.com/s3/buckets/", !Ref petpics3, "?region=us-east-1&tab=objects"]]
  Role: # IAM Role output.
    Value: !Ref CrossAccountS3Role
```

## Bucket Policy Explanation

### Policy JSON

```json
"Version": "2012-10-17" // AWS policy language version.
"Statement": [
  {
    "Effect": "Allow", // This policy allows actions.
    "Principal": {
      "AWS": "arn:aws:iam::REPLACEMEMANAGEMENTACCOUNTID:user/iamadmin" // IAM User in the General AWS Account.
    },
    "Action": [
      "s3:GetObject",     // Read objects.
      "s3:PutObject",     // Upload objects.
      "s3:PutObjectAcl",  // Modify object ACLs.
      "s3:ListBucket"     // List bucket contents.
    ],
    "Resource": [
      "arn:aws:s3:::REPLACEME_BUCKETNAME/*", // All objects in the bucket.
      "arn:aws:s3:::REPLACEME_BUCKETNAME"    // The bucket itself.
    ]
  }
]
```

## Step-by-Step

1. **Initial Access Attempt**

   - From the General AWS Account, tried to open `petpics2` bucket — failed (no trust).

2. **Switch to Production Account**

   - Edited bucket policy in Production account to grant General account's IAM user access.

3. **Replace Placeholders in Policy**

   - Replace `REPLACEMEMANAGEMENTACCOUNTID` with General AWS Account ID.
   - Replace `REPLACEME_BUCKETNAME` with actual bucket ARN.

4. **Testing Access**

   - Switched back to General account, uploaded `Hermione.jpeg` to `petpics2` — success.

5. **Object Ownership Lesson**

   - Even if bucket is owned by Production account, uploaded object is owned by the uploading account (General).

6. **ACL & Ownership Change**

   - Optionally, configure bucket ownership preference to **Bucket Owner Preferred**.

7. **Delete Permission Issue**

   - Initially, General account user couldn’t delete object (policy didn’t allow `s3:DeleteObject`).

8. **Grant Full S3 Actions**

   - Updated action to `"s3:*"` for full access — deletion worked.

## Key Takeaways

- **Bucket Policy** is required for cross-account access when no trust relationship exists.
- Must explicitly list **account ID** and **bucket ARN** in the policy.
- Object ownership defaults to uploader, unless ownership settings are adjusted.
- Fine-grained S3 permissions (`GetObject`, `PutObject`, `DeleteObject`, etc.) are critical to control access levels.
- For broad access, use `"s3:*"` but understand security implications.
