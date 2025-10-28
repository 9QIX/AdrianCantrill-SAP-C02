# Chapter III: Advanced Permissions And Accounts - Summary

## Overview
This chapter covers advanced multi-account management, permissions evaluation, and enterprise-scale AWS governance. Key topics include AWS Organizations, Service Control Policies (SCPs), Security Token Service (STS), cross-account access patterns, and AWS Resource Access Manager (RAM).

## Key Services & Concepts

### 1. AWS Organizations
**Purpose**: Centralized management of multiple AWS accounts for enterprises

**Why Use Organizations**:
- Centralized account creation and management
- Unified billing and cost management
- Structured governance across accounts
- Scalable beyond 5-10 accounts

**Core Components**:

#### Management Account (Master/Payer Account)
- Account used to create the organization
- Unique and special within organization
- Handles billing for all member accounts
- Cannot be restricted by SCPs

#### Member Accounts
- Standard AWS accounts that join the organization
- Lose individual billing (use consolidated billing)
- Can be grouped using Organizational Units (OUs)

#### Organizational Structure
```
Root (Organization container)
├── OU: Business Unit A
│   ├── Account: Dev
│   ├── Account: Prod
├── OU: Business Unit B
│   ├── Account: Dev
│   └── OU: Sub-group
│       └── Account: Test
```

**Account Creation Options**:
- **Invite existing accounts**: Requires acceptance
- **Create new accounts directly**: Only needs unique email

### 2. Service Control Policies (SCPs)
**Purpose**: Define permission boundaries across AWS accounts in an organization

**Key Characteristics**:
- JSON policy documents
- **Do NOT grant permissions** - only define what CAN be granted
- Act as account-level permission boundaries
- Apply to all IAM identities in affected accounts
- **Do NOT apply to Management Account**

**Attachment Levels**:
- **Root container**: Affects all accounts in organization
- **Organizational Unit**: Affects all accounts in OU and nested OUs
- **Individual Account**: Affects that account only

**Permission Evaluation**:
```
Effective Permissions = IAM Policy ∩ SCP
```

**SCP Strategies**:

#### Deny List (Default)
- Uses `FullAWSAccess` SCP by default
- Allows all actions unless explicitly denied
- Lower administrative overhead
- Automatically includes new AWS services

#### Allow List (Restrictive)
- Remove default `FullAWSAccess`
- Explicitly define allowed services only
- More secure but higher admin overhead
- Easy to accidentally block required services

### 3. Security Token Service (STS)
**Purpose**: Provides temporary security credentials for AWS access

**Key Operations**:
- **AssumeRole**: Assume IAM role for temporary access
- **AssumeRoleWithSAML**: Exchange SAML assertion for credentials
- **AssumeRoleWithWebIdentity**: Exchange web identity token for credentials
- **GetSessionToken**: Get temporary credentials for MFA

**Temporary Credentials Include**:
- Access Key ID
- Secret Access Key
- Session Token
- Expiration time

**Use Cases**:
- Cross-account access
- Identity federation
- Mobile/web applications
- EC2 instance roles

### 4. Cross-Account Access Patterns

#### S3 Cross-Account Access Methods
1. **Access Control Lists (ACLs)**: Legacy, limited granularity
2. **Bucket Policies**: Resource-based policies on S3 buckets
3. **IAM Roles**: Assume role for cross-account access (recommended)

#### Role-Based Cross-Account Access
```
Account A (User) → Assume Role → Account B (Role) → Access Resources
```

**Benefits**:
- No long-term credentials sharing
- Centralized permission management
- Audit trail through CloudTrail
- Temporary access with expiration

### 5. AWS Permissions Evaluation
**Evaluation Logic**:
1. **Explicit Deny**: Always wins (SCPs, IAM policies)
2. **Explicit Allow**: Required from IAM policies
3. **SCP Boundaries**: Must allow the action
4. **Resource Policies**: Can grant additional access
5. **Permission Boundaries**: Additional restrictions

**Policy Types**:
- **Identity-based policies**: Attached to users, groups, roles
- **Resource-based policies**: Attached to resources (S3, KMS, etc.)
- **Permission boundaries**: Maximum permissions for identity
- **Service Control Policies**: Organization-level boundaries

### 6. AWS Resource Access Manager (RAM)
**Purpose**: Share AWS resources across accounts and organizations

**Shareable Resources**:
- VPC subnets
- Transit Gateway
- Route 53 Resolver rules
- License Manager configurations
- And many more

**Sharing Models**:
- Within organization (automatic acceptance)
- External accounts (requires acceptance)
- Cross-organization sharing

### 7. Service Quotas
**Purpose**: Manage and request increases for AWS service limits

**Types of Limits**:
- **Hard limits**: Cannot be increased
- **Soft limits**: Can be increased via support request
- **Regional limits**: Apply per region
- **Account limits**: Apply per account

**Management**:
- View current quotas and usage
- Request quota increases
- Set up CloudWatch alarms for quota monitoring

## Advanced Concepts

### Permission Boundaries
**Purpose**: Set maximum permissions for IAM entities

**Use Cases**:
- Delegate permission management
- Prevent privilege escalation
- Compliance requirements

**Evaluation**: Identity permissions ∩ Permission boundary

### Policy Interpretation Examples
**Complex scenarios involving**:
- Multiple policy types
- Explicit allows and denies
- Cross-account access
- Resource-based policies

### Revoking Temporary Credentials
**Methods**:
- Change role trust policy
- Add explicit deny to role policy
- Use policy conditions (time-based, IP-based)
- Revoke active sessions

## Best Practices

### Multi-Account Strategy
- Use separate accounts for different environments
- Implement centralized login account
- Use role switching for account access
- Federate with enterprise identity providers

### SCP Implementation
- Start with deny list approach
- Use least privilege principle
- Test SCPs in non-production first
- Document SCP purposes and effects

### Cross-Account Access
- Prefer IAM roles over resource policies
- Use temporary credentials
- Implement proper logging and monitoring
- Regular access reviews

## Common Exam Scenarios

### Organization Design
- Choosing appropriate OU structure
- SCP placement and inheritance
- Account creation strategies
- Billing and cost allocation

### Permission Troubleshooting
- Understanding why access is denied
- Policy evaluation order
- SCP vs IAM policy conflicts
- Cross-account access issues

### Security Patterns
- Implementing least privilege
- Preventing privilege escalation
- Audit and compliance requirements
- Incident response procedures

## Key Exam Facts

- SCPs do NOT apply to Management Account
- SCPs do NOT grant permissions (only restrict)
- Explicit deny always wins in policy evaluation
- Organizations enable consolidated billing
- RAM allows resource sharing across accounts
- STS provides temporary credentials
- Permission boundaries set maximum permissions
- Service quotas can be monitored and increased

## Architecture Patterns

### Centralized Identity Management
```
Login Account → IAM Users → Assume Roles → Member Accounts
```

### Federated Access
```
Corporate IdP → SAML/OIDC → STS → Temporary Credentials → AWS Resources
```

### Multi-Account Governance
```
Management Account → Organizations → SCPs → Member Accounts → Workloads
```

This chapter is essential for understanding enterprise AWS architectures and is heavily tested in the SAP-C02 exam, particularly around multi-account strategies, permissions evaluation, and governance patterns.