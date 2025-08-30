# Amazon WorkSpaces Lab (Part 2) — Register Directory, Launch a Workspace, Enable Clients, and Clean Up

## Resources Used

- **1-Click VPC Deployment** (A4L VPC with NAT):
  [https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/create/review?templateURL=https://learn-cantrill-labs.s3.amazonaws.com/awscoursedemos/0029-aws-mixed-workspaces-simple-demo/vpc_with_natgw.yaml\&stackName=A4LVPC](https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/create/review?templateURL=https://learn-cantrill-labs.s3.amazonaws.com/awscoursedemos/0029-aws-mixed-workspaces-simple-demo/vpc_with_natgw.yaml&stackName=A4LVPC)
- **Linux / Web Client Support (Device access control):**
  [https://docs.aws.amazon.com/workspaces/latest/adminguide/update-directory-details.html#control-device-access](https://docs.aws.amazon.com/workspaces/latest/adminguide/update-directory-details.html#control-device-access)

## What You’ll Accomplish

- **Register** your **Simple AD** directory with Amazon WorkSpaces.
- **Select subnets** for WorkSpaces ENIs (APP subnets in two AZs).
- **Create a directory user**, choose a **bundle** (Windows Standard, free-tier eligible), and select **Auto-Stop** running mode.
- **Provision** a Workspace and **activate** it via email (profile + client setup).
- **Enable Linux and Web access** (if needed) by updating directory device-access controls.
- **Test access** to the internet from the Workspace (through the NAT Gateways).
- **Clean up**: delete the Workspace, deregister and delete the directory, delete the CloudFormation stack.

## Prerequisites Recap

- The **Animals4Life VPC** stack (A4LVPC) is already **CREATE_COMPLETE**.
- Your **Simple AD** from Part 1 is in **Active** state.

## Detailed Steps

### 1) Register the Directory with WorkSpaces

1. Open **Amazon WorkSpaces** and choose **Create WorkSpaces**.
2. When prompted for a directory, **select your Simple AD** (e.g., “AnimalsForLife”).
   If it shows “not registered,” click **Register**.
3. Choose **two APP subnets** (two different AZs).
   In this lab, APP subnets map to CIDRs ending **10.16.32.0/20**, **10.16.96.0/20**, **10.16.160.0/20**.
   If subnet names aren’t displayed, cross-check in **VPC → Subnets** by CIDR, then select the matching /20s in the registration dialog.
4. Leave **Self-service** and **WorkDocs** **unchecked** for this lab, then **Register**.
   After registration succeeds, click **Next**.

### 2) Create a Directory User

1. Choose **Create additional user**.
2. Enter **Username**, **First/Last name**, and a **valid email** (the activation email is sent here).
3. Return to the user list, **select** your new user, then **Next**.

### 3) Pick a Bundle (OS + Size)

- Choose a **free-tier eligible Standard bundle** based on **Windows** (e.g., “Standard with Windows 10/Server 2019-based” or Windows 11, depending on what’s shown).
- Click **Next**.

### 4) Choose Running Mode and Storage

- Select **Auto-Stop** and set **1 hour** idle timeout (cost-efficient for labs).
- Leave **encryption** and **volume sizes** at defaults for this lab.
- **Next → Create Workspace.**

### 5) Provisioning and Activation

- Status shows **Pending** until it becomes **Available** (typically **20–30 minutes**).
- You’ll receive an **activation email**. Keep it handy; it contains:

  - An **activation URL** to complete the user profile and set a password.
  - A **registration code** for the client.

### 6) Enable Linux and Web Access (Only if Needed)

- By default, **Linux client** and **Web access** may be **disabled**.
- Go to **WorkSpaces → Directories → \[your directory] → Edit (All platforms)**.
- **Enable** checkboxes for **Linux** and **Web access**, then **Save**.
  Reference: “Control device access” in the Admin Guide (link above).

### 7) Install and Register the Client

1. From the activation flow, download the **WorkSpaces Client** for your OS (Windows/macOS; Linux if enabled).
2. Launch the client, enter the **registration code** from the email, then **Register**.
3. Authenticate with the **username** and the **password** you set during activation.
4. The client connects to AWS **streaming/authentication gateways**, then launches your dedicated desktop session.

### 8) Validate Connectivity and Persistence

- Open a browser inside the Workspace (e.g., Firefox) and browse the internet.
  Egress flows through **NAT Gateways** in the **WEB** subnets established by A4LVPC.
- Log off, then reconnect (optionally via **Web access** if enabled) to confirm your **desktop state persists**.

### 9) Clean Up

1. In **WorkSpaces**, **Delete** the Workspace (status becomes **Terminating**, then disappears).
2. In **Directories**, **Deregister** the directory, then **Delete** it.
3. In **CloudFormation**, **Delete** the **A4LVPC** stack to remove all networking resources.

## Notes, Pitfalls, and Tips

- **Subnet selection** during directory registration can be confusing when names are hidden—use **CIDRs** to match APP subnets.
- **Free-tier bundle** availability changes by region and time—choose any **Windows Standard** free-tier option shown.
- **Auto-Stop** is cost-friendly for labs; **AlwaysOn** is predictable for steady use.
- **Device Access**: enabling **Linux** or **Web** access is per-directory; if you switch directories, re-check this setting.
- **Security posture**: this lab uses permissive networking to simplify learning. Lock down SGs and access paths in production.

## Updated Notes (2025)

- **Bundles/OS**: You may see **Windows 11** desktop experience bundles instead of Windows 10/Server-based images. Select any **free-tier eligible “Standard”** Windows bundle the console offers.
- **Streaming Protocols**: Newer launches default to **WSP (WorkSpaces Streaming Protocol)** where available, replacing or complementing PCoIP. WSP generally provides better WAN resiliency and features. No action needed for this lab.
- **Device Access UI**: The “All platforms / Device access” control remains the place to enable **Linux** and **Web** access; the UI wording may vary slightly.
- **Directory Choice**: **Simple AD** is fine for labs. For production, prefer **AWS Managed Microsoft AD** or **AD Connector** (to on-prem AD) for full compatibility and support.
