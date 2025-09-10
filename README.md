# 🛡️ AWS IAM Hands-On Lab: NextWork Intern Onboarding

This project guides you through **onboarding a new intern into an AWS environment** while securely managing access to development and production resources. By the end, the intern will be able to access **development EC2 instances** but **not production**, using **IAM policies, groups, and users**.

---

## 📋 Project Steps

### **Step 1: Deploy EC2 Instances**

**Goal:** Launch separate development and production servers.

#### Instructions:

1. Log in to your **AWS Management Console**.
2. Navigate to **EC2 → Instances → Launch instances**.
3. instance 1 name = nextwork-prod-suleyman
5. instance 2 name = nextwork-dev-suleyman
6. Launch **two instances** (e.g.,
- t2.micro for free-tier testing
- Amazon Linux 2023
- Key pair (login), select Proceed without a key pair.
- used default network and storage settings):

   * **Development:** Tag with `Env=development`
   * **Production:** Tag with `Env=production`

💡 **Tip:** Tags are essential for **fine-grained IAM permissions** later.

✅ **Checkpoint:** Two EC2 instances created, properly tagged.

<img width="350" height="182" alt="Screenshot 2025-09-10 at 14 57 55" src="https://github.com/user-attachments/assets/7b1bc3ee-a264-4264-80a8-cc8f00b3492f" />

---

### **Step 2: Create IAM Policy**

**Goal:** Restrict intern access to development instances only.

#### Instructions:

1. Open **IAM → Policies → Create Policy → JSON**
2. Paste the following JSON:

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
💡Let's unpack this spicy policy
This policy allows some actions (like starting, stopping, and describing EC2 instances) for instances tagged with "Env = development" while denying the ability to create or delete tags for all instances.


💡 Extra for Experts: how are JSON policies structured?
Version
‍This means 2012-10-17 is the date of the latest policy version. This tells you whether the policy is up to date with the latest standards and practices.

‍Statement
‍The main part of the policy structure and defines a list of permissions.

‍Effect
‍This can have two values - either Allow or Deny - to indicate whether the policy allows or denies a certain action. Deny has priority. Looking at the first statement, "Effect": "Allow" means this statement is trying to allow for an action.

‍Action
‍A list of the actions that the policy allows or denies. In this case, "Action": "ec2:*" means all actions that you could possibly take on EC2 instances are allowed. Woohoo!

‍Resource
‍Which resources does this policy apply to? Specifying "*" means all resources within the defined scope (see the next point).

Condition Block (optional)
‍The circumstances under which the policy is in action. In this case, the condition is that the resource is tagged Env - development. This means specifying "Resource": "*" in the line above means all resources with the Env - development tag are impacted by your statement.

3. Name the policy: `NextWorkDevEnvironmentPolicy`
4. Description: `IAM Policy for NextWork's development environment`
5. Click **Create Policy**

💡 **Tip:**

* `Allow` = actions permitted
* `Deny` = actions explicitly blocked
* `Condition` = only applies to development-tagged instances

✅ **Checkpoint:** Policy created successfully.

<img width="384" height="53" alt="Screenshot 2025-09-10 at 15 04 28" src="https://github.com/user-attachments/assets/2161d2db-a955-415f-8f41-39bce072cbdb" />

---

### **Step 3: Create AWS Account Alias**

**Goal:** Simplify login URLs for new interns.

#### Instructions:

1. Go to **IAM → Dashboard → Account Alias**

