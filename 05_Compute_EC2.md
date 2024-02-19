# EC2
* Web service that provides secure and resizable compute capacity in the cloud using instances (servers)
* Operates and manages host machines and hypervisor layers in the background
* Pay by the hour or second of work
* Must specify:
  1. **Hardware**: CPu, memory, network, storage
  2. **Logical Configurations**: Networking location, firewall rules, authentication, OS of choice
* Configuration can be pre-selected and/or exported by selecting an **Amazon Machine Image** (AMI) - AMIS include OS, storage mapping, architecture, launc permissions and any pre-installed applications to minimize startup time
* AMIS can be imported from:
  1. Quick start AMIs provided by Amazon
  2. AWS Marketplace
  3. My AMIs
  4. Community AMIs
  5. Custom built
* Unique AMI ID composed by a prefix ami- followed by a random hash --> Unique per region

## Configuring EC2
### Instance Types
* Combination of vCPUs, memory, network, and in some case GPUs; classified by tiers IE t2.micro
* Types are composed of
  * **Family**: indicated by the character in the first position; it indicates which compute optimized family the instance belongs to
  * **Generation**: indicated by the number in the second position
  * **Additional attributes**: remaining letters befor the period; for example NVMe storage
  * **After period**: instance size; aka the amount of resources dedicated to the instance
### Instance Families
* **General Purpose**: Balance of compute, memory, and networking resources; can be used for a variety of workloads that use resources in equal proportions such as web servers
* **Compute Optimized**: ideal for applications that benefit from high-performant processors such as media transcoding, gaming servers, high performace web servers,s or high performace computing
* **Memory Optimized**: deliver performance for workloads that process large datasets such as databases, distributed in-memory caches, or real time data analytics tools.
* **Accelerated Computing**: use hardware accelerators or co-processors to perform functions such as graphics, floating point arithmetic or data pattern matching. Useful for HPC, ML, physics simulations, etc.
* **Storage Optimzied**: designed for workloads that require high sequential read and write access to large datasets on local storage; since they are optimized to deliver thousands of low latency random I/O operations per second (IOPS). Examples would be NoSQL databases, in-memory databases, data warehousing, Elasticsearch, and analytics
* **HPC Optimized**: built to offer the best price performance for running HPC at scale on AWS; ideal for applications like deep learning workloads or complex simulations.

### Instance Locations
* EC2 instance always live within a VPC. If not specified at creation time, the default VPC for the account is used.
* A subnet in an AZ of the user's choice must also be selected at creation time

## Architecting High Availability
* Create multiple small instances rather than a few large ones
* Use different AZs and/or regions when possible
* Be mindful of data compliance and network configuration (public subnets)

## Instance Lifecycle
1. **Pending**: initial state when first launched and billing hasn't started yet. This is where AWS performs all actiosn needed to setup an instance, such as allocating resources and copying the AMI data.
2. **Running**: the instance is ready to use. This is also the stage at which billing starts. At this point, other actions can be taking on the instance such as stopping, logging in, or hibernating.
3. **Rebooting**: different from performing a stop followed by a start; it gets to keep its public DNS name and private IP addresses
4. **Stopping and Stopped**: instances with an **Elastic Block Store (EBS)** can be started and stopped. This allows you to place the instance in a different underlying physical server. it will retain its private IP addresses.
When stopped, certain configurations such as instance type can be updated.
   * **Stop-hibernate**: the instance enters teh stopped state, but saves the last information or contenet into memory so the next start will be faster. can only be done if hybernation is turned on and the instance meets hibernation requirements
**Note:** stopped instances will stil incur charges for any EBS storage used
5. **Terminate**: the instance stores are erasde and all IP addresses are lst. it can no longer be accessed. As soon as shutting down or terminated statuses are achieved billing stops.

## Pricing
* Choosing the right Instance type for the job a good way to minimize costs.
* Choosing the right pricing option is also a key contributor to reducing costs:
  1. **On-demand**: Pay per-hour or per-second depending on the instances. No long erm commetmetns or upfront costs. Recommended when:
   * Flexivility of EC2 is needed without long-term commitments
   * Applications with short-terms spikes of unpredictable workloads that cannot be interrupted
   * Applicatiosn being developed or tested in EC2 for the first time
  2. **Spot instance**: When flexible start and end times are an option, spot instances can be used for up to 90% off the on-demand price (they make use of any spare resources on the AWS servers but as such could be terminated at any time)
    * Applciations with flexible start and end times
    * Applicatiosn taht are only feasible at low compute prices
    * Uses with fault-tolerant and stateless workloads 
  3. **Saving Plans**: Flexible pricing models that offers low usage prices for 1 or 3 year term commitment to consistent usage. Applied to EC2, Lambda, and Fargate and provide up to 72% savings. Great for workloads with predicateble and consistent usage such as :
   * Users who want to use different isntance types and solutions acccross different locations
   * Customers who can make the monetary commitment to use EC2 over a 1 or 3 year term
  4. **Reserved instances**: for applications with steady state usage that might require reserved capacity; save upto 75% compare to on-demand and pay either upfront, partial upfront, or no upfront with either a 1 year or 3 year term commitment. Choose between:
   * Standard Reserved Instances: up to 72% discount and best suited for ssteady-state usage
   * Convertible reserved instances: procide upto 54% discount and the capability to change the attributes of reserved instances if the exchange results in the creation of reserverd instances of equal or greater value.
   * Scheduled reserved instances: availalbe to launch wihthin a reserved time window for predictable recurring scheduled tasks
  5. **Dedicated hosts**: Physical server in an Amazon warehouse dedicated to personal use. This allows you to re-use software licenses that would be bound to the server hardware such as a Windows Server, or Oracle database. You can also use them to meet compliance requirements since the hardware is not shared with any other accounts. Integrated with AWS licensen manager.
    * Hourly; aka on demand
    * Reservable for up to 70% off

## Additional Configurations
* **Key pair**: to connect to the instance using SSH. If not selected, user sin account can still connect using the connect button on the console (for amazon Linux and some other AMIs)
* **Firewall / Security groups**:  to manage internet access to and from the instance
* **Public IP**: if the instance is to be publically available from the internet
* **Instance Profile**: role that the instance will run under; provides it with permissions to perform AWS actions
* **User Data**: batch script that will run on-launch every time the instance is started; IE start a web-server
