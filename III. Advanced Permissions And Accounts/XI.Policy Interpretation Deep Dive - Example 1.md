# AWS IAM Policy Analysis – Part 1 - Holiday Gifts Example

## Overview

This lesson walks through the analysis of a multi-statement AWS IAM policy, focusing on how **Allow** and **Deny** statements interact, and how conditions can temporarily change access behavior. The example centers on an S3 bucket named **holidaygifts**, simulating a scenario where read access is restricted during a certain date range (to prevent "peeking" at presents).

## The Policy JSON

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": ["s3:PutObject", "s3:PutObjectAcl", "s3:GetObject", "s3:GetObjectAcl", "s3:DeleteObject"],
      "Resource": "arn:aws:s3:::holidaygifts/*"
    },
    {
      "Effect": "Deny",
      "Action": ["s3:GetObject", "s3:GetObjectAcl"],
      "Resource": "arn:aws:s3:::holidaygifts/*",
      "Condition": {
        "DateGreaterThan": { "aws:CurrentTime": "2022-12-01T00:00:00Z" },
        "DateLessThan": { "aws:CurrentTime": "2022-12-25T06:00:00Z" }
      }
    }
  ]
}
```

## Line-by-Line Code Explanation (with inline comments)

```json
{
  "Version": "2012-10-17", // Policy language version (AWS standard date)
  "Statement": [
    // List of policy statements
    {
      "Effect": "Allow", // Grants permissions
      "Action": [
        // Actions that are allowed
        "s3:PutObject", // Upload objects to the bucket
        "s3:PutObjectAcl", // Set ACL (permissions) on uploaded objects
        "s3:GetObject", // Download objects from the bucket
        "s3:GetObjectAcl", // Get ACL information of objects
        "s3:DeleteObject" // Delete objects from the bucket
      ],
      "Resource": "arn:aws:s3:::holidaygifts/*" // Applies to all objects in 'holidaygifts' bucket
    },
    {
      "Effect": "Deny", // Denies permissions (overrides Allow)
      "Action": [
        // Actions that are denied
        "s3:GetObject", // Prevent downloading objects
        "s3:GetObjectAcl" // Prevent retrieving ACL info of objects
      ],
      "Resource": "arn:aws:s3:::holidaygifts/*", // Same resource as Allow statement
      "Condition": {
        // Deny applies only if condition is true
        "DateGreaterThan": { "aws:CurrentTime": "2022-12-01T00:00:00Z" }, // After Dec 1st, 00:00 UTC
        "DateLessThan": { "aws:CurrentTime": "2022-12-25T06:00:00Z" } // Before Dec 25th, 06:00 UTC
      }
    }
  ]
}
```

## Plain-English Explanation of Policy Behavior

1. **Statement 1 (Allow)**
   Grants full object-level access to the `holidaygifts` bucket:

   - Upload objects
   - Change object ACLs
   - Download objects
   - Read object ACLs
   - Delete objects

2. **Statement 2 (Deny)**
   Temporarily **denies** read-related actions (`s3:GetObject` and `s3:GetObjectAcl`) **for the same bucket** during a specific date range:

   - **Start:** December 1st, 00:00 UTC
   - **End:** December 25th, 06:00 UTC

3. **Access Outcome**

   - **Outside the date range:** Full read/write/delete access to objects.
   - **Within the date range:** Write and delete allowed, **reads blocked** (bucket becomes "write-only").
   - **Reason:** In AWS IAM, **explicit deny overrides allow**. If both Allow and Deny statements match an action and resource, Deny wins.

## Key AWS IAM Policy Concepts Highlighted

- **Implicit Deny:** By default, all actions are denied unless explicitly allowed.
- **Explicit Allow:** Grants the ability to perform specified actions on specified resources.
- **Explicit Deny:** Always overrides Allow when there’s an overlap.
- **Conditions:** Modify when a statement is in effect. In this example, conditions are based on the current date and time (`aws:CurrentTime`).
- **Resource ARN Targeting:**

  - `arn:aws:s3:::holidaygifts/*` means "all objects in the bucket `holidaygifts`".
  - The `/*` suffix targets **objects**, not the bucket itself.

## Real-World Analogy

Imagine a gift storage room (the S3 bucket):

- Normally, you can put gifts in, take them out, rearrange them, or check who can access them.
- Between Dec 1 and Christmas morning, a guard is posted at the door:

  - You can still bring in and remove gifts you’ve placed.
  - But you **cannot peek inside the packages** or check who has permission to open them.
  - On Christmas morning after 6 AM, the guard leaves, and you can read as well as write again.

## Exam Tip (SAP-C02)

When analyzing IAM policies:

1. Count how many **statements** exist.
2. Determine each statement’s **effect** (Allow/Deny).
3. Compare **actions** and **resources** for overlaps.
4. Apply the **Deny > Allow > Implicit Deny** rule.
5. Check **conditions** carefully — they may restrict or extend access temporarily or based on context.
