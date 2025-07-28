# AWS Organizations

## Overview

**AWS Organizations** is a service designed to help large businesses manage multiple AWS accounts in a cost-effective, scalable, and secure way. It enables centralized management, consolidated billing, and structured governance of AWS accounts within an enterprise.

## Why Use AWS Organizations?

Without AWS Organizations, managing multiple AWS accounts becomes complex:

- Each account would have its own IAM users and billing.
- Scalability becomes impractical beyond 5–10 accounts.
- Financial and administrative overhead increases significantly.

AWS Organizations solves these issues by:

- Centralizing account creation and management.
- Enabling unified billing.
- Implementing access control policies across all accounts.

## Core Concepts

### 1. **Management Account (formerly "Master Account")**

- The account used to **create** the organization becomes the **Management Account**.
- It's unique and special within the organization.
- Also referred to as:

  - **Master Account**
  - **Payer Account**

> **Note**: Only one Management Account per organization is allowed.

### 2. **Member Accounts**

- Standard AWS accounts that **join** an AWS Organization become **Member Accounts**.
- These accounts:

  - Lose individual billing settings (use consolidated billing via the Management Account).
  - Can be grouped and managed using **organizational units** (OUs).

### 3. **Organizational Structure (Hierarchical Tree)**

The structure follows a tree-like hierarchy:

```
Root (Organization-level container)
├── OU: Business Unit A
│   ├── Account: Dev
│   ├── Account: Prod
│
├── OU: Business Unit B
│   ├── Account: Dev
│   └── OU: Sub-group
│       └── Account: Test
```

- **Root**: Top-most container of the organization.
- **Organizational Units (OUs)**: Containers within the root (can be nested).
- **Accounts**: Can reside in root or inside OUs.

> **Important**: The "Root" here refers to the **organization’s root container**, **not** the **root user** of an individual AWS account.

## Billing in AWS Organizations

### Consolidated Billing

- All charges from member accounts are routed to the **payer/management account**.
- **Benefits**:

  - One invoice for all accounts.
  - Simplified financial tracking.
  - Access to **volume-based discounts** and **Reserved Instance sharing** across accounts.

## Account Creation Options

### 1. **Invite Existing Accounts**

- Send invitations to join the organization.
- The invited account must **accept** the invitation.

### 2. **Create New Accounts Directly**

- Requires only a unique email address.
- No manual acceptance process.
- Ideal for automating account provisioning.

## Identity Management and Access

### Best Practices

- Use a **centralized login account** for all IAM users.
- **Avoid IAM users in every account**.
- Use **IAM roles** and **role switching** to access other accounts.

### Enterprise Integration

- Federate with on-premise identity providers (e.g., Active Directory).
- Use AWS IAM Identity Center (formerly SSO) for central login and access delegation.

### Role Switching Example

```plaintext
[Login Account: Central IAM Identity]
        |
        |  Assume Role (via console or CLI)
        v
[Member Account: Production]
[Member Account: Development]
```

#### Behind the Scenes:

- User logs in to the central login account.
- User assumes an IAM role in the target account.
- Access is granted based on the permissions of that role.

## Security & Policy Management

### Service Control Policies (SCPs)

- SCPs allow governance and restrictions at the **organization** or **OU level**.
- Can **deny** or **limit** actions regardless of IAM permissions.
- Example: Prevent the use of a specific AWS region across all dev accounts.

> SCPs will be covered in detail in another dedicated lesson.

## Demo Setup (Preview)

### Accounts Involved

- **General Account** → Becomes the **Management Account**.
- **Production Account** → Invited as a **Member Account**.
- **Development Account** → Created directly in the organization.

This structure forms the base of the practical hands-on exercises for the course.

## Summary of Key Terms

| Term                             | Description                                                                         |
| -------------------------------- | ----------------------------------------------------------------------------------- |
| **Management Account**           | The original account that created the organization. Handles billing and governance. |
| **Member Account**               | An AWS account that is part of the organization but not the creator.                |
| **Root (Org)**                   | Top container in an organization, holds OUs or accounts.                            |
| **OU (Organizational Unit)**     | Logical container to group accounts for policy and structure.                       |
| **SCP (Service Control Policy)** | Policy to manage account permissions across the org.                                |
| **Consolidated Billing**         | Single invoice for all member accounts.                                             |
| **Role Switching**               | IAM users in one account assume roles in another for access.                        |

## Next Steps

In the **next lesson**, you will:

1. Create an AWS Organization using the **general** account.
2. Invite the **production** account to join as a member.
3. Create a **development** account directly within the organization.
4. Implement centralized login and role switching.
