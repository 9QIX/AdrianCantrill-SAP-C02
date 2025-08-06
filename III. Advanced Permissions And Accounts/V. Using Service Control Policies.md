# AWS Organizations & Service Control Policies (SCPs) – Demo

## Overview

This demo lesson demonstrates how to configure and use **Service Control Policies (SCPs)** within an **AWS Organization**. The exercise involves creating a simple account hierarchy, applying SCPs, and observing their effect, particularly restricting access to S3 in the production account.

## Step 1: Organizational Structure Setup

1. **Accounts Used:**

   - **General Account:** Management account (root of the AWS Organization).
   - **Production Account:** Invited into the organization.
   - **Development Account:** Created within the organization.

2. **Creating Organizational Units (OUs):**

   - From the **AWS Organizations Console**, create two OUs:

     - `prod` (for the production account)
     - `dev` (for the development account)

3. **Moving Accounts into OUs:**

   - Move the production account into the `prod` OU.
   - Move the development account into the `dev` OU.
   - The general account remains in the root.

## Step 2: Create and Upload to an S3 Bucket in the Production Account

1. **Switch Role:**

   - Switch into the production AWS account using the `OrganizationAccountAccessRole`.

2. **Create an S3 Bucket:**

   - Use the S3 Console to create a bucket named something like `catpix-[random-number]`.
   - Choose the **US East (N. Virginia)** region.
   - Ensure the bucket name is globally unique.

3. **Upload an Object:**

   - Download a cat image (provided by the lesson).
   - Upload `samson.jpg` to the S3 bucket.

4. **Verify Access:**

   - Open the image in the S3 console.
   - This works because you're using a role with **AdministratorAccess** permissions.

## Step 3: Enable and Create Service Control Policies (SCPs)

1. **Enable SCPs in AWS Organizations:**

   - Navigate to **Policies** tab.
   - Enable **Service Control Policies**.

2. **Default SCP:**

   - `FullAWSAccess` is automatically attached to all accounts/org units.
   - This SCP **allows everything** (no restrictions).

## Step 4: Create a Custom SCP – Deny S3 Access

Download and open `deny_s3.json` from the course. Here's the content:

### `deny_s3.json`

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "*",
      "Resource": "*"
    },
    {
      "Effect": "Deny",
      "Action": "s3:*",
      "Resource": "*"
    }
  ]
}
```

### Line-by-Line Explanation:

- **`Version`**: Defines the policy language version (always use `2012-10-17` for AWS SCPs).

- **Allow Statement:**

  ```json
  {
    "Effect": "Allow",
    "Action": "*",
    "Resource": "*"
  }
  ```

  - Allows all actions (`*`) on all resources (`*`) – equivalent to full access.

- **Deny Statement:**

  ```json
  {
    "Effect": "Deny",
    "Action": "s3:*",
    "Resource": "*"
  }
  ```

  - Denies all S3-related actions for all resources.

> **Note**: In SCP evaluation, **explicit denies override any allows**.

## Step 5: Apply the Custom SCP to the Production OU

1. **Create Policy:**

   - Name: `AllowAllExceptS3`
   - Paste the JSON content above.

2. **Attach to Production OU:**

   - Go to `prod` OU > Policies tab.
   - Attach `AllowAllExceptS3`.
   - Detach the directly attached `FullAWSAccess` policy to ensure restriction takes effect.

## Step 6: Observe the Impact of SCPs

1. **Access from General Account:**

   - General account still has full access to S3 (not affected by the new SCP).

2. **Switch to Production Account:**

   - Try accessing S3 service from this account.
   - You get a **permissions error** (e.g., cannot list buckets).
   - This is because the SCP explicitly denies S3, regardless of the IAM role permissions.

3. **Access Other Services:**

   - You can still access services like EC2 since they're not denied in the SCP.
   - This confirms the **SCP restriction is only for S3**.

## Step 7: Restore Full Access (Optional Cleanup)

1. **Switch Back to General Account:**

   - Navigate to the production OU policies tab.

2. **Swap SCPs:**

   - Attach `FullAWSAccess`.
   - Detach `AllowAllExceptS3`.

3. **Verify Access in Production Account:**

   - Switch roles into production again.
   - You can now access the S3 bucket and view the object (`samson.jpg`).

4. **Delete Resources:**

   - Empty and delete the S3 bucket as part of cleanup.

## Summary of What You Learned

- How to use **Service Control Policies** in AWS Organizations.
- SCPs **override IAM permissions** for the accounts they are applied to.
- You created a custom SCP that allowed all actions **except S3**.
- Verified the impact by observing S3 access denial despite using an admin role.
- Restored access by re-attaching the default full access policy.

## Concepts Reinforced

- **AWS Organizations** allow centralized account management.
- **SCPs** are boundary policies applied at the organization or OU level.
- **IAM policies grant permissions**, while **SCPs set the maximum permission boundary**.
- Explicit `Deny` in an SCP takes precedence over all `Allow`s.
