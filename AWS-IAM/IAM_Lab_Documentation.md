# AWS IAM Hands-On Practice Lab Documentation

This document outlines and explains the AWS Identity and Access Management (IAM) lab exercises conducted and captured in `screenshots.docx`.

---

## Lab Overview & Key Concepts Practiced

The lab demonstrates foundational and advanced AWS IAM principles:
1. **Default Implicit Deny**: An IAM user with no policies attached cannot access or view any AWS resources.
2. **AWS Managed Policies**: Granting broad predefined service access (`AmazonEC2FullAccess`, `AmazonEC2ReadOnlyAccess`).
3. **Resource Provisioning under IAM Users**: Creating resources (EC2 instance) using IAM user credentials rather than the root account.
4. **IAM User Groups**: Scaling user management and permissions by grouping users (`devops_team` and `qa_team`).
5. **Customer Managed Policies & Resource-Level Permissions**: Defining granular JSON policies restricting actions (`ec2:StartInstances`, `ec2:StopInstances`) to a specific instance ARN.
6. **Least Privilege Enforcement**: Verifying that unpermitted actions (`ec2:TerminateInstances`) are blocked with authorization errors.

---

## Detailed Step-by-Step Breakdown

### Step 1: Default Implicit Deny & Zero Permissions
- **User Created**: `ram` (`arn:aws:iam::459532536509:user/ram`)
- **Initial State**: No policies attached (Permissions policies: `0`).
- **Observation**:
  - When logged into the AWS Console as `ram`, navigating to the EC2 Dashboard shows **Access Denied** across all widgets (Instances, Security Groups, Volumes, Elastic IPs, Key Pairs, etc.).
  - API operations like `health:DescribeEvents`, `ec2:DescribeAvailabilityZones`, and `ec2:DescribeAccountAttributes` fail immediately.

---

### Step 2: Granting AWS Managed Policy (`AmazonEC2FullAccess`) & Launching EC2
- **Action**: Attached AWS managed policy `AmazonEC2FullAccess` directly to user `ram`.
- **Testing**:
  - Logged into the AWS Console as `ram`.
  - Permissions successfully allowed navigating EC2 and launching a new virtual server.
- **Resource Created**:
  - **Name**: `RAM_SERVER`
  - **Instance ID**: `i-0abd6ed652b1d77f3`
  - **Instance Type**: `t3.micro`
  - **Availability Zone**: `us-east-1c`
  - **Public IPv4**: `54.91.222.173`

---

### Step 3: Organizing Users with IAM User Groups
To manage permissions at scale rather than attaching policies to individual users:
- **Created Users**:
  - `Anshu`
  - `kiran`
  - `madhu`
  - `ram`
- **Created Groups & Assignments**:
  - **`devops_team`**: Added `kiran` and `ram`
  - **`qa_team`**: Added `Anshu` and `madhu`
- **Concept Validated**: Group-based access control (RBAC) allows centralized permission management, ensuring team members inherit policies automatically upon joining.

---

### Step 4: Crafting a Custom Policy with Fine-Grained Resource Controls
- **Action**: Removed full access and tested transition through `AmazonEC2ReadOnlyAccess`.
- **Customer Managed Policy Created**: `mc-ec2-start-stop-instance`
- **Policy JSON**:
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "VisualEditor0",
      "Effect": "Allow",
      "Action": [
        "ec2:StartInstances",
        "ec2:StopInstances"
      ],
      "Resource": "arn:aws:ec2:us-east-1:459532536509:instance/i-0abd6ed652b1d77f3"
    },
    {
      "Sid": "VisualEditor1",
      "Effect": "Allow",
      "Action": "ec2:DescribeInstances",
      "Resource": "*"
    }
  ]
}
```
- **Analysis of Policy**:
  - `VisualEditor0`: Restricts `ec2:StartInstances` and `ec2:StopInstances` exclusively to the single instance ARN `arn:aws:ec2:us-east-1:459532536509:instance/i-0abd6ed652b1d77f3`. The user cannot start or stop any other instance.
  - `VisualEditor1`: Grants `ec2:DescribeInstances` on `*` so the user can view instance lists and statuses in the AWS console without errors.

---

### Step 5: Testing Least Privilege & Policy Boundaries
- **Action**: Attached `mc-ec2-start-stop-instance` to user `ram` and logged in.
- **Test 1: Stop Instance**:
  - User selected `RAM_SERVER` and triggered "Stop instance".
  - **Result**: Successful (`Instance state: Stopping`).
- **Test 2: Terminate Instance**:
  - User selected `RAM_SERVER` and attempted "Terminate instance".
  - **Result**: **Failed with Authorization Error**:
    > *Failed to terminate (delete) an instance: You are not authorized to perform this operation. User: `arn:aws:iam::459532536509:user/ram` is not authorized to perform: `ec2:TerminateInstances` on resource: `arn:aws:ec2:us-east-1:459532536509:instance/i-0abd6ed652b1d77f3` because no identity-based policy allows the `ec2:TerminateInstances` action.*
- **Outcome**: Confirms the Principle of Least Privilege—the user can manage the lifecycle (Start/Stop) as required by their role, but is strictly blocked from accidental or unauthorized deletion (`Terminate`).

---

## Summary of Practice Takeaways
| Scenario | Policy Applied | Permitted Actions | Blocked Actions |
| :--- | :--- | :--- | :--- |
| **Initial User** | None (0 Policies) | None | All EC2 actions (Implicit Deny) |
| **Admin Provisioning** | `AmazonEC2FullAccess` | Launch, View, Modify, Terminate | Non-EC2 services (unless configured) |
| **Team Scaling** | IAM Groups (`devops_team`, `qa_team`) | Inherited group policies | Actions outside group policy scope |
| **Fine-Grained Security** | Custom `mc-ec2-start-stop-instance` | `DescribeInstances`, `StartInstances`, `StopInstances` on specific ARN | `TerminateInstances`, modifying other instances |
