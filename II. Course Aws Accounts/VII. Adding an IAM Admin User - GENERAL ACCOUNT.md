# IAM Admin User Creation and Secure Login Configuration

## Overview

This demo focuses on **securing an AWS account** by creating an **IAM admin user** to replace the use of the **account root user** for all day-to-day activities. Best practices emphasize that the root user should only be used for initial setup and rare, critical actions.

## Why Avoid the Root User?

- The root user:

  - Cannot be deleted or recreated.
  - Has unrestricted access.
  - Cannot be restricted by policies.

- Using the root user increases the security risk of your account.
- Best practice: **Create a separate IAM user with administrator privileges** and stop using the root user.

## Step-by-Step Guide: Creating and Securing the IAM Admin User

### 1. Access the IAM Console

- From the AWS Management Console, search for **IAM** and select the service.

### 2. Set an Account Alias (Optional, Recommended)

#### Why?

- Makes the login URL more readable and identifiable (e.g., for differentiating between "general" and "production" accounts).

#### How?

1. In the IAM dashboard, find the **IAM Sign-In URL** section.
2. Click **"Create"** or **"Edit"**.
3. Enter a globally unique alias (e.g., `yourname-general-account`).
4. Save it.

**Result:**
You now have a sign-in URL like:
`https://yourname-general-account.signin.aws.amazon.com/console`

### 3. Create the IAM Admin User

#### Steps:

1. In IAM, go to **Users** → click **Add users**.
2. Set **User name** to `iamadmin`.
3. **Select access type**:

   - **AWS Management Console access** → Enable.
   - **Console password**: Set a **custom password**.
   - Uncheck **"Require password reset"** on first login (optional).

#### Note:

You can use a password manager to generate and store the password securely.

### 4. Grant Administrator Permissions

#### Options:

- **Attach existing policies directly**.
- Search for and select **`AdministratorAccess`**.
- This policy grants **full access to all AWS resources**.

### 5. Review and Create the User

- Review the username, access settings, and permissions.
- Click **Create user**.

## Logging in as the IAM Admin User

1. Use the IAM login URL created earlier.
2. Enter:

   - **IAM username**: `iamadmin`
   - **Password**: The one you set during creation.

You are now logged in **as an IAM user**, not as the root user. You can verify this via the account dropdown (top right corner).

## Securing the IAM Admin User with MFA

Just like the root user, this admin user should be protected with **Multi-Factor Authentication (MFA)**.

### Steps:

1. Go to the **account dropdown** → **My security credentials**.
2. Scroll to **Multi-Factor Authentication (MFA)**.
3. Click **Assign MFA device**.
4. Choose **Virtual MFA device** → Continue.
5. Scan the **QR Code** using your authenticator app.
6. Enter **two consecutive codes** from the app to verify.
7. Click **Assign MFA**.

**Important:**
Use a **new entry** in your authenticator app for this IAM user.
Do **not** reuse the root user’s MFA profile.

### Testing MFA

1. **Sign out** from the AWS console.
2. Navigate back to your IAM login URL.
3. Login with:

   - Username: `iamadmin`
   - Password
   - **One-time code** from your MFA app

## Summary of Key Actions

- Created an **IAM admin user** with console access.
- Assigned the `AdministratorAccess` policy.
- Logged in using IAM-specific login URL.
- Enabled and configured **MFA** for the IAM user.
- Stopped using the **root account** for regular operations.

## Final State

- You now have **two MFA-protected users**:

  1. **Root User** (secured, not used for daily tasks)
  2. **IAM Admin** (secured, used for course and admin tasks)

To login:

- Go to: `https://your-alias.signin.aws.amazon.com/console`
- Use:

  - Username: `iamadmin`
  - Password
  - One-time MFA code

## Notes

- The IAM admin identity has **equal access** to the root user but can be fully managed and secured.
- MFA adds a critical layer of protection against unauthorized access.
- You'll repeat this process again for your **production AWS account** later in the course.
