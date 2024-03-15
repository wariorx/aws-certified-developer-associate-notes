# Systems Manager
* Collection of acapabilities that enable visibility and control over AWS resources
* Make use of the **SSM Aggent** in EC2 instances, edge devices, and on-prem servers to update, manage, and configure resources
  * The agent will process requests from the Systems Manager service in AWS and run them, then report bck with a status and execution information

## Application Management

### Application Manager
* Investigate and remediate issues with AWS resources in the context of applications and clusters
* **Application** = logical group of resource that you want to operate as a unit. This can be different versions of the application, developer environments, etc.
  * Custom applications
  * Launch Wizard
  * CloudFormation Stacks
  * AppRegistry
  * ECS Clusters
  * EKS Clusters
* Reduce the time it takes DevOps engineers to detect and investigate AWS issues
### AppConfig
* Create, manage, create, and deploy application configurations and feature flag, and control deployments to applocations of any size
* Application types:
  * EC2
  * Lambda
  * Mobile applications
  * Edge devices
* **Features**
  * Feature flags and toggles – Safely release new capabilities to your customers in a controlled environment. Instantly roll back changes if you experience a problem.
  * Application tuning – Carefully introduce application changes while testing the impact of those changes with users in production environments.
  * Allow list or block list – Control access to premium features or instantly block specific users without deploying new code.
  * Centralized configuration storage – Keep your configuration data organized and consistent across all of your workloads. You can use AWS AppConfig to deploy configuration data stored in the AWS AppConfig hosted configuration store, AWS Secrets Manager, Systems Manager Parameter Store, or Amazon S3.
  * Validate configuration data syntactically and semantically before deploying
  * Leverage linear or exponential deployment strategies for canary testing and ensure operational time
  * Monitoring and automatic auto-rollback using Cloudwatch Alarms
  
### Parameter Store
* Secure, hierarchical storage for configuration data and secrets management.
* Store data as plain text or encrypted passwords
* Reference Secrets Manager secrets to securely store and rotate passwords
* Share parameters and secrets with other accounts and reference them from other AWS services
* Store **String, StringList(csv), or SecureString**
* Uses KMS to encrypt SecureString
  
## Change Management

### Change Manager
* Enterprise change management framework for requesting, approving, implemention and reporting on operational changes to application configuration and infrastructure
* Manage multiple accounts accross regions from a single administrative account or use a local account to manage a single account
* Use **change templates** to help automate change processes for resources and help avoid unintentional results. Each template specifies:
  * **Automation runbooks** can be used to define the changes made to resources; this can be added to change requests
  * User who must review
  * SNS topic used for notifications to approvers
  * Cloudwatch alarm used to monitor workflows
  * SNS topic for status changes
  * Tags to apply to the change template
  * Whether change requests can be run with or without approval
  
### Automation
* Simplify common maintenance, deployment, and remediation tasks for AWS services kije EC2, RDS, Redsgift, or S3
* Build automated solutions to deploy, configure, and manage AWS resources at scale
* Run automations accross multiple regions and accounts
* Use **automation runbooks** to defune the actions that systems manager perofrms on managed nodes and AWS resources for common tasks like restarting instances or creating AMIs or create custom runbooks

```json
{
    "schemaVersion": "0.3",
    "description": "Automates deployment of a basic web application on EC2 instances",
    "assumeRole": "{{ AutomationAssumeRole }}",
    "parameters": {
        "InstanceIds": {
            "type": "StringList",
            "description": "List of EC2 instance IDs to deploy the web application onto"
        }
    },
    "mainSteps": [
        {
            "name": "InstallHttpd",
            "action": "aws:runShellScript",
            "inputs": {
                "runCommand": [
                    "yum update -y",
                    "yum install -y httpd"
                ]
            }
        },
        {
            "name": "StartHttpdService",
            "action": "aws:runShellScript",
            "inputs": {
                "runCommand": [
                    "service httpd start",
                    "chkconfig httpd on"
                ]
            }
        },
        {
            "name": "DeployWebApplication",
            "action": "aws:runShellScript",
            "inputs": {
                "runCommand": [
                    "echo '<html><h1>Hello World!</h1></html>' > /var/www/html/index.html"
                ]
            }
        }
    ],
    "outputs": {
        "DeploymentStatus": {
            "description": "Status of the deployment process",
            "value": "Web application deployed successfully"
        }
    }
}
```
### Change Calendar
* Set uo date and time ranges when actions specified might or might not be performed on an AWS account. For example, set up times on which an automation runbook may not be ran

### Maintenance Windows
* Define schedule for when to perform potentially distruptive actions on nodes such as patching an OS, updating drivers, or installing software patches
  
## Node Management
### Fleet Manager
### Compliance
### Inventory
### Session manager
### Patch Manager
### State Manager

## Operations Management
### Explorer

### Change Manager

### OpsCenter

