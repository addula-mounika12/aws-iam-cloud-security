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

The first step in securing a cloud environment is establishing a functional infrastructure with clearly defined environments. In this project, I achieved this by launching two **Amazon EC2 instances**—one for the **production** environment and one for the **development** environment. This separation is a cloud best practice that enables fine-grained access control, safer testing, and more resilient application management.

I began by logging into the **AWS Management Console**, navigating to the **EC2 service**, and selecting the AWS Region geographically closest to my location to optimize latency and resource availability.

#### 🟢 Production EC2 Instance
- Instance Name: `Mywork-prod-Mounika`
- Tag: `Key = Env`, `Value = production`
- Purpose: Represents the **live environment** used by end users, simulating a real-world production system.
- Configuration:
  - **AMI**: Selected a Free Tier-eligible Amazon Linux 2 image
  - **Instance Type**: `t2.micro` to stay within Free Tier and minimize cost
  - **Tags**: Added to distinguish this instance as a production asset

#### 🛠️ Development EC2 Instance
- Instance Name: `mywork-dev-Mounika`
- Tag: `Key = Env`, `Value = development`
- Purpose: Serves as a **sandbox environment** for writing, testing, and debugging code without impacting the production system.
- Configuration mirrored the production setup (AMI and instance type) to ensure compatibility and performance parity

By applying environment-specific **tags**, I set the foundation for IAM policies to differentiate access based on the instance's purpose. This tagging would later allow me to apply **least privilege access** to ensure that IAM users could only interact with the appropriate instance.

This step was crucial in demonstrating the ability to design scalable, role-separated infrastructure and paved the way for enforcing secure access using IAM.

![EC2 instance setup](https://github.com/addula-mounika12/aws-iam-cloud-security/blob/78d0f6db6822d95d69253558d36fdec967393212/assets/Screenshot%202025-06-20%20160023.png?raw=true)

---

### Step 3: Creating an IAM Policy to Control Access

After successfully launching the production and development EC2 instances, the next step was to ensure **controlled, role-based access** to these resources. In a real-world cloud environment, it's critical to prevent unauthorized actions, especially in production systems. To simulate this scenario, I created a **custom IAM policy** that would allow an intern to interact only with the **development** environment while blocking all interaction with the **production** instance.

I navigated to the **IAM console**, selected **Policies** from the sidebar, and chose **Create Policy**. To achieve fine-grained control, I switched to the **JSON editor**, which provides a structured format for defining permission rules programmatically.

The policy was written to enforce the **principle of least privilege**, and included the following logic:

- ✅ **Full EC2 permissions** (`ec2:*`) were allowed, but **only** on instances tagged with `Env=development`. This ensures the intern could perform necessary operations like start, stop, and reboot on the development instance.
- 🔍 **Read-only permissions** (`ec2:Describe*`) were granted on **all** EC2 resources, providing visibility without the ability to modify.
- 🚫 **Explicit deny** permissions were set for sensitive actions like `CreateTags` and `DeleteTags`, further reducing the risk of accidental changes to critical metadata.

Once the policy was defined, I reviewed it carefully and saved it with the name:  
> `MyWorkDevEnvironmentPolicy`

I also added a clear description to explain its purpose for future auditing and maintenance. This custom policy would later be attached to an IAM group, ensuring any user in that group automatically inherits these scoped permissions.

This step highlighted the power of **resource-level access control** using tags and the flexibility of AWS IAM for designing secure, scalable access frameworks.

![IAM Policy JSON Editor](https://github.com/addula-mounika12/aws-iam-cloud-security/blob/main/assets/Screenshot%202025-06-20%20163207.png?raw=true)



---

### Step 4: Setting Up an AWS Account Alias

To improve the clarity and usability of the AWS login process—especially for IAM users like interns or temporary team members—I created a **custom AWS account alias**. By default, AWS generates a complex sign-in URL containing a 12-digit account ID, which is difficult to remember and doesn’t reflect any organizational identity.

Creating a recognizable alias transforms this long numeric login link into a user-friendly URL, making it easier to share and access without compromising on security.

#### 🔧 Why It's Useful:
- Makes AWS sign-in URLs easier to read and remember
- Enhances professionalism and branding
- Helps during onboarding by simplifying credential sharing

#### 🛠️ Steps Taken:
1. Navigated to the **IAM Dashboard** in the AWS Console.
2. Clicked on the **Account Alias** option from the left-hand menu.
3. Entered a custom, meaningful alias that represents my AWS environment.
4. Saved the changes and tested the new sign-in URL.
This step, although minor in setup time, significantly improves the **user experience** for IAM users and contributes to a clean and organized cloud administration process.

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
