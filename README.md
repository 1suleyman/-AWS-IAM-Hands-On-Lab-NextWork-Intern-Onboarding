# 🛡️ AWS IAM Hands-On Lab: NextWork Intern Onboarding

Welcome to the **NextWork IAM Hands-On Lab**! This project walks through onboarding a new intern into an AWS environment while securely managing access to development and production resources.

By the end of this lab, you'll understand how to:

* Create IAM policies
* Use IAM groups and users
* Configure an AWS account alias
* Test permissions for new users

This lab is ideal for AWS beginners and those wanting hands-on practice with **Identity and Access Management (IAM)** in a safe, sandbox environment.

---

## 📋 Project Overview

| Step                                         | Description                                                                                                                                             |
| -------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Step 1: Deploy EC2 Instances**             | Launch two EC2 instances: one for development, one for production.                                                                                      |
| **Step 2: Create an IAM Policy**             | Create a policy (`NextWorkDevEnvironmentPolicy`) that allows access to **development EC2** instances but restricts production instances.                |
| **Step 3: Create an AWS Account Alias**      | Simplify login URLs by creating a friendly account alias for the AWS Management Console.                                                                |
| **Step 4: Create IAM Users and User Groups** | Set up a **user group** (`nextwork-dev-group`) and **user** (`nextwork-dev-[yourname]`) for the new intern. Attach the development policy to the group. |
| **Step 5: Test Intern Access**               | Log in as the intern and verify: <br>• Cannot access/stop production instances <br>• Can access/manage development instances                            |

---

## 🔑 Key Concepts

### IAM Policies

IAM policies are JSON documents that define **who can do what** in your AWS environment. They contain:

* **Effect:** Allow or Deny
* **Action:** List of AWS actions (e.g., `ec2:StartInstances`)
* **Resource:** The AWS resource(s) affected
* **Condition (optional):** Rules for when the policy applies (e.g., tag-based access)

**Example:**

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "ec2:*",
      "Resource": "*",
      "Condition": {
        "StringEquals": {
          "ec2:ResourceTag/Env": "development"
        }
      }
    },
    {
      "Effect": "Allow",
      "Action": "ec2:Describe*",
      "Resource": "*"
    },
    {
      "Effect": "Deny",
      "Action": [
        "ec2:DeleteTags",
        "ec2:CreateTags"
      ],
      "Resource": "*"
    }
  ]
}
```

This policy:

* **Allows** all EC2 actions **only** on development-tagged instances
* **Allows** all EC2 describe actions (read-only) on any instance
* **Denies** tag creation/deletion on any instance

---

### IAM User Groups

* Collections of users with **shared permissions**
* Policies are attached to groups instead of individual users
* Makes permission management scalable and consistent

**Example:** `nextwork-dev-group` → all interns share the same development access

---

### IAM Users

* Individual AWS accounts for people or applications
* Can log into AWS console or access via CLI/API
* Must belong to a group to inherit permissions

**Example:** `nextwork-dev-[yourname]` → added to `nextwork-dev-group`

---

### AWS Account Alias

* Friendly name for your AWS account login URL
* Default: `https://<account_id>.signin.aws.amazon.com/console/`
* With alias: `https://nextwork-alias.signin.aws.amazon.com/console/`

---

## 🏗️ Step-by-Step Workflow

### 1️⃣ Deploy EC2 Instances

* Launch 2 EC2 instances
* Tag instances:

  * Development: `Env=development`
  * Production: `Env=production`

### 2️⃣ Create IAM Policy

1. Open **IAM → Policies → Create Policy → JSON**
2. Paste `NextWorkDevEnvironmentPolicy` JSON
3. Name: `NextWorkDevEnvironmentPolicy`
4. Description: `IAM Policy for NextWork's development environment`
5. Create Policy

### 3️⃣ Create AWS Account Alias

1. Open **IAM → Dashboard → Account Alias**
2. Enter alias: `nextwork-alias-[yourname]`
3. Create alias

### 4️⃣ Create IAM User & User Group

1. **User Group:**

   * Name: `nextwork-dev-group`
   * Attach: `NextWorkDevEnvironmentPolicy`
2. **User:**

   * Name: `nextwork-dev-[yourname]`
   * Enable **AWS Management Console Access**
   * Add to `nextwork-dev-group`

### 5️⃣ Test Intern Access

* Log in using intern credentials at `https://nextwork-alias-[yourname].signin.aws.amazon.com/console/`
* Verify:

  * ✅ Can start/stop **development instance**
  * ❌ Cannot stop/start **production instance**

---

## 📸 Screenshots

Include your screenshots from each step:

1. IAM Policy JSON editor
2. Account Alias creation
3. User Group creation
4. User creation & login credentials
5. Access tests (both allowed and denied actions)

---

## 💡 Lessons Learned

* Tagging resources is essential for **fine-grained IAM control**
* Using **groups** simplifies permissions for teams
* **Policies** must be tested carefully to prevent accidental access
* Friendly **account aliases** make onboarding new users seamless

---

## 🚀 Optional Steps

* Add more interns to the `nextwork-dev-group`
* Explore **IAM Roles** for cross-account access
* Learn **AWS Organizations** for multi-account setups
* Integrate **MFA** for secure logins

---

## 📝 References

* [AWS IAM Documentation](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html)
* [AWS EC2 Tags](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/Using_Tags.html)
* [AWS IAM Policy Elements](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html)

---

💡 **Pro Tip:** Always test a new user’s access with real tasks — never assume policies work as intended without verification.
