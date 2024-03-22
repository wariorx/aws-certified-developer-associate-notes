# RDS - Relational Database Service
* Data is organized into tables; structured data AND relationships can be established between tables
* **OpenSource**: MySQL, PostgeSQL, MariaDB
* **Commercial**: Oracle. MS SQL Server
* **AWS Native**: Aurora
* SQL queries can be used to retreive data and identify patterns
* Managed database service custoemr can use to create and mange relational databases in the cloud

## Pros
* Easy to establish and understand complex relationships through joins
* Reduced redundancy through normalization and references
* Developer Familiarity
* Strong consistency, high integrity, and atomic operations (ACID) principle

## Use Cases
* Fixed schema applications that don't change often
* Applications taht need persistent storage to follow ACID principles

## Unmananaged Databases (not RDS)
* Customer is responsible for and has control over the hardware and underlying infrastructure (IE running db in an EC2 instance)
* Customer needs to manage optimizations, scaling, availability, backups, patches, etc.
## Managed Databases (RDS)
* AWS procides high availability, scaling, patching, and backups
* Customer must only worry about optimizing the database
  
## Database instances
* Instance = compute portion of the database, which runs the DB engine
* Different engines offer different features and configurations
* An instance can have multiple databases with the same engine; each db containing multiple tables
* Choose instance type and size 

| Type | Family | Description |
| ---- | ---- | ---- |
| Standard classes | m | balance of compute, memory, and network resources |
| Memory optimized | r, x | memory optimized to accelerate performance for workloads that process large datasets |
| Burstable classes | t | provide a baseline CPU performance with the ability to burst above the baseline |

## Storage on RDS
* MySQL, MariaDB, PostgreSQL, Oracle, SQL Server: Uses EBS under teh hood for database and log storage
* Aurora: uses cluster volumes (virtual volumes that use SSDs). Contains copies of the dat accross 3 AZ within the instance region. Temporary files are stored in local storage
* Storage Options
  
| Type | Family | Description |
| ---- | ---- | ---- |
| General Purpose | gp2, gp3 | Cost effective storage, ideal for a wide range of medium-size db instances, development and testing |
| Provisioned IOPS SSD | io1 | Designed to meet I/O intensive workloads, requiring low I/O latency and consistent I/O throughput. Best suited for Prod |
Magnetic | Standard | For backward compatibility; not recommended unless backward compatibility is required since it's slower and the maximum storage is less |

## Using RDS within a VPC
* RDS instances must live within a subnet in a VPC
* DBs within the same Subnet are assigned to the **database group**; and a database group is distributed amongst at least 2 AZ
* DB Sunets should be private (no route to internet gateway) - ACLs and Security groups can be used to further restrict access

## Backup data
* **Automated**: turned on by default; the entire DB instance is backed up along with its transactional logs. When creating the instance, you must set a backup window (period of low traffic) during which the automated backups will be performed
  * **Retaining backups**: retained between 0 and 35 days; setting this to 0 will prevent automated backups and delete all existing ones
  * **Point-in-time recovery**: creates a new instance using data restored from a specific point in time. Provides more granularity by restoring the full backup and rolling back transactions up to a specified range.
* **Manual Snapshots**: Allow you to keep backups for a longer period of time; until they are manually delted

Both backup options are advised

## Redundancy with Multi-AZ
* By default, AWS will create a redundant copy of the database in another AZ
* Data in the primary (writer instance) is synchronously replicated to the copy (reader instance)
* The copy is not considered active and should not be queried by applications
* DNS names are provided for both databases. In automatic failover, the standby database is promoted to the primary role and all queries are directed to it instead
* You can always manually create a new stand by instance fur further durability by either demoting the primary OR simply creating a new standby

## Security
* Network Traffic
  * Netowrk ACLs
  * Security Groups
* Actions: IAM Policy
  * Manage RDS resources
  * Create,delete, and update isntances
* Encryption: At rest Enabled by default, in transit (TLS) is supported and recommended
  
## Aurora
* Fully maanged RDS endinge compatible with MySQL and PostgreSSQL
* Combine the speed and reliability of high-end commerical databases with the cost-efficiency and effectiveness of open-source databases
* Leverage fast underlying distribyted storage that **grows automatically as needed** up to 128 tebibytes
* Clustering and replication is automated
  * **Primary db instance**: supports read and write operations and performs all data modificatiosn to the cluster Volume
  * **Aurora replica**: connects to the same cluster volume as the primary instance byt supports only read. Up to **15** replicas in addition to the primary instance which can be located in separate AZs and automatic failover
* You may connect to reader endpoints to reduce load on primary database for read operations with minimal replica lag (compared to RDS replication eventual consistency)
* Data is by default replicated 6 times accross 3 AZs
  
### Aurora Serverless v2
* On-demand autoscaling configuration for Aurora
* Automate the process of monitoring the workload and adjusting database capacity as needed
* Unlike serverless v1 which always doubled capacity on scaling, v2 can incrementally scale based on demand
* Charge only for the resources the DB consumes
* Configure capacity scaling by specifying ACUs (Auroa Capacity Units), with a minimum of 0.5 ACus and maximum of 128 ACUs. Each ACU is approximately **2 GiB** of memory, corresponding CPu, and networking
* You can mix serverless v2 and provisioned capacities if needed
* Use for
  * **Variable workloads**
  * **Multi-tenant applications**
  * **New applications**
  * **mixed-used applications** with periodic spikes in traffic
  * **Capacity planning**
  * **Development and testing**