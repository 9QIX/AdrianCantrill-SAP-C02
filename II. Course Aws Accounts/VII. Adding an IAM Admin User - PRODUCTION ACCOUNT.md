# Setting Up IAM Admin in the Production Account

## Overview

This demo continues from the previous lesson by guiding you through the **creation of an IAM Admin identity** inside the **Production AWS account**. The process mirrors the steps taken for the General account but includes critical differences such as managing multiple environments and ensuring unique configuration.

## Prerequisites

1. **Log in using the Root User** of the **Production AWS Account**.
2. Ensure the **AWS Region** is set to `us-east-1`.
3. Confirm the account label (top-right dropdown) says `Production`.

## Step-by-Step Setup

### 1. **Access IAM Console**

- Use the AWS search bar to find and open **IAM**.
- Dismiss any UI prompts or messages (e.g., new dashboard notifications).

### 2. **Create Account Alias for Production Account**

- Navigate to **Account Alias** section (right side).
- Click **Create**.
- Use a globally unique name that clearly indicates **production**.

  - E.g., `your-company-production`

- Save changes.
- Record this alias somewhere secure; it will be needed for IAM sign-in.

### 3. **Create the IAM Admin User**

- Go to **Users** → **Add Users**.
- Username: `iamadmin`
- Select **Password - AWS Management Console access**.
- Use a **custom password** (preferably via a password manager).
- **Uncheck** the box for "Require password reset".
- Click **Next**.

### 4. **Assign Admin Permissions**

- Select **Attach existing policies directly**.
- Locate and check `AdministratorAccess`.
- Click **Next**, review, and then click **Create User**.

### 5. **Verify IAM User Creation**

- Success message should appear.
- User `iamadmin` is now created in the **Production AWS account**.

### 6. **Sign In Using the IAM Admin User**

- Use the **Production sign-in URL** (not General).
- It should automatically populate with the account alias.
- Enter:

  - Username: `iamadmin`
  - Password: the one you set earlier

## Important: Environment Isolation

You now have **two AWS accounts**, each with its own IAM Admin:

| AWS Account | Root User     | IAM User       |
| ----------- | ------------- | -------------- |
| General     | Root User (A) | `iamadmin` (A) |
| Production  | Root User (B) | `iamadmin` (B) |

> These are **distinct identities**. Credentials do not carry across accounts.

## Step 7: Secure IAM Admin with MFA

1. Logged in as `iamadmin` in the **Production account**.
2. Click the user dropdown → **My Security Credentials**.
3. Scroll to **MFA** section → Click **Assign MFA Device**.
4. Choose **Virtual MFA Device**.
5. Click **Show QR Code** → Scan with your authenticator app.

   - Create a **new item** (do not reuse previous MFAs).

6. Enter **two consecutive MFA codes** from the app.
7. Click **Assign MFA**.
8. Confirmation of successful MFA assignment will be shown.

### Final Test: Secure Sign-In

1. **Sign out** of the console.
2. Visit the **Production AWS sign-in URL**.
3. Enter:

   - Username: `iamadmin`
   - Password: previously created
   - **MFA Code**: from authenticator app

4. Click **Submit**.

If successful, you now have **secured admin access** to the production account.

## Final Summary

By completing this lesson, you've established and secured IAM access to **two separate AWS accounts**:

### Total Identities Created

| AWS Account | IAM User   | MFA Enabled |
| ----------- | ---------- | ----------- |
| General     | `iamadmin` | Yes         |
| Production  | `iamadmin` | Yes         |

### Identity Matrix

| Account Type | Root User | IAM Admin (Password + MFA) |
| ------------ | --------- | -------------------------- |
| General      | Yes       | Yes                        |
| Production   | Yes       | Yes                        |

All four identities (two per account) are now protected with **username + password + one-time password (MFA)**.
