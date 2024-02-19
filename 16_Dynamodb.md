# Dynamodb
* Fully-managed NoSQL database service that provides fast and predictable performance that scales seamlessly
* Create database tables that can store and retrieve any amount of data and serve any level of request traffic
* Scale up or down table throughput capacity without downtime or performance degradation
* Monitor resoruce usage and performance metrics
* Automatically spread data and traffic over a sufficient number of servers to handle throughput and storage requirements.
* All data is stored in SSDs and automatically replicated accross AZs in a region

## Core components
* **Tables**: A collection of data (0 or more items) similar to traditional db systems
  * Partition Key (required): part of the table primary key; hash value used to retrieve items from table and allocate data accross hosts for scalability and availability
  * Sort Key: Second part of the primary key; allows you to sort or search among all items sharing the same partition key
* **Components**: group of attributes (1 or more) that is uniquely identifiable amongst all other items. There is no limits to the number of items that can be stored in a table
* **Attributes**: fundamental data element; ie string, number, etc. Similar to fields or columns in traditional db systems
  * String
  * Number
  * Binary

## Use Cases
* Scalability requirements not provided by current database system in use
* New app development
* Working with an OLTP (Online Transaction Processing) workload - which reqquires real-time updates (IE media analysis)
* Mission-critical applications taht must be highly available without manual intervention
* High durability is required

## Security
* Highly durable storage infrastructure with redundant copues accross AZs
* Encryption at rest using AWS KMS
* IAM permissions: IAM roles can be used both for console access AND database authorization to perform actions
* Protexted by AWS global network procedures


## Monitoring
* CloudTrail: 
  * all KMS key actions are recorded in cloud trail. These logs can be used to audit trail data actions on the database
  * DynamoDb alos records actions in cloudtrail for loggin and monitoring