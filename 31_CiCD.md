# CI/CD

## CodePipeline
* Fully managed CI/CD services that helps automate release pipelines for fast and reliable application and infrastructure updates
* Defines a process workflow and describes how a new code change progresses thorugh your release process
* Pipelines are defined in a series of **stages** which act as a logical division of the workflow
  * Stages are comprised of a series of **actions** - tasks such as building code or deploying to environments
* Provides with a graphical user interface to create, configure, and manage your pipeline and its various stages and actions
* Use **parallel execution** to run actions at the same time and increase workflow speeds

### Integrations
* Pull source code from:
  * CodeCommit
  * Github
  * ECR
  * S3
* **CodeBuild** Run unit tests and build code
* **Deploy code** using CodeDeploy, Elastic Beanstalk, ECS, or Fargate
* **Update AWS resources** though CloudFormation actions - continuously deliver serverless applications using Lambda, APi Gateway, and DynamoDb using **SAM**
* Trigger custom functions defined at any code stage using **Lambda Integrations**
* **Third party developer tools** and custom systems

### Plugins
* You may also integrate third-party developer tools like GitHub or Jenkins into any stage for source control, building, testing, or deployment
* **Custom plugins** can also be registered to a **custom action** - allowing you to hook your servers into CodePipeline using the CodePipeline agent

### Templating
* Define Pipeline structure through a **delcarative JSON** document specifying the release workflow and its stages and actions. You may also use these to update existing pipelines

### Access Control
* Use **IAM** to manage pipeline permissions such as changing workflows and running stages or actions.
  * IAM users
  * IAM roles
  * SAML-integrated directories

### Notifications
* Create notifiactions on pipeline results using **SNS** notifications. Each notification includes a status message as well as a link to the resources whose event generated the notification

## Cloud9
* Cloud-based IDE to write, run, and debug code through your browser
* Support for 40+ popular langugaes like JS, Python, and PHP
* Quickly share development environments and track inputs in real time
* Keep **file revision history** to quicjly access past changes
* Image editing
* Collaboration chat
* Built-in ternminal with sudo privileges
### Integrations
* **CodeStar** quickly setup end-to-end CI/CD applications. Using CodeStar privoides a unified experience that enables you to easily build, test, and deploy applications with the help of other CI/CD services such as CodeBuild an CodeDeploy

### Use cases
* Speedup Lambda development by editing and debugging functions locally instead of having to upload the code to the lambda console
* Quickly run commands on the underlying EC2 instance Cloud9 is hosted on to directly access AWS services

## CodeStar
**NOTE:** Will be discontinued mid 2024
* Unified user interface for easily managing software development activities in one place
* **Project templates** help quickly start developing applications
* **Team access management** though IAM integration
* Code storage using **CodeCommit** or Github
* Build code using **CodeCommit**
* Integrate with **CodePipeline** for fast pipeline creationg and management
* **Automated deployments** using CodeDeploy and CloudFormation
* **IDE Integrations** by developing code in Cloud9 or a series of otehr IDEs such as Visual Studio or Eclipse
* **Central project dashboard** to easily track and mange CI/CD, allowing you to monitor build, take actions where needed, add **project wiki**
  * Integrates with **cloudwatch** for application monitoring and **Jira** for project amangement and ticket monitoring

## CodeCommit
* Secure and highly scalable fully managed source control service for **private Git repos**
* Migrate any Git repository into CodeCommit for easy integration into AWS CI/CD

### Integrations
* **AWS Amplify**
* **Cloud9** cloud9 allows you to remote interat with Code hosted in CodeCommit and leverage source control
* **CloudFOrmation**
* **CloudTrail** logging and monitoring CodeCommit API calls
* **CloudWatch** monitor CodeCommit repositories and respond to repository evnts to respond to events by targeting streams, functions, tasks or other processes 
* **CodeBuild**
* **Code Guru**
* **Code Star**
* **Elasitc Beanstalk**
* **KMS** - code is **encrypted by default** using KMS
* **Lmabda** invoked in response to events
* **SNS** send notifications in response to repo events or integrate with other AWS ervices such as SQS
* **IAM** for access control on repository 

## CodeBuild
* Compile, build, test, and produce softwar packaes for deployments

