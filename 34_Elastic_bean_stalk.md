# Elastic Beanstalk
* Quickly deploit abd nabage AWS infrastructure without worrying about the underlying services that support it - focus on code
* Supports development in
  * Go
  * Java
  * .NET
  * Node.js
  * Python
  * Ruby
* Interact on console, CLI or eb CLI
* Deploy and perform mos deployment tasks such as EC2 size changes, monitoring, etc. from the web interface

## Concepts
* **Application** logical collection of EBS components; environments, versions, and environment configurations
* **App version** specific, labeled iteration of deployable code for a web app. It points to an S3 object containing the deployable code.
  * Apps can have many versions and each application verson is uniqye
* **Environment** collection of AWS resouces running an application version. Each runs only ONE application version at a time - but we can also run multiple versions simultaneously under the same environment
* **Environment tier**: designates teh type of application that will run and determines what resources EBS provisions. 
  * **Web server**: applications that serve HTTP requests
  * **Worker**: applications taht pull tasks from SQS queue
* **Environment Configuration** identifies a collection of parameters and settings that define how an environment and its associated resources behave. Configuration changes will be automatically applied and environment resources will be updated / re-created / destroyed as needed
* **Saved configuration / Configuration Templates** template that you can use as a starting point for for creating unique environment configurations. You can create and modify saved configurations and apply them to environments using EB CLIm console, AWS CLI, or API calls
* **Platform**: combination of an operating system, programming language runtime, web server, applications server, and EBS components. You may define your own or use a provided platform:
  * **Runtime**: the programming language runtime software required to run the application
  * **Platform version**: specific platform details - labeled using semver x.y.z pattern. It can be in 2 states:
    * Supported: consists entirely of supported components that have not yet reached their end of life (defined by AWS or third party)
    * Retired: one or more components have reached end of life. You may maintain these but they won't be able to create new platforms with these
  * **Platform branch** platform versions sharing some specific version of their components (usually major version) - for example Python 3.6 running on 64bit Amazon Linux; IIS 10.0 running on 64bit Windows Server 2016

## Permissions
* **Service role**: EBS assumes this role to use other AWS services in your behalf. New environment creation will result in a service role being created with managed policies if one is not already present in the account
* **Instance profile** IAM role applied to the instances in th environment; allowing them to perform AWS actions they mayb need such as retrieving app versions from s3 buckets, integrating with X-ray, or connecting to Dynamodb


## Deployments
* All available to load-balanced environemnts; not all to single-instance environments or legacy windows server
  * All at once
  * Rolling: avoid downtime and minimize reduced availability at the cost of longer deployment time
  * Rolling with an additional batch: NO reduced availability at the cost of longest deployment time
  * Immutable: ensures that a new app version is always deployed to new instances by creating new intances in a new auto-scaling group with the new versions alongside the existing ones and then shifting traffic
  * Traffic splitting / canary: test health of the new application version using a portion of the traffic while keeping the rest serving the old application
  * Blue / green : avoid downtime by deploying a new environment version to a new set of resources, then update DNS CNAME to map to the new resources and perform testing. Drop old resources when testing is successful.

## Advanced environment customization with configuration files (.ebextensions)
* Add configuration files to a web application source code to configure your environment and customize the resources it contains
* `.config` yaml or json file documents within a `.ebextensions` folder lovalted at the root of the source bundle; IE `.ebextensions/network-load-balancer.config`
 ```yaml
 option_settings:
  aws:elasticbeanstalk:environment:
    LoadBalancerType: network
 ```
* **Requirements**:
  * Elastic beanstalk consumes all `.ebextensions` files in a deployment; but the best practice is to place a single folder at teh root of the bundle
  * Files must be followed by the `.config` suffix
  * Formatting must be YAML or JOSN
  * Each key must be unique within a configuration file

* **Sections**
  * `option_settings`: defines the configuration options used to modify the environment behavior. It is made up of one or more **namespaces** (`aws:elasticbeanstalk:environment`) followed by one or more **option names** (`LoadBalancerType`)
  
* **Presedence**: During environment creation, configuration options are applied from multiple sources with the following precedence, from highest to lowest
  * Settings applied directly to the environment  via API calls or a client
  * Saved configurations
  * Configuration files
  * Default values (if none of the above specified) 