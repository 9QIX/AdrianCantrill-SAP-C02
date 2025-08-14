# Shared ORG VPC - PART2

### Switching to Participant Accounts

After creating the resource share in the management account, the next step involves accessing these shared resources from the participant accounts (Development and Production).

#### Viewing Shared Resources in Production Account

When switching to the Production account:

1. **Resource Shares Menu**:

   - Under "Shared by me": Empty (no resources shared from this account)
   - Under "Shared with me": Shows "A4L-shared-services-VPC" resource share

2. **Resource Share Details**:

   - Name, owner, creation date, status, and ARN are visible
   - All shared resources (subnets) are listed
   - Other participant accounts are NOT visible (security isolation)

3. **Account Separation**:
   - Each participant account can only see resources shared with them
   - No visibility into other participant accounts' access
   - Maintains security boundaries between accounts

#### VPC Console Access

From the Production account VPC console:

1. **Subnet Visibility**:

   - All shared subnets appear in the subnet list
   - "Owner Account" column shows the sharing account ID
   - Subnets initially appear without names (tags not shared)

2. **Independent Tagging**:
   - Participant accounts can apply their own tags and names
   - Does not affect the original resource in the owner account
   - Provides organizational flexibility per account

### Launching EC2 Instance in Shared Subnet

#### Instance Configuration

The demo creates an EC2 instance in a shared subnet with the following specifications:

**Basic Configuration:**

- Instance Name: "EC2 using shared VPC"
- AMI: Amazon Linux 2 (free tier eligible)
- Instance Type: t2.micro or t3.micro (free tier)
- Key Pair: Proceed without key pair

**Network Configuration:**

- VPC: Select the shared VPC (10.16.0.0/16)
- Subnet: Web subnet A (public subnet)
- Auto-assign Public IP: Enabled
- Auto-assign IPv6 IP: Enabled

**Security Group Configuration:**

```
Name: shared-VPC-SG
Description: shared-VPC-SG

Inbound Rules:
- SSH (Port 22): Source 0.0.0.0/0
- HTTP (Port 80): Source 0.0.0.0/0
```

#### User Data Script Implementation

The user data script is applied during instance launch:

```bash
#!/bin/bash -xe
# Shebang with error handling and command echoing

# System Updates
yum -y update        # Update all installed packages
yum -y upgrade       # Upgrade all packages to latest versions

yum -y install httpd wget    # Install Apache web server and wget utility
systemctl enable httpd       # Enable Apache to start on boot
systemctl start httpd        # Start Apache service immediately

# Install Website Content (Note: Comment says WordPress but installs static site)
wget https://cl-sharedmedia.s3.amazonaws.com/genericcatpicwebsite/index.html -P /var/www/html
# Download main HTML file to web root directory
wget https://cl-sharedmedia.s3.amazonaws.com/genericcatpicwebsite/milky.jpeg -P /var/www/html
# Download cat image 1
wget https://cl-sharedmedia.s3.amazonaws.com/genericcatpicwebsite/nori.jpeg -P /var/www/html
# Download cat image 2
wget https://cl-sharedmedia.s3.amazonaws.com/genericcatpicwebsite/sushi.jpeg -P /var/www/html
# Download cat image 3
cd /var/www/html    # Change to web root directory

# File Permissions Configuration
usermod -a -G apache ec2-user           # Add ec2-user to apache group
chown -R ec2-user:apache /var/www       # Set ownership of web directory
chmod 2775 /var/www                     # Set directory permissions with setgid bit
find /var/www -type d -exec chmod 2775 {} \;    # Set all directories to 2775
find /var/www -type f -exec chmod 0664 {} \;    # Set all files to 0664
```

**Permission Explanation:**

- `2775`: Directory permissions with setgid bit (new files inherit group ownership)
- `0664`: File permissions (read/write for owner/group, read for others)
- These permissions ensure proper web server access while maintaining security

### Important Availability Zone Considerations

#### AZ Randomization Impact

When using Resource Access Manager across accounts:

1. **AZ Labels Don't Match**:

   - AZ "A" in one account may map to AZ "C" in another
   - AWS randomizes physical AZ mapping per account
   - This prevents inference of physical infrastructure layout

2. **Planning Implications**:
   - Cannot rely on AZ labels for cross-account coordination
   - Must use actual AZ IDs for deterministic placement
   - Important for high availability and disaster recovery planning

### Resource Access Limitations

#### Read-Only Access Model

Participant accounts have specific limitations:

1. **Network Infrastructure**:

   - Cannot modify route tables of shared subnets
   - Cannot alter Network ACLs
   - Cannot attach/detach Internet Gateways
   - Cannot modify NAT Gateway configurations

2. **Available Actions**:
   - Launch EC2 instances in shared subnets
   - Create security groups within shared VPC
   - Read network configuration details
   - Apply custom tags to shared resources (local view only)

#### Resource Isolation Between Accounts

Key isolation principles:

1. **Instance Visibility**:

   - EC2 instances are only visible in the account that created them
   - Management account cannot see participant account instances
   - Participant accounts cannot see each other's instances

2. **Security Boundaries**:
   - Each account maintains independent resource management
   - Shared infrastructure doesn't compromise account isolation
   - Billing and compliance remain account-specific

### Testing and Verification

#### Website Access Verification

After instance launch and user data execution:

1. **Access Method**:

   - Copy the instance's public IPv4 address
   - Open in web browser
   - Should display cat pictures website

2. **Expected Result**:
   - Functional web server running Apache
   - Static HTML page with cat images
   - Demonstrates successful deployment in shared infrastructure

### Development Account Access

#### Parallel Access Capabilities

The Development account has identical access:

1. **Resource Visibility**:

   - Same shared subnets visible
   - Same read-only limitations apply
   - Independent tagging capabilities

2. **Instance Isolation**:
   - Cannot see Production account's EC2 instances
   - Can launch its own instances in shared subnets
   - Maintains complete account separation

### Cleanup Procedures

#### Step-by-Step Cleanup

**In Production Account:**

1. Terminate EC2 instance:

   - Right-click instance → Terminate instance
   - Wait for termination to complete

2. Delete Security Group:
   - Navigate to Security Groups
   - Select "shared-VPC-SG"
   - Actions → Delete security groups
   - May need to wait up to 10 minutes for dependencies to clear

**In Management Account:**

1. Delete Resource Share:

   - Navigate to Resource Access Manager
   - Select resource share under "Shared by me"
   - Click Delete → Confirm deletion
   - Wait for deletion to complete

2. Delete CloudFormation Stack:
   - Navigate to CloudFormation console
   - Select "A4LVPC" stack
   - Click Delete → Confirm deletion
   - This removes all VPC infrastructure

#### Cleanup Dependencies

Important considerations during cleanup:

- EC2 instances must be fully terminated before security group deletion
- Resource shares cannot be deleted while resources are in use
- CloudFormation stack deletion removes all created resources
- Deletion process may take several minutes to complete
