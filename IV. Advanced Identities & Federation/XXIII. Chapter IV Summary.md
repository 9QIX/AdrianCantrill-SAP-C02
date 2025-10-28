# Chapter IV: Advanced Identities & Federation - Summary

## Overview
This chapter covers advanced identity and federation solutions in AWS, focusing on enterprise-scale identity management, authentication, and authorization patterns. Key topics include SAML federation, IAM Identity Center, Amazon Cognito, WorkSpaces, Directory Services, and AWS Control Tower.

## Key Services & Concepts

### 1. SAML 2.0 Identity Federation
**Purpose**: Exchange external enterprise identity for temporary AWS credentials

**When to Use**:
- Enterprise SAML-compatible IdP exists
- Large user base (>5,000 users)
- Need centralized identity management
- Single source of truth requirement

**Architecture**:
- **Application-initiated**: App → IdP → SAML assertion → STS → AWS credentials
- **User-initiated**: User → IdP portal → Role selection → AWS Console access

**Key Components**:
- IAM SAML Provider
- IAM Roles for SAML
- AWS STS (AssumeRoleWithSAML)
- AWS Sign-In SAML endpoint

**Session Duration**: Up to 12 hours

### 2. IAM Identity Center (AWS SSO)
**Purpose**: Centralized workforce identity federation across multiple AWS accounts

**Why Choose Over Direct SAML**:
- Centralized control across accounts
- Abstraction from protocol complexity
- Reduced operational overhead
- Broader scope (AWS + external apps)

**Identity Sources Supported**:
- Built-in identity store
- AWS Managed Microsoft AD
- On-premises Microsoft AD
- External SAML 2.0 IdP

**Manages**:
- Access to all AWS accounts in organization
- AWS Console and CLI v2 access
- External business applications (Dropbox, Slack, Office 365)

### 3. Amazon Cognito
**Two Components**:

#### User Pools
- Sign-up/sign-in functionality
- Issues JWTs after authentication
- Supports internal, social, and SAML identity sources
- Built-in hosted UI with MFA support
- **Does NOT grant AWS service access**

#### Identity Pools (Federated Identities)
- Exchanges identity tokens for temporary AWS credentials
- Supports authenticated and unauthenticated identities
- Uses IAM roles for access control
- **Provides AWS credentials for service access**

**Architecture Patterns**:
- User Pools only (JWTs for app/API)
- Identity Pools directly with external IdPs
- Combined approach (simplified multi-IdP handling)

### 4. Amazon WorkSpaces
**Purpose**: Managed virtual desktops (Windows/Linux) from AWS

**Key Features**:
- Requires AWS Directory Service for authentication
- Network interfaces injected into customer VPC
- Storage encryption via KMS
- Multiple bundles (Value to GraphicsPro)
- Billing: Monthly or Hourly with auto-stop

**Directory Integration**:
- Simple AD (lowest cost, PoC)
- AD Connector (proxy to on-prem AD)
- AWS Managed Microsoft AD (full AD features)

**Availability**: Single AZ (not inherently HA)

### 5. AWS Directory Service

#### AWS Managed Microsoft AD
- Native Microsoft AD as managed service
- Deployed across multiple AZs
- Supports trusts with on-premises AD
- Schema extensions and GPO support
- RADIUS-based MFA integration
- Works independently of on-prem connectivity

#### AD Connector
- Proxy to on-premises AD
- No directory data in AWS
- Depends on on-prem connectivity
- Cannot establish trusts

#### Simple AD
- Samba-based directory
- Limited compatibility
- Good for small labs/PoCs

### 6. AWS Control Tower
**Purpose**: Orchestrated multi-account governance and setup

**Key Components**:
- **Landing Zone**: Governed multi-account foundation
- **Guardrails**: Governance rules (Preventive via SCPs, Detective via Config)
- **Account Factory**: Automated account provisioning
- **Baseline Accounts**: Log Archive and Audit accounts

**Organizational Structure**:
- Management Account (hosts Control Tower)
- Security OU (Log Archive + Audit accounts)
- Custom OUs (workload accounts)

**Services Orchestrated**:
- AWS Organizations
- IAM Identity Center
- CloudFormation
- AWS Config
- CloudTrail
- CloudWatch/SNS
- Service Catalog

## Selection Guidelines for SAP-C02

### Workforce vs Customer Identity
- **Workforce identities** → IAM Identity Center (SSO)
- **Customer identities** → Amazon Cognito

### Federation Approach
- **Enterprise SAML IdP** → SAML 2.0 or IAM Identity Center
- **Social providers** → Cognito Identity Pools
- **Web identity** → Cognito (NOT SAML)

### Directory Services
- **Need trusts/schema extensions** → AWS Managed Microsoft AD
- **Proxy to on-prem only** → AD Connector
- **Small labs/PoCs** → Simple AD

### Multi-Account Governance
- **Standardized setup** → AWS Control Tower
- **Custom requirements** → Manual Organizations setup

## Common Exam Traps

1. **SAML vs Web Identity**: Don't choose SAML for Google/Facebook/Twitter scenarios
2. **User Pools vs Identity Pools**: User Pools issue JWTs, Identity Pools provide AWS credentials
3. **Directory Service Types**: Only Managed Microsoft AD supports trusts and schema extensions
4. **WorkSpaces Availability**: Not HA by default, single AZ deployment
5. **Control Tower Guardrails**: Preventive (SCPs) vs Detective (Config rules)
6. **SSO vs Cognito**: SSO for workforce, Cognito for customers

## Key Exam Facts

- SAML sessions: Up to 12 hours
- Cognito supports web scale (no IAM user limits)
- WorkSpaces requires Directory Service
- Control Tower creates Log Archive and Audit accounts
- Management account exempt from SCPs
- Identity Pools support both authenticated and unauthenticated access
- IAM Identity Center is free to use
- AWS Managed Microsoft AD works independently of on-prem connectivity

## Architecture Patterns

### Enterprise Identity Federation
```
On-prem IdP → SAML Assertion → AWS STS → Temporary Credentials → AWS Services
```

### Multi-Account SSO
```
Identity Source → IAM Identity Center → Multiple AWS Accounts → Console/CLI Access
```

### Customer Identity (Web/Mobile)
```
User → Cognito User Pool → JWT → Identity Pool → AWS Credentials → AWS Services
```

### Hybrid Directory
```
On-prem AD ←→ Trust ←→ AWS Managed Microsoft AD → WorkSpaces/EC2
```

This chapter is fundamental for understanding enterprise-scale identity management in AWS and is heavily tested in the SAP-C02 exam.