### Build Process
* **Input**: CodeCommit, Github, CodePipeline, etc.
* **Build Project**: includes information about how to run a build - wher to get the source code, build environment, commands to run, and where to store the output
  * **Build environment**: combination of OS, programming language runtime, and tools used to run a build
  * **buildspec** steps and build commands and settings used ro run a build
* **Output**: results form the build step will be uploaded to an S3 bucket of your choice. You may also do any additional tasks in the buildspec such as **SNS** notifications
* **Logging**: Build runs will og events to CloudWatch. Additionaly, if CodePipeline is in use you may also visualize some events there

### Build SPEC
* **Version**: version of the build spec being used; latest is 0.2
* **Phases** steps on which you can instruct CodeCommit to run commands, These are a set of naemd actions whihc cannot be renamed and additional ones cannot be added
  * **install**
  * **pre_build**
  * **build**
  * **post_build**
* **Optional Sequences**
  * **env** information for one or more env variables
  * **run-as** linux-only; identifies the user that will run the commands with rw permissions
  * **proxy**: run build on specified proxy server
  * **report** specify teh report group reports are sent to, up to 5 reports. IF arn doesn't match an existing one, a new one will be created
  * **artifacts** specifies WHERE code build can find the output and how to prepare it for S3 upload. Not required if building to ECR OR if only running tests
  * **cache** idenitifies information on where CodeBuild cna prepare cache files for S3 cache bucket
* **Artifacts** set of build output artifacts to be uploaded to the output bucket
  * **files** files to include in the build output

    ```yaml
    version: 0.2

    run-as: Linux-user-name

    env:
      shell: shell-tag
      variables:
        key: "value"
        key: "value"
      parameter-store:
        key: "value"
        key: "value"
      exported-variables:
        - variable
        - variable
      secrets-manager:
        key: secret-id:json-key:version-stage:version-id
      git-credential-helper: no | yes

    proxy:
      upload-artifacts: yes
      logs: yes

    batch:
      fast-fail: true
      # build-list:
      # build-matrix:
      # build-graph:
    phases:
    install:
        runtime-versions:
        java: corretto11
    pre_build:
        commands:
        - echo Nothing to do in the pre_build phase...
    build:
        commands:
        - echo Build started on `date`
        - mvn install
    post_build:
        commands:
        - echo Build completed on `date`
    artifacts:
    files:
        - target/messageUtil-1.0.jar
    ```

## CodeArtifact
* Secure and scalable package management service for development
* Store artifacts using popular package managers and build tools like Maven, npm, SwiftPM, pip,etc.

### Concepts
* **Asset**: individual file stored in CodeArtifact that's associated with a package version such as an anpm .tgz
* **Package**: bundle of software and the metadata that is required to resolved dependencies and install the software
  * Package name
  * **Package namespace**: some package formats support hierarchical names to organize packages into logical groups to avoid name collisions
    * IE NPM scope -> @types is scope and /name is package -> @types/name
  * **Package version(s)**: identifies the specifc version of the package. Semantics vary by underlying package manager IE `semver`
    * Each time a version is updated a **package version revision** is created to differentiate the updated version
  * Set of package versions
  * Package metadata (IE npm rags)

* **Repository**: Contains a set of package versions, each of which maps to a set of assets.
  * **Polygot**: single repository can contain packages of any supported type
  * Exposes endpoints for fetching and publishing packages using tools like npm CLI and pip.
  * Up to 1000 per Domain
* **Domain**: aggregation of Repositories; All package assets and metadata are stored in the domain but are consumed through repositories.
  * A given package is only stored once in the domain regardless of how many different repositories reference it
  * Repositories are members of a single domain and cannot be moved
  * Allows you to apply **organizational policies** accross multiple repositories - determine which accounts can access repos in the domain, which repos are public, etc. -> single Domain per org is recommended
* **Upstream repository**: package versions in a repository can be accessed from the repository endpoint of a downstream repository -> allows you to mirror other repositories.

### Example setup with NPM
* Login code artifact: `aws codeartifact login --tool npm --domain my_domain --domain-owner {owner_accoutnt_id} --repository my_repo`
* Repo https endpoint example" `https://my_domain-111122223333.d.codeartifact.us-west-2.amazonaws.com/npm/my_repo/`
* Set registry to point to CodeArtifact: `npm config set registry=https://my_domain-111122223333.d.codeartifact.region.amazonaws.com/npm/my_repo/`
* Add auth token received from login to npm: `npm config set registry=https://my_domain-111122223333.d.codeartifact.region.amazonaws.com/npm/my_repo/`

