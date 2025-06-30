# ☁️ Cloud Security with AWS IAM

This project demonstrates how I implemented cloud access control using AWS Identity and Access Management (IAM). It simulates a real-world scenario where interns and developers require secure, role-based access to different environments. I used EC2 for infrastructure and IAM for granular access control.

---

## 🔎 Project Overview

- Launched EC2 instances for production and development
- Created custom IAM policy with least privilege access
- Set up AWS account alias for cleaner login URLs
- Created IAM users and groups with scoped access
- Validated permissions through live tests and IAM Policy Simulator

---

## 🔧 Tools & Services Used

- AWS EC2  
- AWS IAM  
- IAM Policy Simulator  
- AWS Management Console  

---

## 📆 Step-by-Step Breakdown

### Step 1 & 2: Launching EC2 Instances for Production and Development Environments

To begin this project, I logged into the AWS Management Console and accessed the EC2 service. I launched two EC2 instances:

- **Production Instance**: Named `Mywork-prod-Mounika` and tagged with `Env=production`, representing the live system used by end users.
- **Development Instance**: Named `mywork-dev-Mounika` and tagged with `Env=development`, used for writing, testing, and debugging code without impacting production.

Both instances were launched using a Free Tier-eligible Amazon Machine Image (AMI) and selected instance type `t2.micro` to avoid any charges. Each instance was also deployed in the AWS Region closest to my location for optimal performance.

Creating these two isolated environments allowed me to apply environment-specific access controls later in the project.

```html
<img src="https://github.com/addula-mounika12/aws-iam-cloud-security/blob/main/assets/Screenshot%202025-06-20%20160023.png?raw=true" height="80%" width="80%" alt="EC2 instance setup"/>
```

---

### Step 3: Creating an IAM Policy to Control Access

Next, I focused on creating a custom IAM policy to ensure an intern could interact only with the development environment. This was essential to applying the principle of least privilege.

In the IAM console, I navigated to **Policies > Create policy** and switched to the JSON editor to define a policy that:
- Allows full EC2 actions only on instances tagged with `Env=development`
- Grants read-only (describe) access on all EC2 instances
- Explicitly denies tag creation and deletion on any instances

I named this policy `MyWorkDevEnvironmentPolicy`, provided a clear description, and saved it for use with IAM groups.

```html
<img src="https://github.com/yourusername/aws-iam-cloud-security/assets/iam-policy-json.png" height="80%" width="80%" alt="Custom IAM Policy"/>
```

---

### Step 4: Setting Up an AWS Account Alias

To personalize the AWS login experience and avoid sharing long account IDs, I created a custom AWS account alias. This alias appears in the login URL and makes it easier to share access with interns or other users.

I navigated to the **IAM dashboard > Account Alias**, created a recognizable alias, and verified that the login URL reflected the change.

```html
<img src="https://github.com/yourusername/aws-iam-cloud-security/assets/account-alias.png" height="80%" width="80%" alt="Account Alias Setup"/>
```

---

### Step 5: Creating IAM Users and User Groups

IAM users were created to represent specific individuals such as interns and developers. To avoid manually assigning permissions, I created a **user group** and attached the `MyWorkDevEnvironmentPolicy` to it.

This structure allows all users within the group to inherit permissions automatically. The principle of least privilege was enforced by restricting access to only the development environment.

Login credentials for the IAM user were shared via downloaded `.csv` files, ensuring secure and traceable distribution.

```html
<img src="https://github.com/yourusername/aws-iam-cloud-security/assets/iam-user-group.png" height="80%" width="80%" alt="IAM Users and Groups"/>
```

---

### Step 7: Verifying IAM Permissions in Action

To verify the policy’s effectiveness, I logged in as the IAM user using the new account alias in an incognito browser. I attempted the following actions:

- **On the production instance**: Attempted to stop the instance; received an **Access Denied** error, as expected.
- **On the development instance**: Stopped the instance successfully, confirming access was correctly granted.

This validated that the IAM policy worked as intended based on tags.

```html
<img src="https://github.com/yourusername/aws-iam-cloud-security/assets/verify-permissions.png" height="80%" width="80%" alt="IAM User Permission Check"/>
```

---

### Step 8: Testing Permissions with IAM Policy Simulator

To safely validate permissions, I used the IAM Policy Simulator:

- Accessed the **IAM Policy Simulator** from the root account
- Selected the dev user group
- Simulated actions: `StopInstances` and `DeleteTags`

Initially, both were denied due to default resource settings. I updated the simulator to include the condition:

- `ec2:ResourceTag/Env = development`

After re-running:
- `StopInstances` was **granted** (tag matched)
- `DeleteTags` was **denied** (as per policy)

This confirmed the policy functioned correctly and reinforced the value of simulation before deployment.

```html
<img src="https://github.com/yourusername/aws-iam-cloud-security/assets/policy-simulator.png" height="80%" width="80%" alt="IAM Policy Simulator Output"/>
```

---

## 🧠 Key Takeaways

- Hands-on experience in deploying and managing secure cloud environments
- Learned to implement granular IAM policies based on tagging
- Validated access using both live testing and policy simulation
- Reinforced security best practices such as least privilege and role-based access control

---

## 🎓 What I Learned

- How to distinguish between production and development infrastructure on AWS
- Policy creation and debugging with real-world tagging conditions
- Secure identity lifecycle management using users, groups, and custom permissions
- Role-based access aligned with audit and compliance standards

---

> 🚀 Built and documented by Mounika Addula
