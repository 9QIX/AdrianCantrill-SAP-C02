# AWS Organizations: Demo Setup – Part 1

## Objective

This lesson demonstrates how to create a practical **AWS Organizations** structure that will be used for the rest of the course. You'll:

- Convert a **General AWS Account** into a **Management Account**.
- **Invite a Production Account** into the organization.
- Learn to **navigate multi-account environments** using multiple browser sessions.

## Prerequisites

Before beginning:

- You must have two AWS accounts created:

  - **General Account** (used to create the organization)
  - **Production Account**

- You need access to:

  - The **email address or account ID** of the Production AWS Account.
  - Two separate browser sessions or a browser that supports **independent sessions** (like Firefox containers).

## Step-by-Step: Creating the AWS Organization

### 1. Log into the General Account

- Log in as the **IAM admin user** in the **General AWS Account**.
- Set your region (e.g., **US East - N. Virginia**).

### 2. Navigate to the AWS Organizations Console

- Use the AWS Console search bar:
  `Services → Organizations`
- Click **Create Organization**.

> This converts the General AWS Account into the **Management Account** for the AWS Organization.

### 3. Verify Email (If Prompted)

- AWS may send a verification email to the root user email address.
- Click the verification link to complete email validation.

## Step-by-Step: Invite the Production Account

### 4. Open a New Session for the Production Account

- Use another browser or a private/incognito session.
- Log into the **Production AWS Account** as the IAM admin user.

### 5. Copy the Production Account ID

- In the console, click on the account drop-down (top-right).
- Copy the **Account ID** to your clipboard.

### 6. Return to the General Account (Management Account)

- Navigate back to the **Organizations console**.
- Click **Add account → Invite account**.

### 7. Enter Production Account Information

- Paste the **Account ID** (or email) of the Production Account.
- Optional: Add a note (useful for shared teams).
- Click **Send Invitation**.

> **Note**: If you encounter an error about account limits, submit a support request to increase your **organization quota**.

## Step-by-Step: Accepting the Invite

### 8. In the Production Account Session

- Go to `Services → Organizations`.
- Navigate to **Invitations**.
- Locate the invite from the General Account.
- Click **Accept**.

### 9. Back in the General Account Session

- Refresh the Organizations dashboard.
- You should now see **two accounts**:

  - The **General Account** (Management Account)
  - The **Production Account** (Member Account)

## Visual Recap

```
AWS Organization:
├── Management Account: General Account
└── Member Account: Production Account
```

## Troubleshooting

| Issue                                    | Solution                                                                       |
| ---------------------------------------- | ------------------------------------------------------------------------------ |
| Invitation error about too many accounts | Open a support case to increase AWS Organization limits.                       |
| Session mix-up                           | Use clearly separated browser sessions (different browsers or incognito mode). |
| Email verification pending               | Check inbox/spam folder and verify before proceeding.                          |

## Conclusion

This concludes **Part 1** of the AWS Organization setup.

- You now have an organization with two accounts: one management, one member.
- Part 2 will build on this by creating a **new Development Account** directly within the organization.

> ✅ Take a short break if needed and continue with Part 2 when ready.