Example .npmrc file after following steps:
```
registry=https://my_domain-111122223333.d.codeartifact.us-west-2.amazonaws.com/npm/my-cli-repo/
//my_domain-111122223333.d.codeartifact.us-west-2.amazonaws.com/npm/my_repo/:_authToken=eyJ2ZX...
//my_domain-111122223333.d.codeartifact.us-west-2.amazonaws.com/npm/my_repo/:always-auth=true
```

* installing and pushing should now go through the CodeArtifact: `npm install {packageName}` and `npm publish` 

### Use Cases
* Fetch software packages on demand from public repositories
* Publish and share packages privately within an organization
* Approve and audit package usage - automate approval workflows
* Use packages in automated builds


## CodeDeploy
* Fully managed deployment service that automates software deployments to various compute services such as EC2, ECS, Lambda, or on-prem servers.
* Automate software deployments -> Build can be triggered whenever new deployable files along with a configuration `AppSec` (defines actions for CodeDeploy to take) files are dropped in an **S3 bucket or Github repo** in an `archieve` bundle file
  * Example ECS AppSec file:
    ```yaml
    version: 0.0
    resources: 
      ecs-service-specifications
    hooks: 
      deployment-lifecycle-event-mappings
    ```
    * **version**: version of AppSec to use. Currently only 0.0
    * **resources**: information about the ECS application to deploy
    * **hooks**: specifies lambda functions to run at specific deployment lifecycle events to validate teh deployment. For ECS:
      * BeforeInstall – Use to run tasks before the replacement task set is created. One target group is associated with the original task set. If an optional test listener is specified, it is associated with the original task set. A rollback is not possible at this point.

      * AfterInstall – Use to run tasks after the replacement task set is created and one of the target groups is associated with it. If an optional test listener is specified, it is associated with the original task set. The results of a hook function at this lifecycle event can trigger a rollback.

      * AfterAllowTestTraffic – Use to run tasks after the test listener serves traffic to the replacement task set. The results of a hook function at this point can trigger a rollback.

      * BeforeAllowTraffic – Use to run tasks after the second target group is associated with the replacement task set, but before traffic is shifted to the replacement task set. The results of a hook function at this lifecycle event can trigger a rollback.

      * AfterAllowTraffic – Use to run tasks after the second target group serves traffic to the replacement task set. The results of a hook function at this lifecycle event can trigger a rollback.

### Features
* Support for **server, serverless, and container applications**
* Automated deployments accross environments
* **Stop and rollback** deployments at any time
* **Platform agnostic** works with any application and is re-usable accross code bases
* **Concurrent deployments**: if you have more than one application that uses EC2/On-Premises compute platform, CodeDeploy can deploy them concurrently to the same instances

### Deployment Types: 
* **In-place**: performs a **rolling update** accross targets - specify the number of instances to be taken down at a time for updates
* **Blue-green**: latest application revision is installed on replacement instances. A portion of traffic is rerouted ot these instances when you choose (testing / verification). Once changes have been verified, all traffic is directed to new instances and old ones are taken down. **health tracking** takes place accross deployment types
  * Blue/green on Lambda and ECS can take place as
    * **Canary**
    * **Linear**
    * **All-at-once**
  * Blue/green on CloudFormation will shift traffic from current resources to updated reosurces as part of a Stack Update -> only ECS will get blue/green

### Platforms
* **EC2/On-Premises**: physical servers that can be Amazon EC2 cloud instances, on prem-servers, or both. Apps compsoed of execuables, configs, images, etc.
  * Code Deploy allows you to manage the way in which traffic is directed to instances by using an in-place or blue/green deployment
* **Lambda** updated versions of Lambda functions. You can amange the way in which traffic is shifted to the updated function versions during a deployment from linear, canary, or all-at-once
* **ECS**: deoky amazon ECS containerized application as a task set. New versions will be deployed to a replacement task set. Manage using canary, linear, or all-at-once configurations

## CodeGuru
* Services that uses program analysis and machine learning to detect potential defects that are difficult for developers to fund and offers suggestions for improving Java or Python code
  * Detect defects
  * Detect vulnerabilities
  * Detect resource leaks

### Integrations
* CodeCommit
* BitBucket
* GitHub
* S3
  
