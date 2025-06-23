# AWS CLI Setup and IAM Admin Access Key Configuration

## Overview

This lesson walks through the following:

- Creating IAM access keys for **two AWS accounts**:

  - _General Account_
  - _Production Account_

- Installing the **AWS CLI v2**
- Configuring **named profiles** in AWS CLI for both accounts
- **Best practices** around credential security

## 1. Creating Access Keys for IAM Admin Users

### Steps for General AWS Account:

1. **Login** as IAM Admin user to the General AWS account.
2. Go to **My Security Credentials** via the account dropdown.
3. Click **Create Access Key**.
4. Copy or **download the `.csv`** containing:

   - Access Key ID
   - Secret Access Key

5. Rename the file to:

   ```
   iam_admin_access_keys_general.csv
   ```

### Important Notes:

- Each IAM user can have **up to 2 access keys** (active/inactive).
- If secret key is lost, you **must delete and recreate** the key.
- Store keys **locally and securely** (not in cloud storage).

### Repeat for Production AWS Account:

1. Log out from General account.
2. Log into Production AWS account using IAM Admin credentials and MFA.
3. Navigate to **My Security Credentials**, then **Create Access Key**.
4. Download the key file and rename to:

   ```
   iam_admin_access_keys_production.csv
   ```

## 2. Installing AWS CLI v2

### Steps (Windows Example):

1. Visit the [AWS CLI v2 Install Page](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html)
2. Download the installer for your OS.
3. Run the installer and accept all defaults.
4. After installation, verify by running:

```bash
aws --version
```

Should output something like:

```bash
aws-cli/2.x.x Python/3.x.x ...
```

## 3. Configuring AWS CLI Named Profiles

### Explanation:

The AWS CLI uses **configuration profiles** for managing credentials. These can be:

- **Default profile**: used when no profile is specified.
- **Named profiles**: allow working with multiple accounts or roles.

### Command Syntax:

```bash
aws configure --profile <profile_name>
```

### Configure `iam-admin-general` Profile:

```bash
aws configure --profile iam-admin-general
```

- **Access Key ID**: from `iam_admin_access_keys_general.csv` (before the comma)
- **Secret Access Key**: from the same file (after the comma)
- **Region**: `us-east-1`
- **Output Format**: leave blank or press Enter

### Configure `iam-admin-production` Profile:

```bash
aws configure --profile iam-admin-production
```

- Use `iam_admin_access_keys_production.csv`
- Region: `us-east-1`
- Output Format: blank

## 4. Verifying the Configuration

### Using AWS CLI with Named Profile:

Example command to list S3 buckets:

```bash
aws s3 ls --profile iam-admin-general
```

Expected Result:

- No error = success
- Empty string = no buckets yet (valid)
- Error = check credentials again

Repeat for the production profile:

```bash
aws s3 ls --profile iam-admin-production
```

## 5. Credential Security Best Practices

- **Never share** access keys publicly or record them in videos.
- Treat credentials like passwords: anyone with access can act as the IAM user.
- Delete CSV key files after configuration:

  ```bash
  rm iam_admin_access_keys_general.csv
  rm iam_admin_access_keys_production.csv
  ```

- You can **regenerate** new credentials anytime if compromised.

## Summary of Configured Profiles

| Profile Name           | Purpose                             | Region      |
| ---------------------- | ----------------------------------- | ----------- |
| `iam-admin-general`    | Admin access to General AWS Account | `us-east-1` |
| `iam-admin-production` | Admin access to Production Account  | `us-east-1` |

## Final Remarks

You have now:

- Installed AWS CLI v2
- Created and configured secure access to two AWS accounts
- Validated CLI access using S3 list commands
- Followed best practices in key management and credential safety

This setup forms the foundation for all future AWS CLI and automation tasks throughout the SAP-C02 course.
