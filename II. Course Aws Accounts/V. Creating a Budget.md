# AWS Cost Management & Free Tier

## Overview

This lesson focuses on **basic AWS cost management** and how to **stay within the AWS Free Tier**. Key areas covered include:

- Understanding the AWS Free Tier
- Accessing billing tools
- Setting up billing alerts
- Creating a monthly cost budget

## 1. AWS Free Tier

### Types of Free Tier Offers

AWS Free Tier consists of three types of offers:
| Type | Description |
| --------------------- | --------------------------------------------------------- |
| **Always Free** | Services that are always free up to a certain usage level |
| **12-Months Free** | Free for 12 months post sign-up |
| **Short-Term Trials** | Limited-time free trials for specific services |

**Examples:**

- **EC2**: 750 hours/month of `t2.micro` or `t3.micro` (depending on region)
- **S3**: 5GB of standard storage
- **RDS**: 750 hours/month for specific DB instances

**Reference:** [AWS Free Tier Overview](https://aws.amazon.com/free)

## 2. Accessing AWS Billing Tools

### Steps:

1. **Log in as root user**
2. Go to the top-right **dropdown menu**
3. Click **Billing Dashboard**

This dashboard provides:

- Monthly bills
- Historical bills and payments
- Cost & usage reports
- Forecasted spend (via **Cost Explorer**)

**Useful Views:**

- Monthly spend trends
- Region/service breakdowns
- Historical usage and forecasts

## 3. Enable Billing Alerts

To receive timely billing notifications:

1. Go to **Billing Preferences**
2. Check the following options:

   - Receive PDF invoice by email
   - Receive Free Tier usage alerts
   - Receive billing alerts
   - Provide a **notification email address**

3. Click **Save Preferences**

**Importance:** Alerts prevent surprise charges and help monitor usage effectively.

## 4. Creating a Monthly Cost Budget

### Steps to Create a Budget:

1. Navigate to **Budgets** in the left menu
2. Click **Create a Budget**
3. Choose **Cost Budget**

### Budget Settings:

- **Name:** `monthly-10-usd-budget`
- **Period:** Monthly
- **Type:** Recurring
- **Budget Amount:** `$10`

### Alert Configuration:

- **Alert Threshold:** 50%
- **Trigger Type:** Actual
- **Email Notification:** Enter your email address

### Finalize:

- Review all inputs
- Click **Create Budget**

> AWS will now track your cost and notify you when actual usage reaches 50% of the \$10 monthly budget.

## 5. Cost Explorer Activation

If prompted:

- Click **Enable Cost Explorer**
- Launch the tool
- AWS may take **up to 24 hours** to prepare the data

You can still proceed with budget creation without waiting for full data population.

## Summary

- Use **AWS Free Tier** mindfully throughout the course
- Regularly monitor costs in the **Billing Dashboard**
- **Enable alerts** to stay informed
- Create **budgets** to enforce cost boundaries

> This is a crucial habit, especially for production workloads, to prevent unexpected charges.
