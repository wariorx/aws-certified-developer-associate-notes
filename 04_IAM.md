# Identity Access Management
* Service that helps manage access to an AWS account and its resources

## Features
* **Shared access** Allows for sharing of access at a granular level to an account without having to share credentials 
* **Global service**
* **Integrated** in many AWS services
* **Supports MFA**
* **Identity Federation** - users with credentials elsewhere (IE corporate credentials) can gain temporary access to an AWS account
* **Free**

## IAM Users
* Represents a person or service that interacts with AWS
* Defined at the account level - user activities are billed to the account
* Can access resources using either username + password (console) or an access key (programatic)
* User credentials can be roated; but are not rotated by default
* Can have permissions individually assigned; or be added to user groups / roles that grant them permissions

## IAM Groups
* A collection of users; all users in the group inherit the permissions assigned to the group
* Great for scalability - best practice
* Groups cannot belong to groups
* Are granted permissions through IAM policies

## IAM Policies
* Are atttached to IAM Identities (users, groups, roles) and are evaluated on the fly for access control
* Four major elements:
1. **Version**: defines the policy language and syntax rules. Version 2021-10-17 provides all available features as of right now.
2. **Effect**: allow or deny
3. **Action**: the type of action that should be allowed or denied by the policy; IE s3:Get or sts:*
4. **Resource**: The element or object the policy covers
```json
{
"Version": "2012-10-17",
"Statement": [{
    "Effect": "Allow",
    "Action": "*",
    "Resource": "*"
}]
}
```
For more granularity **Conditions** can be added to the policy
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "DenyS3AccessOutsideMyBoundary",
      "Effect": "Deny",
      "Action": [
        "s3:*"
      ],
      "Resource": "*",
      "Condition": {
        "StringNotEquals": {
          "aws:ResourceAccount": [
            "222222222222"
          ]
        }
      }
    }
  ]
}
```

**Principals** can also be applied to resource-based policies (applied to a specific resource or roles) to specify WHO is being granted access

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "sts:AssumeRole"
      ],
      "Principal": {
        "Service": "ec2.amazonaws.com"
      }
    }
  ]
}
```

## IAM Roles
* Identities that can be assumed by a user or service to temporarily gain credentials
* Can be assumed programatically through sts calls
* Expire after a time period; role must be assumed again to obtain new credentials
* Scale well as many users and services may assume a role at the same time
  
## Best Practices
* **Lock down root user**: strong password + MFA + No sharing + No access keys
* **Least privilege principle**: grant only the minimum necessary permissions
* **Use IAM for its intended purpose**: secure access to account and resources; do not use for web sig-in or OS security controls
* **Use roles when possible**: shortest-lived crentials (15 min to 36 hours) - vs user keys and passwords which may live indefinitively or until forced to rotate
* **Consider Identity Providers (IdP)**: use a third party provider and/or AWS IAM Identity center to authenticate users; then leverage IAM Identity federation to allow them to assume a role when needed
* **Regularly update users, roles, and other credentials** 