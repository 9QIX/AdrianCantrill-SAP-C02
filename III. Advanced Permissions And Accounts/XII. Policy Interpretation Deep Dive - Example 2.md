# AWS IAM Policy Analysis – Part 2 - Region Example

## Overview

This lesson examines a **single-statement IAM policy** that uses **inverse logic** to explicitly deny actions outside a list of approved AWS regions, while exempting global AWS services from the restriction.  
It highlights how `NotAction` and `StringNotEquals` can invert expected logic, making exam questions more challenging under pressure.

## The Policy JSON

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "DenyNonApprovedRegions",
      "Effect": "Deny",
      "NotAction": ["cloudfront:*", "iam:*", "route53:*", "support:*"],
      "Resource": "*",
      "Condition": {
        "StringNotEquals": {
          "aws:RequestedRegion": ["ap-southeast-2", "eu-west-1"]
        }
      }
    }
  ]
}
```

## Line-by-Line Explanation (with comments)

```json
{
  "Version": "2012-10-17", // AWS policy language version
  "Statement": [
    // List of statements (here, only one)
    {
      "Sid": "DenyNonApprovedRegions", // Optional identifier for the statement
      "Effect": "Deny", // Explicitly denies matching requests
      "NotAction": [
        // Matches all actions EXCEPT these
        "cloudfront:*", // CloudFront service actions
        "iam:*", // Identity and Access Management actions
        "route53:*", // Route 53 service actions
        "support:*" // AWS Support service actions
      ],
      "Resource": "*", // Applies to all resources
      "Condition": {
        // Deny applies only if condition is true
        "StringNotEquals": {
          // Inverse string comparison (does NOT equal)
          "aws:RequestedRegion": [
            // Region being requested by the API call
            "ap-southeast-2", // Sydney region
            "eu-west-1" // Ireland region
          ]
        }
      }
    }
  ]
}
```

## Plain-English Interpretation

1. **Single Statement**

   - No overlap to analyze. The `Effect` is `Deny`.

2. **Default Deny Reminder**

   - AWS starts with **implicit deny**.
   - This policy will usually be paired with a separate **Allow** policy to grant access where needed.
   - On its own, it adds an **explicit deny** condition but does not grant any permissions.

3. **Inverse Matching with `NotAction`**

   - Instead of listing the actions to deny, this lists the actions to **exclude** from denial.
   - Any action **not** in the list will be subject to the deny rule.

4. **Global Service Exemptions**

   - The excluded actions correspond to global services:

     - **CloudFront**
     - **IAM**
     - **Route 53**
     - **AWS Support**

   - These operate from `us-east-1` and would otherwise be denied if region restrictions were applied without exceptions.

5. **Condition Block – Inverse Comparison**

   - Uses `StringNotEquals` on `aws:RequestedRegion`.
   - This means the condition matches if the request is **not** in either:

     - `ap-southeast-2` (Sydney)
     - `eu-west-1` (Ireland)

   - When matched, the deny applies.

6. **Final Effect**

   - **If** the request is:

     - For a non-global service (i.e., not in `NotAction` list)
     - And the target AWS region is **not** Sydney or Ireland
       **→ Access is denied**.

   - **If** the request is to a global service in the `NotAction` list
     **→ No effect from this deny policy**.

## Real-World Analogy

Think of AWS services as different buildings in a corporate campus:

- You have **access cards** (Allow policy) that let you into certain buildings.
- This deny policy is like a **security checkpoint** that:

  - Blocks you from entering **any building outside Sydney or Ireland campuses**.
  - Ignores four special “global” buildings that everyone can always enter, no matter where they’re located.

## Key Exam Takeaways (SAP-C02)

- **Always check for inverse logic**:

  - `NotAction` → matches everything _except_ listed actions.
  - `StringNotEquals` → matches when the value is _not equal_.

- **Global services** (like IAM, CloudFront, Route 53, Support) do not operate within a specific AWS region.
- **Explicit deny** overrides allow, but here it’s scoped to non-global services outside approved regions.
- **Paired policies**: A deny-only policy without an allow policy doesn’t give permissions—it only restricts.
- For high-pressure exam scenarios:

  - **Scan first for “not”** in actions or conditions before reading the rest.
  - Inverse logic is a common trap for wrong answers.

## Visual Flow Diagram

```mermaid
flowchart TD
    A[Start: API Request] --> B{Is Action in NotAction List?}
    B -- Yes --> G[Global Service → Policy Does Not Apply]
    B -- No --> C{Is aws:RequestedRegion in Approved List?}
    C -- Yes --> H[Approved Region → Policy Does Not Apply]
    C -- No --> D[Condition True: Apply Explicit Deny]
    G --> E[Proceed with Allow Policy (if exists)]
    H --> E
    D --> F[Access Denied]
    E --> Z[Access Allowed]
```

**Explanation of Diagram**:

- The policy first checks if the action belongs to a global service (`NotAction` list). If yes, deny is skipped.
- If not, it checks the region. If the region is approved, deny is skipped.
- If the region is **not** approved and the action is not global, the explicit deny applies.
