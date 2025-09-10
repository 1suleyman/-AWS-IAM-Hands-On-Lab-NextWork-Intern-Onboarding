# 🛡️ AWS IAM Hands-On Lab: NextWork Intern Onboarding

In this project, I onboarded a new intern into our AWS environment while securely managing access to development and production resources. By the end, the intern could access **development EC2 instances** but **not production**, thanks to the **IAM policies, groups, and users** I set up.

---

## 📋 My Project Steps

### **Step 1: Deploy EC2 Instances**

**Goal:** I needed to launch separate development and production servers.

#### What I Did:

1. Logged into my **AWS Management Console**.

2. Navigated to **EC2 → Instances → Launch instances**.

3. Created **two instances**:

   * Instance 1: `nextwork-prod-suleyman`
   * Instance 2: `nextwork-dev-suleyman`

4. Configured them as follows:

   * t2.micro for free-tier testing
   * Amazon Linux 2023
   * Key pair: I selected **Proceed without a key pair**
   * Used default network and storage settings

5. Tagged them appropriately:

   * **Development:** `Env=development`
   * **Production:** `Env=production`

💡 **Tip I learned:** Tags are crucial for **fine-grained IAM permissions** later.

✅ **Checkpoint:** My two EC2 instances were up and properly tagged.

<img width="350" height="182" alt="Screenshot 2025-09-10 at 14 57 55" src="https://github.com/user-attachments/assets/7b1bc3ee-a264-4264-80a8-cc8f00b3492f" />

---

### **Step 2: Create IAM Policy**

**Goal:** I needed to restrict the intern’s access to **development instances only**.

#### What I Did:

1. Opened **IAM → Policies → Create Policy → JSON**
2. Pasted this JSON:

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
      "Action": ["ec2:DeleteTags","ec2:CreateTags"],
      "Resource": "*"
    }
  ]
}
```

💡 **What I learned about this policy:**

* The first statement allows actions like starting, stopping, and describing **EC2 instances tagged with `Env=development`**.
* The second statement lets the user describe all EC2 resources.
* The third statement denies creating or deleting tags anywhere.

**Extra tip I discovered:**

* **Version** = policy version date
* **Statement** = main body of the policy
* **Effect** = Allow or Deny (Deny takes priority)
* **Action** = what the user can or cannot do
* **Resource** = what the policy applies to
* **Condition** = optional, limits the policy to resources with specific tags

3. Named the policy: `NextWorkDevEnvironmentPolicy`
4. Description: `IAM Policy for NextWork's development environment`
5. Clicked **Create Policy**

💡 **Tip:**

* `Allow` = permitted actions
* `Deny` = explicitly blocked actions
* `Condition` = only applies to **development-tagged instances**

✅ **Checkpoint:** Policy created successfully.

<img width="384" height="53" alt="Screenshot 2025-09-10 at 15 04 28" src="https://github.com/user-attachments/assets/2161d2db-a955-415f-8f41-39bce072cbdb" />

---

### **Step 3: Create AWS Account Alias**

**Goal:** I wanted to simplify the login URL for my intern.

#### What I Did:

1. Went to **IAM → Dashboard → Account Alias**
2. Entered the alias: `lebroooonjamess`
3. Clicked **Create alias**

💡 **What I learned:**

* Without an alias, the login URL is:
  `https://Your_Account_ID.signin.aws.amazon.com/console/`
* With my alias, it became easier to remember:
  `https://lebroooonjamess.signin.aws.amazon.com/console/`

✅ **Checkpoint:** Account alias created successfully.

<img width="385" height="50" alt="Screenshot 2025-09-10 at 15 14 38" src="https://github.com/user-attachments/assets/825c24db-d08c-48dc-b9a7-439581d20f03" />

---

### **Step 4: Create IAM User & User Group**

**Goal:** I wanted to securely add the intern to our environment.

#### What I Did:

**1️⃣ Created User Group:**

* Name: `nextwork-dev-group`
* Attached Policy: `NextWorkDevEnvironmentPolicy`

**2️⃣ Created User:**

* Name: `nextwork-dev-lebronjr`
* Enabled **AWS Management Console Access**
* Assigned password and **forced reset at first login**
* Added the user to **nextwork-dev-group**

💡 **What I learned:** Groups make permission management **scalable** — I can add multiple interns without editing each policy.

✅ **Checkpoint:** User and group created successfully.

<img width="331" height="48" alt="Screenshot 2025-09-10 at 15 16 35" src="https://github.com/user-attachments/assets/7dae65ac-1d72-464d-8aa4-0e779249c64e" />

<img width="355" height="45" alt="Screenshot 2025-09-10 at 15 19 20" src="https://github.com/user-attachments/assets/e5d55e58-6ce9-421d-96ed-07fda9f43afd" />

<img width="197" height="53" alt="Screenshot 2025-09-10 at 15 19 35" src="https://github.com/user-attachments/assets/f507c013-e0e3-47da-bb19-fdb2bfef80ae" />

---

### **Step 5: Test Intern Access**

**Goal:** I needed to verify that permissions worked as intended.

#### What I Did:

1. Logged in using intern credentials at:
   `https://lebroooonjamess.signin.aws.amazon.com/console`
2. Tested EC2 access:

   * ✅ I could **start/stop development instance**

<img width="541" height="35" alt="Screenshot 2025-09-10 at 15 23 40" src="https://github.com/user-attachments/assets/8c47a273-e23b-4997-88be-710a31dbc95a" />

<img width="475" height="39" alt="Screenshot 2025-09-10 at 15 25 10" src="https://github.com/user-attachments/assets/bbf20330-00f3-4949-8ce2-e63d47eddc5a" />

* ❌ I could **not start/stop production instance**

<img width="260" height="64" alt="Screenshot 2025-09-10 at 15 24 17" src="https://github.com/user-attachments/assets/35d3f3af-c6d0-449e-b2de-6a2a169da22d" />

💡 **Tip:** I always tested **both allowed and denied actions** to confirm the policy works.

✅ **Checkpoint:** Intern access verified successfully.

---

## 📸 My Screenshots

I took screenshots at each checkpoint:

* IAM Policy JSON editor
* Account Alias creation
* User Group creation
* User creation & login credentials
* EC2 access test (allowed vs. denied actions)

---

## 💡 Notes / Tips I Learned

* **Resource tagging** = essential for fine-grained IAM control
* **Groups** simplify permissions for multiple users
* **Policies** should always be tested to avoid accidental access
* **Account aliases** make onboarding seamless and professional
* Consider adding **MFA** for extra security

---

## 🚀 Optional Steps I Could Try Next

* Add more interns to `nextwork-dev-group`
* Explore **IAM Roles** for cross-account access
* Learn **AWS Organizations** for multi-account setups

---

## 📝 References I Used

* [AWS IAM Documentation](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html)
* [AWS EC2 Tags](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/Using_Tags.html)
* [AWS IAM Policy Elements](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html)

---

💡 **Pro Tip I Learned:** Never assume IAM policies work — **always test with real actions** using the new user’s credentials.
