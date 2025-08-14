# Cross Account Access to S3 - ROLE - STAGE4

This section from the Learn Cantrill course covers **cross-account S3 bucket access** using **IAM Roles** rather than ACLs or direct bucket policies.
The demo walks through setting up three buckets and a cross-account role, then using that role to interact with the bucket from another AWS account.

## Key Resources

- **1-Click Deployment (Production Account)**
  [CloudFormation Template Link](https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/create/review?templateURL=https://learn-cantrill-labs.s3.amazonaws.com/awscoursedemos/0039-aws-mixed-cross-account-s3/prod_bucketsandrole.yaml&stackName=buckets)
- **Bucket Images ZIP**
  [Download Images](https://learn-cantrill-labs.s3.amazonaws.com/awscoursedemos/0039-aws-mixed-cross-account-s3/bucket_images.zip)
- **Bucket Policy JSON**
  [View Policy](https://learn-cantrill-labs.s3.amazonaws.com/awscoursedemos/0039-aws-mixed-cross-account-s3/petpics_policy.json)

## CloudFormation Template

```yaml
AWSTemplateFormatVersion: "2010-09-09"
Description: >
  petpics1, 2 and 3
  IAM Role
Parameters:
  AccountToTrust:
    Description: "A4LManagement (General) AccountID"
    Type: String
Resources:
  petpics1:
    Type: AWS::S3::Bucket
  petpics2:
    Type: AWS::S3::Bucket
  petpics3:
    Type: AWS::S3::Bucket
  CrossAccountS3Role:
    Type: "AWS::IAM::Role"
    Properties:
      AssumeRolePolicyDocument:
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

### Template Explanation

- **AWSTemplateFormatVersion & Description**
  Standard CloudFormation metadata.

- **Parameters**

  - `AccountToTrust`: The AWS account ID (General account) that will assume the role.

- **Resources**

  - `petpics1`, `petpics2`, `petpics3`: S3 buckets.
  - `CrossAccountS3Role`: IAM Role granting the trusted account permission to assume it and interact with `petpics3`.

    - **AssumeRolePolicyDocument**: Allows only the `AccountToTrust` root to assume this role.
    - **Policies**: Grants:

      1. List all buckets.
      2. List/Get location of `petpics3`.
      3. Get/Put objects in `petpics3`.

- **Outputs**
  Provides console URLs for each bucket and the IAM role name.

## Bucket Policy

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

### Policy Explanation

- **Effect: Allow** — Grants permission.
- **Principal** — The IAM user in the management account who gets access.
- **Actions**:

  - `s3:GetObject` — Read objects.
  - `s3:PutObject` — Upload objects.
  - `s3:PutObjectAcl` — Modify object ACLs.
  - `s3:ListBucket` — View object listings in the bucket.

- **Resource** — Grants access to both the bucket itself and all objects within it.

## Demo Workflow Summary

### 1. Initial Setup

- **Attempt access** to `PetPics3` from General account IAM Admin user → **Access Denied** (no ACL, no bucket policy).
- **Switch role** into the Production account using:

  - Production AWS Account ID.
  - Role name created during CloudFormation deployment.

### 2. Using Cross-Account Role

- In Production account via role, open `PetPics3`.
- **Upload `maxi.jpg`** into `PetPics3`.
- The **production account** owns both bucket and object because the upload was done using a role in that account.

### 3. Switching Back

- Switching to General account → still no access to `PetPics3` unless assuming the role again.
- Switching to Production account's admin role → full access.

## Key Concepts from the Demo

![alt text](image-7.png)

1. **Access Control Methods**

   - **ACLs (Legacy)** — Require canonical account IDs, objects may be owned by uploader.
   - **Bucket Policies** — Easier to manage, but objects can still be owned by external accounts.
   - **Cross-Account IAM Roles** — The cleanest method. The account with the bucket also owns uploaded objects.

2. **Ownership**

   - Role assumption into bucket-owning account ensures that **both bucket and uploaded objects are owned by the same AWS account**.

3. **Best Practice**

   - Use cross-account IAM roles for scalable, tidy management and guaranteed ownership control.

## Cleanup Procedure

1. Delete uploaded objects (`maxi.jpg`) from `PetPics3`.
2. Ensure all buckets are empty (`PetPics1`, `PetPics2`, `PetPics3`).
3. Delete the CloudFormation stack in Production account.
4. Switch back to the General account.
