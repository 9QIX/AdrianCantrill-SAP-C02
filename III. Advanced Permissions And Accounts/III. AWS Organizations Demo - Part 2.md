# AWS Organizations: Demo Setup – Part 2

## Objective

In this lesson, you will:

- Set up cross-account access via **IAM role switching** between AWS accounts in an organization.
- Manually create a role for the invited **Production Account**.
- Automatically create a role for a newly created **Development Account**.
- Finalize the three-account architecture:

  - Management Account (General)
  - Production Account (invited)
  - Development Account (created)

## Role Switching Overview

In AWS Organizations:

- **Invited accounts** (like Production) require you to **manually create an IAM role** to allow role switching.
- **Created accounts** (like Development) have that role **automatically created** by AWS.

Role switching allows users in one account to assume a role in another, enabling secure and centralized identity management.

## Step-by-Step: Create Role in the Production Account

### 1. Open IAM Console in Production Account

- Use the **browser session** where you're logged into the **Production Account**.
- Navigate to: `Services → IAM → Roles → Create Role`.

### 2. Configure the Role Trust

- **Trusted Entity Type**: Another AWS Account
- **Account ID**: Use the **Management Account’s** ID (from the General Account).

This makes the Management Account trusted to assume this role.

### 3. Assign Permissions

- Attach the policy: `AdministratorAccess`.

This grants full admin privileges to anyone assuming the role.

### 4. Name the Role

Use the **standard AWS role name**:

```
OrganizationAccountAccessRole
```

- Uppercase `OAA` and `R`.
- Use the **US spelling**: `Organization` (with Z, not S).

This name is consistent with AWS’s default for organization-created accounts.

### 5. Verify Trust Relationship

After creating the role:

- Navigate to the role's **Trust Relationships** tab.
- Confirm the **trusted account ID** matches your **General/Management Account**.

This setup enables cross-account access via role switching.

## Step-by-Step: Switch Role into Production Account

### 1. Go Back to the General Account Session

- Ensure you’re logged in as the **IAM admin** in the **General Account** (Management Account).
- Copy the **Production Account ID**.

### 2. Initiate Role Switch

- From the AWS Console, click the **account dropdown** → `Switch Role`.

### 3. Fill in Role Switch Details

| Field        | Value                             |
| ------------ | --------------------------------- |
| Account ID   | Production Account ID             |
| Role Name    | OrganizationAccountAccessRole     |
| Display Name | `prod`                            |
| Color        | `Red` (indicates high importance) |

Click **Switch Role**.

You will now be accessing the Production Account via **assumed role**, which AWS implements using the **AssumeRole API**.

### 4. Role History & Shortcuts

- A shortcut to the switch role will now appear in the **console UI** under **role history**.
- This is **browser-specific** (not stored in AWS).
- Clicking the shortcut again allows seamless role switching in future sessions.

## Create the Development Account

### 1. Return to the General Account (Management Account)

- Close the Production Account session (no longer needed).
- Go to the **AWS Organizations Console**.
- Click **Add Account → Create Account**.

### 2. Fill in New Account Details

| Field         | Value                                                          |
| ------------- | -------------------------------------------------------------- |
| Account Name  | `development` (follow consistent naming structure)             |
| Email Address | Use a **unique email**. Tip: use Gmail's `+` aliasing trick.   |
| IAM Role Name | `OrganizationAccountAccessRole` (same as before, default used) |

Click **Create**.

AWS now creates this new account **within the organization** and **automatically creates** the standard IAM role.

If you receive an error about account limits, contact AWS support to increase your **organization account quota**.

### 3. After Creation

- Wait a few minutes.
- Refresh the Organizations Console to see the **Development Account** with a new **account ID**.

## Step-by-Step: Switch Role into Development Account

### 1. Copy the Development Account ID

- Once available, copy the **Development Account ID** to clipboard.

### 2. Initiate Role Switch Again

- From the Management Account session, click **account dropdown → Switch Role**.

| Field        | Value                                 |
| ------------ | ------------------------------------- |
| Account ID   | Development Account ID                |
| Role Name    | OrganizationAccountAccessRole         |
| Display Name | `dev`                                 |
| Color        | `Yellow` (used for non-prod accounts) |

Click **Switch Role**.

### 3. Confirm Switch

- You'll now be in the **Development Account**, using **temporary credentials** issued by the role.
- A new shortcut for `dev` will appear under **role history**.
- You can now easily switch between `general`, `prod`, and `dev` without repeating setup steps.

## Verify IAM Role in Development Account

- Go to `Services → IAM → Roles`.
- Locate `OrganizationAccountAccessRole`.
- Check **Trust Relationships**.

You will see the **General Account** (Management Account) ID listed as the trusted principal. AWS configured this **automatically** when the account was created.

## Final AWS Account Structure

| Account Type     | Account Name | Role Created By | IAM Role Present | Access Method |
| ---------------- | ------------ | --------------- | ---------------- | ------------- |
| Management       | General      | You             | IAM Admin User   | Direct Login  |
| Member (Invited) | Production   | You (manually)  | Yes              | Switch Role   |
| Member (Created) | Development  | AWS (automatic) | Yes              | Switch Role   |

## Summary

By the end of this demo lesson, you have:

- Created an **IAM role** manually in the Production Account for role switching.
- Switched roles from the General Account into the Production Account.
- Created a **Development Account** within the AWS Organization.
- Verified AWS automatically creates the role for cross-account access when accounts are created in-org.
- Set up **shortcuts** for seamless switching between all accounts.

This three-account architecture (General, Production, Development) forms the foundation for the rest of the course.
