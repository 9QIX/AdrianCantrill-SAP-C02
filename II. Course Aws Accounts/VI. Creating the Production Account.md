# Creating and Configuring a Production AWS Account

## Overview

This lesson focuses on **creating a second AWS account** (the _production account_) to simulate a **multi-account environment** using **AWS Organizations**. You'll follow similar steps used when setting up the general account.

> This is a _do-it-yourself_ lesson to reinforce your understanding of AWS account setup.

## Why a Second AWS Account?

- To simulate **real-world multi-account AWS environments**
- Used in demos requiring **cross-account access** (e.g., cross-account S3)
- Enables separation between general-purpose and production workloads

## General vs. Production Account Usage

| Account                | Use Case                                            |
| ---------------------- | --------------------------------------------------- |
| **General Account**    | Single-account demos, basic configuration           |
| **Production Account** | Multi-account demos (e.g., S3 cross-account access) |

## Step-by-Step Guide: Creating the Production Account

### 1. Use a Unique Email Address

AWS requires a **unique email** per account. You can reuse your Gmail inbox using the **Gmail Plus Trick**:

```plaintext
your.email+production@gmail.com
```

This lets you receive emails in the same inbox while AWS treats it as a separate email.

### 2. Follow AWS Sign-Up Process

Repeat the same steps as with the general account:

- Use same name, address, and billing info
- Choose a new **Account Name**, e.g., `yourname-production`
- Choose the same **Support Plan** as the general account

### 3. Secure the Production Account

#### a. Enable MFA on the Root User

- Use a **separate MFA entry** in your app (e.g., Google Authenticator, 1Password)
- Don’t reuse the MFA setup from the general account

#### b. Test Login

- Log out and log in again to verify MFA works

### 4. Configure Billing Preferences

Follow these steps (just like in the general account):

1. Go to the **Billing Console**
2. Open **Billing Preferences**
3. Check all the following:

   - [x] Receive PDF invoice by email
   - [x] Receive Free Tier usage alerts
   - [x] Receive billing alerts

4. Enter your **notification email**
5. Click **Save Preferences**

### 5. Create a Cost Budget

Set up a budget to monitor spending:

1. Go to **Budgets**
2. Click **Create Budget**
3. Choose **Cost Budget**
4. Set:

   - Budget amount (e.g., `$10`)
   - Alert threshold (e.g., 50%)
   - Notification email

5. Review and **Create**

### 6. IAM and Account Settings

#### a. Enable IAM Access to Billing

- Navigate to **Account Settings**
- Enable the option:
  **"IAM Access to Billing Information"**

#### b. (Optional) Add Contact Details

If you did this in the general account, repeat it here:

- **Account Contact**
- **Security Contact**
- **Billing Contact**

## Summary Checklist

- [ ] Unique email for production account
- [ ] AWS account created and signed in
- [ ] MFA configured separately
- [ ] Billing preferences set
- [ ] Budget created
- [ ] IAM billing access enabled
- [ ] Optional contact info added

## Key Takeaway

You’re now responsible for creating and configuring the production AWS account **on your own**. This exercise ensures you're capable of provisioning a new AWS account from scratch—an essential skill for real-world cloud architecture.

> Proceed with the setup and join the next lesson after completing these steps.
