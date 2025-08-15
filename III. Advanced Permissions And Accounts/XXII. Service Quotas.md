# AWS Service Quotas

## Introduction

AWS Service Quotas define how much of a specific AWS resource you can use in your account. They impact **every AWS account** and are essential knowledge for the AWS Solutions Architect Professional exam.

While not the most glamorous AWS topic, understanding service quotas is critical for:

- Designing scalable architectures.
- Avoiding unexpected service limitations in production.
- Preparing for quota increase requests in advance.

## What Are Service Quotas?

A **service quota** is a limit on the amount of a specific AWS resource or feature your account can use.

**Examples:**

- Maximum number of EC2 instances of a certain type in a region.
- Maximum number of IAM users in an account.

### Types of Quotas

1. **Per-region quotas** (most services)

   - Example: EC2 instance count limit in `us-east-1`.

2. **Per-account quotas** (mostly global services)

   - Example: IAM user limit applies to the entire account across all regions.

## Default Quotas

- AWS sets **low default quotas** for new accounts (for protection).
- Larger systems often require quota increases.
- Most quotas **can be increased** on request.
- **Some quotas cannot be changed** — these require architectural workarounds.

## Unchangeable Quotas

Some quotas are **hard limits** that cannot be increased.

**Example:**

- **IAM Users**: Hard limit of `5,000` users per account.

  - Architectural impact: For >5,000 users, use an external identity provider (e.g., Cognito, SSO, or third-party IdP).

## Changeable Quotas

- Quotas that can be increased require a **request process**.
- Larger requests = more review time + human involvement.
- Important for projects with **excessive resource demands** — plan quota request timelines into project schedules.

## Where to Find Quota Information

### 1. **AWS Service Endpoints and Quotas Page**

- Shows **endpoints** and **default quotas** per service.

- Example (DynamoDB):

  - **Per table**: 40,000 read capacity, 40,000 write capacity (per region).
  - **Per account**: 80,000 read capacity, 80,000 write capacity.

- Example (EC2):

  - Elastic IP addresses per region.
  - On-demand instance limits per instance family.
  - Spot instance limits.
  - Key pairs, placement groups, etc.

**Reference:**
[Service endpoints and quotas](https://docs.aws.amazon.com/general/latest/gr/aws-service-information.html)

### 2. **AWS Service Quotas Console**

- Search for "Service Quotas" in AWS Console.
- Shows **default vs applied quotas** for a service.
- Adjustable quotas are labeled as such.
- Directly request quota increases from this console.

**Example:**

- Default: 5 running on-demand standard instances per region for certain instance families.
- Applied: 5 (same as default).
- Adjustable: Yes — can request higher.

**Reference:**
[Service Quotas Console](https://console.aws.amazon.com/servicequotas/home?region=us-east-1#!/)

## Requesting Quota Increases

### Method 1: Service Quotas Console (Preferred)

1. Select a service.
2. Choose the quota.
3. Click **Request Quota Increase**.
4. Submit desired value.

### Method 2: AWS Support Center (Legacy)

- Create a ticket → Select "Service limit increase".
- Choose the service, region, and resource type.
- Enter requested value.
- Less efficient for tracking multiple requests.

## Quota Management in AWS Organizations

- Use **Quota Request Templates** to standardize quota increases across all organization accounts.
- Reduces repetitive manual requests for new accounts.

**Reference:**
[Quota request templates](https://docs.aws.amazon.com/servicequotas/latest/userguide/organization-templates.html)

## Monitoring Quotas with CloudWatch

- Create **CloudWatch alarms** for quota usage.
- Trigger notifications when usage exceeds a threshold (e.g., 80% of quota).
- Helps prevent production issues by allowing proactive increases.

**Reference:**
[Configure CloudWatch alarms for quotas](https://docs.aws.amazon.com/servicequotas/latest/userguide/configure-cloudwatch.html)

## AWS CLI for Service Quotas

You can interact with Service Quotas via the AWS CLI.

### List Applied Service Quotas

```bash
aws service-quotas list-service-quotas --service-code ec2
```

**Explanation:**

- `aws service-quotas`: Uses the Service Quotas CLI API.
- `list-service-quotas`: Lists all quotas currently applied to your account for the specified service.
- `--service-code ec2`: Specifies EC2 service.

### List Default Service Quotas

```bash
aws service-quotas list-aws-default-service-quotas --service-code ec2
```

**Explanation:**

- `list-aws-default-service-quotas`: Shows AWS default limits, not your account’s applied limits.
- Useful for comparing default vs applied quotas.

### Request a Quota Increase

```bash
aws service-quotas request-service-quota-increase \
    --service-code ec2 \
    --quota-code L-1216C47A \
    --desired-value 20
```

**Explanation:**

- `request-service-quota-increase`: Submits a request to raise a quota.
- `--service-code ec2`: Service to adjust.
- `--quota-code L-1216C47A`: Unique identifier for the quota type.
- `--desired-value 20`: New requested quota.

**Reference Commands:**

- [List Service Quotas](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/service-quotas/list-service-quotas.html)
- [List Default Quotas](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/service-quotas/list-aws-default-service-quotas.html)
- [Request Quota Increase](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/service-quotas/request-service-quota-increase.html)

## Exam Tips

- Know **which quotas are changeable vs fixed**.
- Be familiar with **where** to find quota info in the console and docs.
- Understand how to **request increases** via:

  - Service Quotas Console
  - AWS Support Center
  - AWS CLI

- Understand **CloudWatch quota monitoring**.