💡 What is an Account Alias? Why are we creating one?
Once you onboard new users into your AWS account (which we'll do for our new NextWork intern), these new users get access through a unique log-in URL for your account.

An Account Alias is a friendly name for your AWS account that you can use instead of your account ID (which is usually a bunch of digits) to sign in to the AWS Management Console.

Your AWS account's sign-in page has this URL by default: https://Your_Account_ID.signin.aws.amazon.com/console/

If you create an AWS account alias for your AWS account ID, your sign-in page URL looks more like: https://Your_Account_Alias.signin.aws.amazon.com/console/

You would create an alias to make it easier to remember and share your AWS console's login URL with others e.g. NextWork's new intern. Companies often use this so that their AWS account sign-in page is more user-friendly for their users!

3. Enter alias: `lebroooonjamess`
4. Click **Create alias**

💡 **Tip:** Your login URL now becomes:
`https://lebroooonjamess.signin.aws.amazon.com/console/`

✅ **Checkpoint:** Account alias created.

<img width="385" height="50" alt="Screenshot 2025-09-10 at 15 14 38" src="https://github.com/user-attachments/assets/825c24db-d08c-48dc-b9a7-439581d20f03" />

---

### **Step 4: Create IAM User & User Group**

**Goal:** Add the intern to the environment securely.

#### Instructions:

**1️⃣ Create User Group:**

* Name: `nextwork-dev-group`
* Attach Policy: `NextWorkDevEnvironmentPolicy`

**2️⃣ Create User:**

* Name: `nextwork-dev-lebronjr`
* Enable **AWS Management Console Access**
* Assign password and **force reset at first login**
* Add user to **nextwork-dev-group**

💡 **Tip:** Groups make permission management **scalable** — add multiple interns without editing each policy.

✅ **Checkpoint:** User and group created successfully.

<img width="331" height="48" alt="Screenshot 2025-09-10 at 15 16 35" src="https://github.com/user-attachments/assets/7dae65ac-1d72-464d-8aa4-0e779249c64e" />

<img width="355" height="45" alt="Screenshot 2025-09-10 at 15 19 20" src="https://github.com/user-attachments/assets/e5d55e58-6ce9-421d-96ed-07fda9f43afd" />

<img width="197" height="53" alt="Screenshot 2025-09-10 at 15 19 35" src="https://github.com/user-attachments/assets/f507c013-e0e3-47da-bb19-fdb2bfef80ae" />

---

### **Step 5: Test Intern Access**

**Goal:** Verify permissions work as intended.

#### Instructions:

1. Log in using intern credentials at:
   `https://lebroooonjamess.signin.aws.amazon.com/console`
2. Test EC2 access:

   * ✅ Can **start/stop development instance**

<img width="541" height="35" alt="Screenshot 2025-09-10 at 15 23 40" src="https://github.com/user-attachments/assets/8c47a273-e23b-4997-88be-710a31dbc95a" />

<img width="475" height="39" alt="Screenshot 2025-09-10 at 15 25 10" src="https://github.com/user-attachments/assets/bbf20330-00f3-4949-8ce2-e63d47eddc5a" />

   * ❌ Cannot **start/stop production instance**

<img width="260" height="64" alt="Screenshot 2025-09-10 at 15 24 17" src="https://github.com/user-attachments/assets/35d3f3af-c6d0-449e-b2de-6a2a169da22d" />

💡 **Tip:** Always test **both allowed and denied actions** to confirm the policy works.

✅ **Checkpoint:** Intern access verified.

---

## 📸 Screenshots

Include screenshots at each checkpoint:

* IAM Policy JSON editor
* Account Alias creation
* User Group creation
* User creation & login credentials
* EC2 access test (allowed vs. denied actions)

---

## 💡 Notes / Tips

* **Resource tagging** = essential for fine-grained IAM control
* **Groups** simplify permissions for multiple users
* **Policies** should always be tested to avoid accidental access
* **Account aliases** make onboarding seamless and professional
* Consider adding **MFA** for extra security

---

## 🚀 Optional Steps

* Add more interns to `nextwork-dev-group`
* Explore **IAM Roles** for cross-account access
* Learn **AWS Organizations** for multi-account setups

---

## 📝 References

* [AWS IAM Documentation](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html)
* [AWS EC2 Tags](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/Using_Tags.html)
* [AWS IAM Policy Elements](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html)

---

💡 **Pro Tip:** Never assume IAM policies work—**always test with real actions** using the new user’s credentials.
