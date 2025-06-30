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

- **Production Instance**: Named `Mywork-prod-Mounika` and tagged with `Env=production`
- **Development Instance**: Named `mywork-dev-Mounika` and tagged with `Env=development`

Both instances used a Free Tier-eligible AMI and `t2.micro` type. Creating two isolated environments allowed me to apply role-based access using IAM policies later.

![EC2 instance setup](https://github.com/addula-mounika12/aws-iam-cloud-security/blob/78d0f6db6822d95d69253558d36fdec967393212/assets/Screenshot%202025-06-20%20160023.png?raw=true)


---

### Step 3: Creating an IAM Policy to Control Access

Next, I focused on creating a custom IAM policy to ensure an intern could interact only with the development environment. This was essential to applying the principle of least privilege.

In the IAM console, I navigated to **Policies > Create policy** and switched to the JSON editor to define a policy that:
- Allows full EC2 actions only on instances tagged with `Env=development`
- Grants read-only (describe) access on all EC2 instances
- Explicitly denies tag creation and deletion on any instances

This policy was named `MyWorkDevEnvironmentPolicy` and saved for later use with IAM groups.

![IAM Policy JSON Editor](https://github.com/addula-mounika12/aws-iam-cloud-security/blob/main/assets/Screenshot%202025-06-20%20163207.png?raw=true)
---

### Step 4: Setting Up an AWS Account Alias

To personalize the AWS login experience and avoid sharing long account IDs, I created a custom AWS account alias. This alias appears in the login URL and makes it easier to share access with interns or other users.

I navigated to the **IAM dashboard > Account Alias**, created a recognizable alias, and verified that the login URL reflected the change.

![Account Alias Setup](https://github.com/addula-mounika12/aws-iam-cloud-security/blob/4f063d96e5f8790dcf50b09bc9d41b21912c66be/assets/Screenshot%202025-06-20%20165645.png?raw=true)

---

### Step 5: Creating IAM Users and User Groups

To securely manage access within the AWS environment, I utilized the **Identity and Access Management (IAM)** service to create both IAM users and IAM groups. Each IAM user represents a unique identity—such as an intern or developer—who requires specific access to AWS resources.

Rather than assigning permissions to each user individually, I followed AWS best practices and created an **IAM group**. This group acts as a container for multiple users who share the same access requirements. It simplifies permission management, enforces consistency, and aligns with the principle of **least privilege**.

Once the group was created, I attached the previously defined custom policy, `MyWorkDevEnvironmentPolicy`, to it. This policy was specifically designed to:
- ✅ Grant full access to EC2 instances tagged with `Env=development`
- 🚫 Deny access to production resources and tag modification actions

By associating this policy with the group, every IAM user added to the group automatically inherits the defined permissions. This role-based access structure makes it easy to onboard new users while maintaining security boundaries between environments.

For secure delivery of login credentials, AWS provides two options:
- **Emailing** login instructions directly to the user
- **Downloading a `.csv` file** that contains the user’s credentials (Access key ID, Secret access key, and Console sign-in URL)

For this project, I chose to download the `.csv` file and securely distribute it to the intern, ensuring traceable access and audit readiness.

![IAM Users and Groups](https://github.com/addula-mounika12/aws-iam-cloud-security/blob/ec2a77e37a2c65481efe0bdc0fee6de0f206f625/assets/Screenshot%202025-06-30%20172449.png?raw=true)



---

### Step 6: Verifying IAM Permissions in Action

Once the IAM users and groups were created and permissions assigned via the `MyWorkDevEnvironmentPolicy`, it was essential to validate whether the policy was functioning as intended. To do this, I simulated a real intern login experience.

I began by opening the AWS Console sign-in URL (which now included my custom account alias) in an incognito browser window. This step mimics how a new user would access the environment for the first time, ensuring no cached credentials interfered. Using the IAM credentials provided in the .csv file, I logged in as the `dev` IAM user.

Immediately upon logging in, I noticed **“Access Denied”** messages across several panels on the AWS dashboard. This was a positive sign, indicating that the IAM policy was correctly enforcing restrictions on services the user was not permitted to access.

To thoroughly test the access control, I navigated to the **EC2 service**, making sure I selected the same AWS Region in which both the production and development instances were running.

#### 🔒 Attempting Unauthorized Action on Production EC2 Instance

I first selected the EC2 instance tagged with `Env=production` and attempted to stop it by navigating through:
> **Actions → Instance State → Stop**

As expected, AWS returned an **authorization error**, indicating that the IAM user did **not** have the permission to perform this action. This confirmed that the policy successfully denied access to production resources, aligning with the least privilege model.

![Access Denied - Production](https://github.com/addula-mounika12/aws-iam-cloud-security/blob/8d7b2d52e7db2683bf16c27765806af4951e7632/assets/Screenshot%202025-06-20%20175032.png?raw=true)

#### ✅ Testing Authorized Action on Development EC2 Instance

Next, I selected the EC2 instance tagged `Env=development`. I repeated the same stop action:
> **Actions → Instance State → Stop**

This time, the operation completed successfully. The instance was stopped without any authorization errors, verifying that the IAM user **did** have appropriate permissions for the development environment.

![Access Allowed - Development](https://github.com/addula-mounika12/aws-iam-cloud-security/blob/a1ffc13c5efd1cf77002a8f237b229f38657ea39/assets/Screenshot%202025-06-20%20175616.png?raw=true)

These two test cases demonstrated that the IAM policy enforced **granular, tag-based access control**—allowing actions only on resources with specific attributes, and denying everything else. This reinforced my understanding of **IAM policy conditions** and their practical impact in secure cloud environments.


---

### Step 7: Testing Permissions with IAM Policy Simulator

To further enhance my understanding of AWS Identity and Access Management (IAM), I used the **IAM Policy Simulator** to test user permissions without affecting any real cloud resources. While earlier I tested permissions by attempting actions on live EC2 instances, this approach poses risks and potential disruptions to a production environment.

The IAM Policy Simulator provides a safe, efficient, and scalable way to validate what actions a user or group can perform under specific IAM policies.

I switched back to my **root AWS account**, navigated to the **Policy Simulator** via the IAM dashboard (under the “Tools” section), and selected the `dev` user group to test the permissions defined in the `MyWorkDevEnvironmentPolicy`.

To simulate typical user operations:
- I chose the **EC2 service**
- Selected the actions: `StopInstances` and `DeleteTags`

### 🔍 Initial Results
- Both actions were **denied**, which raised a red flag.
- The issue: The simulation was being run against **all resources (`*`)**, whereas the IAM policy applies **only to resources tagged with `Env=development`**.

### ✅ Fix & Retest
- I updated the simulation with a resource condition:
  - **Key**: `ec2:ResourceTag/Env`
  - **Value**: `development`

After re-running the simulation:
- ✅ `StopInstances` was **granted** (as expected)
- 🚫 `DeleteTags` remained **denied** (intended behavior)

This confirmed that the custom IAM policy was functioning exactly as designed.

![IAM Policy Simulator Result](https://github.com/addula-mounika12/aws-iam-cloud-security/blob/835113a92cdba8095265096ca0a47ae66ee84ff3/assets/Screenshot%202025-06-20%20181635.png?raw=true)

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
