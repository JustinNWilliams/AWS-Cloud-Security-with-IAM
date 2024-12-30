# Cloud Security with AWS IAM

## What is AWS IAM?
![image](https://github.com/user-attachments/assets/a6c8ef97-881f-4ca3-b605-0c8f208f1f65)

**AWS Identity and Access Management (IAM)** is Amazon's service for managing access to AWS resources. It allows you to:
- Create and manage users and groups.
- Define permissions.
- Control access to AWS services and resources.

In this project, I used AWS IAM to implement secure access policies for a development environment while restricting access to production resources.

![image](https://github.com/user-attachments/assets/56e1c34d-1433-4c0f-a25e-18260efac3b3)

---

## Table of Contents
1. [How I Used AWS IAM in This Project](#how-i-used-aws-iam-in-this-project)
2. [Tags](#tags)
3. [IAM Policies](#iam-policies)
   - [My JSON Policy](#my-json-policy)
4. [Account Alias](#account-alias)
5. [IAM Users and User Groups](#iam-users-and-user-groups)
6. [Logging in as an IAM User](#logging-in-as-an-iam-user)
7. [Testing IAM Policies](#testing-iam-policies)
   - [Stopping the Production Instance](#stopping-the-production-instance)
   - [Stopping the Development Instance](#stopping-the-development-instance)

---

## How I Used AWS IAM in This Project
For this project:
- I created a policy that allows access to the **development environment** while restricting access to the **production environment**.
- I assigned this policy to a user group and added an intern to the group.
- To ensure everything worked as intended, I tested the permissions by attempting to stop both production and development instances.

---

## Tags
**Tags** are labels used to differentiate AWS resources.  
In this project, I tagged my EC2 instances with the key `Env` and the values:
- **Production**
- **Development**

To add tags, here are the steps I followed:
1. Logged in to the AWS Management Console and navigated to the **EC2 service**.
2. Clicked **Launch Instances** to create an instance.
3. Under the **Name and Tags** section:
   - Added a tag with **Key**: `Env` and **Value**: `production` for the production instance.
   - Repeated the same steps for the development instance, but set the **Value** to `development`.
4. Launched both instances.

![Screenshot 2024-12-30 053730](https://github.com/user-attachments/assets/bf28ad5b-4ca7-4006-9ae4-614ae0714929)
![Screenshot 2024-12-30 053751](https://github.com/user-attachments/assets/d1cfaae4-161b-4361-8a4e-77a92f736f63)

---

## IAM Policies
**IAM Policies** are rules that define what users can and cannot do with AWS resources.

### The Policy I Set Up
I created an IAM policy in JSON format with the following rules:
- **Effect**: Specifies whether to allow or deny access.
- **Action**: Lists the actions the policy allows or denies. In this case, I set the action to `"*"`, meaning all actions are allowed for the defined resource.
- **Resource**: Specifies which resource(s) the policy applies to.

This policy allows full access to **development instances** while restricting access to **production instances**.

Here are the steps I followed to create the policy:
1. Navigated to the **IAM console** and selected **Policies** from the left-hand menu.
2. Clicked **Create Policy** and switched to the **JSON editor**.
3. Pasted the JSON policy (see below) into the editor.
4. Named the policy `NextWorkDevEnvironmentPolicy` and clicked **Create Policy**.

![Screenshot 2024-12-30 054544](https://github.com/user-attachments/assets/5735a82b-128e-4592-9305-4b769d6cffb8)

---

### My JSON Policy
Below is the JSON policy I created for this project:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "*",
      "Resource": "arn:aws:ec2:region:account-id:instance/development-instance-id"
    },
    {
      "Effect": "Deny",
      "Action": "*",
      "Resource": "arn:aws:ec2:region:account-id:instance/production-instance-id"
    }
  ]
}
```

## Account Alias
An **Account Alias** is a nickname for your AWS account that makes the sign-in URL easier to remember and share.

I created an account alias, which updated my login URL to:  
**https://juwillia.signin.aws.amazon.com/console**

### Steps to create the alias:
1. Navigated to the **IAM dashboard**.
2. Clicked **Create** under the **Account Alias** section.
3. Entered `nextwork-alias-myname` (replacing *myname* with my name).
4. Clicked **Create Alias**.

![Screenshot 2024-12-30 055349](https://github.com/user-attachments/assets/7626b928-ae4c-4520-b44f-26e1af43cde1)


---

## IAM Users and User Groups

### Users
**IAM Users** are individuals or applications that need access to AWS services.

### User Groups
**IAM User Groups** are collections of users that share the same permissions.

I created a user group for this project and attached my custom policy to it.  
This ensures that all users in the group have the same permissions as defined in the policy.

### Steps I followed:
1. Navigated to **User Groups** in the IAM console.
2. Clicked **Create Group**.
3. Named the group `nextwork-dev-group` and attached the `NextWorkDevEnvironmentPolicy`.
4. Clicked **Create Group**.
5. Went to **Users** and clicked **Create User**.
6. Named the user `nextwork-dev-myname` and granted **AWS Management Console access**.
7. Added the user to the `nextwork-dev-group` and clicked **Create User**.

![image](https://github.com/user-attachments/assets/5766c493-91e1-4de6-baf4-8971ce9ab9e9)

---

## Logging in as an IAM User
There are two ways to share login credentials with an IAM user:
1. Create an account alias and share the updated sign-in URL.
2. Send credentials to the user and prompt them to change their password on the first login.

![Screenshot 2024-12-30 060017](https://github.com/user-attachments/assets/3a58e004-cb61-4edb-9589-75d69f94e832)

### Steps I followed to test the user login:
1. Logged in using the new user's credentials in an **incognito browser window**.
2. Verified that the user had no access to resources not permitted by the policy.

---

## Testing IAM Policies

### Stopping the Production Instance
I attempted to stop a **production instance**, but it failed with the error:  
**"Failed to stop the instance. You are not authorized to perform this operation."**  
This worked as intended because the policy explicitly denies access to production instances.

### Steps I followed:
1. Logged in as the IAM user created earlier.
2. Navigated to the **EC2 console** and selected the **production instance**.
3. Attempted to stop the instance, which resulted in an error as expected.

![Screenshot 2024-12-30 061017](https://github.com/user-attachments/assets/ee8f3541-e033-4ca6-9cd4-40f6fe8e64ef)


---

### Stopping the Development Instance
Next, I attempted to stop a **development instance**, and it worked without any issues.  
This is because the policy allows full access to development instances.

### Steps I followed:
1. Selected the **development instance** in the EC2 console.
2. Stopped the instance successfully.

![Screenshot 2024-12-30 061201](https://github.com/user-attachments/assets/f9be7b08-38b7-445f-81a9-e4c5cee0fd60)


---

## Summary
In this project, I successfully set up and tested **AWS IAM policies** to manage access to AWS resources securely. Here’s what I accomplished:
- Created a custom **IAM policy** using JSON to control access to EC2 instances.
- Tagged EC2 instances with **Production** and **Development** labels for easy differentiation.
- Configured an **Account Alias** to simplify AWS console login.
- Assigned permissions to a user group and tested them by logging in as an IAM user.

This project helped me understand the importance of managing permissions effectively to ensure secure access to AWS resources.
