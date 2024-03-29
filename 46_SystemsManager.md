# Systems Manager
* Collection of acapabilities that enable visibility and control over AWS resources
* Make use of the **SSM Aggent** in EC2 instances, edge devices, and on-prem servers to update, manage, and configure resources
  * The agent will process requests from the Systems Manager service in AWS and run them, then report bck with a status and execution information
* For EC2 instances, managed instance profile `EnablesEC2ToAccessSystemsManagerRole ` can be used to grant systems maanger permissions
* For non-EC2, you can use Service Roles 
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
* Monitor and manage properties of entire fleet; including
  * Amazon Elastic Compute Cloud (Amazon EC2) instances
  * Servers on your own premises (on-premises servers)
  * AWS IoT Greengrass core devices
  * AWS IoT and non-AWS edge devices

Virtual machines (VMs), including VMs in other cloud environments
### Compliance
* View compliance history and change tracking for Patch Manager
* Customize compliance
* Remediate issues by using Run Command, State manager, and EventBridge
### Inventory
* Collect emtadata from maanaged nodes and store in in central S3 buckets - then use built-in query and analysis tools
  * Applications
  * AWS components
  * Files
  * IP config
  * Windows updates
  * Instance details
  * services
  * tags
  * Windows REgistry
  * Windows Roles
  * Custom inventory
  
### Session manager
* Manage your EC2 instances, edge devices and on-prem servers with either an interactive browser shell or AWS CLI
* Securely and auditably maange nodes wihtout the need to open inbound ports or manage SSH keys
* Compy with corporate policies that require controlled access to maange nodes, security practices, and auditable logs
* Centralized access control to managed nodes using IAM
* Cross-platofrm windows, Linux, Mac OS

### Run Command
* Remote and securily maange the configuration of your managed nodes - EC2 instances or non-EC2 machines in hybrid clouds that can be configured for Systems Manager
* Automate common tasks and perform one-time configurations at scale
* Remotely run commands on EC2 instances

### Patch Manager
* Automate the process of patching nodes
* Use patch policies to define patching for all accounts in all Regions for an organization
* Apply patches for both OS and applications - note that major OS version upgrades are not supported
* Uses **patch baselines** for auto-approving patches within days of hteir release in additional to lists of approved and rejected patches
* Generate compliance reports after running Scan operations on nodes on the fleet
* **Scan and Install** operations methods:
  *  **Patch policy configured in Quick Setup** – Based on integration with AWS Organizations, a single patch policy can define patching schedules and patch baselines for an entire organization
  *  Host Management option configured in Quick Setup – Host Management configurations are also supported by integration with AWS Organizations, making it possible to run a patching operation for up to an entire Organization
  * **Maintenance window to run a patch Scan or Install task** – A maintenance window, which you set up in the Systems Manager capability called Maintenance Windows, can be configured to run different types of tasks on a schedule you define. A Run Command-type task can be used to run Scan or Scan and install 
  * **On-demand 'Patch now'** operation in Patch Manager – The Patch now option lets you bypass schedule setups when you need to patch managed nodes as quickly as possible. Using Patch now, you specify whether to run Scan or Scan and install operation and which managed nodes to run the operation on

### State Manager
* Secure and scalable configuration amangement service that automates the process of leeping maanged nodes in a user-defined state
* Bootstrap nodes with start-up software
* Download and update agents on a defined schedule
* configure network settings
* Join nodes to a Microsoft AD domain
* Run scripts on Linux, Windows, and macOS
* Use **Automation with State Manager** to manage configuration drifts

### Distributor
* Package and publish software nodes
* Package own software or use Distributor to find and publish AWS-provided packages such as the Cloudwatch Agent or third party agents
* Install packages as a one-time operation (Run command) or on a schedule (State Manager)
* Control pacakge access accross groups of managed instances
* Automate deployments
* Re-install packages and perform inplace updates

## Operations Management
### Incident manager
* manage incidents occurring in AWS applications
* Combines user engagements, escalations, runbooks, response plans, chat channels, and post-incident analysis
* Mitigate and ecover from incidents affecting applications hosted on AWS
* Create automated plans for engaging response teams
* Provide relevant troubleshooting data
* Enable automated response actions in runbooks
* Provide methods to collaborate with stakeholders
* Detect incidents by configuring Cloudwatch Alarms and Eventbridge evnets
* Post-incident analytics to identify inprovements on responses
  
### Explorer
* Operations dashboard that reports information about AWS resources
* Display aggregated views of operations data for AWS accoutns accross regions
* Includes metadata about managed nodes in multicloud enviromnets
* Aggregates information provided by other systems manager capabilities

### OpsCenter
* Provides central location where operations engineers can manage operational work items related to AWS resources
* OpsItem is any operational issue or interruption that needs investigation and remediation - it includes the relevant information such as name or ID required to solve an evnet
* Integrate with othe AWS services like Config, CloudTrail, and Eventbridge

* Investigate and remediate issues with on-prem managed nodes configured with systems manager
* 


