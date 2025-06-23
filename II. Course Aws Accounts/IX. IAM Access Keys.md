# Understanding IAM Access Keys

## Overview

At this stage of your AWS learning journey, you should have:

- Set up multiple AWS accounts (e.g., general and production).
- Configured account-level security.
- Created IAM admin users.
- Enabled Multi-Factor Authentication (MFA).
- Set billing alerts.

Up until now, all interactions with AWS were done via the **Console UI**. This lesson introduces a new way to access AWS: using the **Command Line Interface (CLI)** with **Access Keys**, which are long-term credentials associated with IAM users.

## What Are Access Keys?

- **Access Keys** are used to authenticate AWS CLI or API calls.
- Unlike the username/password combo used in the Console, access keys are used for programmatic access.
- They consist of two components:

  - `Access Key ID` (public)
  - `Secret Access Key` (private)

### Example Format

```text
Access Key ID:     AKIAIOSFODNN7EXAMPLE
Secret Access Key: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
```

These two are used **together** to authenticate API and CLI calls to AWS services.

## Key Characteristics

| Attribute             | Description                                                                                    |
| --------------------- | ---------------------------------------------------------------------------------------------- |
| **Long-term**         | Access keys do not expire automatically; you must delete or rotate them.                       |
| **Up to Two Keys**    | IAM users can have **up to two** active access keys at a time.                                 |
| **Lifecycle Control** | Keys can be **created**, **deactivated**, **reactivated**, or **deleted**.                     |
| **Sensitive**         | Treat your **Secret Access Key** like a password. If exposed, anyone can act as your IAM user. |

## Access Keys vs Username & Password

| Feature                 | Username & Password             | Access Keys                       |
| ----------------------- | ------------------------------- | --------------------------------- |
| Used For                | Console UI                      | CLI/API                           |
| MFA Support             | Yes                             | No native MFA                     |
| Max Number              | One                             | Two                               |
| Visibility After Create | Password can be changed anytime | Secret access key shown only once |

## Credentials Structure

All AWS credentials have:

- **Public part** (username or access key ID)
- **Private part** (password or secret access key)

This mirrors the design of other secure authentication systems.

Multi-factor authentication (MFA) adds an **additional private component**, increasing security.

## Security Implications

- **Secret access keys cannot be retrieved again** after creation.
- If you **lose** your secret access key:

  - **Delete** the entire key pair.
  - **Create a new one**.

- If the key is compromised, **immediately deactivate or delete it**.

## Rotating Access Keys

**Rotating access keys** is a best practice:

1. Create a **second** access key.
2. Update all CLI or applications to use the **new key**.
3. **Delete the old key**.

This allows for seamless transitions without downtime.

## Important Notes

- **Only IAM Users** (not IAM Roles) use access keys for long-term credentials.
- The **account root user** can have access keys but **this is strongly discouraged**.
- **IAM Roles** use **temporary credentials**, not access keys (discussed in a later section).

## Upcoming: CLI Setup Demo

In the next lesson, you will:

- **Create access keys** for IAM users in both the **general** and **production** accounts.
- **Download and configure the AWS CLI tools**.
- Use the **access keys** to authenticate CLI access to both environments.

This will complete your shift from web-based access to secure command-line and programmatic access to AWS.
