# Dynamodb
* Fully-managed NoSQL database service that provides fast and predictable performance that scales seamlessly
* Create database tables that can store and retrieve any amount of data and serve any level of request traffic
* Scale up or down table throughput capacity without downtime or performance degradation
* Monitor resoruce usage and performance metrics
* Automatically spread data and traffic over a sufficient number of servers to handle throughput and storage requirements.
* All data is stored in SSDs and automatically replicated accross AZs in a region

## Core components
* **Tables**: A collection of data (0 or more items) similar to traditional db systems
  * **Partition Key** (required): part of the table primary key; hash value used to retrieve items from table and allocate data accross hosts for scalability and availability
  * **Sort Key**: Second part of the primary key; allows you to sort or search among all items sharing the same partition key
* **Items**: group of attributes (1 or more) that is uniquely identifiable amongst all other items. There is no limits to the number of items that can be stored in a table
* **Attributes**: fundamental data element; ie string, number, etc. Similar to fields or columns in traditional db systems
  * String
  * Number
  * Binary

### Primary Keys
* Each item on a table has a unique identifier known as the primary key which distringuishes from all other items in the table
* The primary key is hashed to determine the partition (physical storage) in which the item will be stored
* **Partition key**: simple primary key compose of one attribute
* **Partition key and sort key**: composite primary key composed of two attribbutes; the partition key and the secondary sort key

### Secondary Indexing
* Allows you to query data in the table using an alternate key in addition to queries against the primary key.
* Indexes are maintained automatically upon adding, removing, or udpating records
* Specify which attributes will be projected onto the index from the table
* **Global secondary index**: An index with a partition key and a sort key that can be different from those on the table.Limit 20.
* **Local seocnadry index**: index that has the same partition key as the table, but different sort key. Limit 5

```json
//table
{
    "Artist": "No One You Know",
    "SongTitle": "My Dog Spot",
    "AlbumTitle": "Hey Now",
    "Price": 1.98,
    "Genre": "Country",
    "CriticRating": 8.4
}    

//index
{
    "Genre": "Country",
    "AlbumTitle": "Hey Now",
    "Artist": "No One You Know",
    "SongTitle": "My Dog Spot"
}
```

## Data Types
* **Scalar types**: exactly one value; number, string, binary, Boolean, null
* **Document types**: complex structures with nested atrributes; IE json objects
* **Set Types**: Multiple scaler values; string set, number set, binary set
## Use Cases
* Scalability requirements not provided by current database system in use
* New app development
* Working with an OLTP (Online Transaction Processing) workload - which reqquires real-time updates (IE media analysis)
* Mission-critical applications taht must be highly available without manual intervention
* High durability is required
`

## Security
* Highly durable storage infrastructure with redundant copues accross AZs
* Encryption at rest using AWS KMS. All user data is encrypted by default.
* IAM permissions: IAM roles can be used both for console access AND database authorization to perform actions
* Protexted by AWS global network procedures

### Encryption
* Using KMS keys, you may specify from one of the following key types
  * **AWS Owned**: default encvryption type owned by Dynamodb at no charge
  * **AWS Managed Keys**: Key stored in user account and managed by KMS
  * **Cusomter Managed Keys**: Key is stored in the users account and is created, owned, anad managed by the user
## Monitoring
* CloudTrail: 
  * all KMS key actions are recorded in cloud trail. These logs can be used to audit trail data actions on the database
  * DynamoDb records actions in cloudtrail for logging and monitoring

## Consistency
* Provides **read-commited isolation** which ensures that read operations alrways return commited valyes for an item; but those not prevent modifications immediately afer a read
* **Eventually consistent reads**: default read conssitent model for all rad operations. The read responses may not reflect the results of a recently completed write opperation; but after a short delay a new read should match the latest write.
* **Strongly consistent reads**: Oeprations such as GetItem, Query, and Scan provide an optional ConsistentRead paramter; on which a response with the most up-to-date data will be returned. Only supported on tables and local secondary indexes


## Operations
* GetItem
* Query
* Scan: return one or more items and item attributes by accessing every item in a table or secondary index. You may provide a FilterExpression. Size limit 1 MB, after that you will have to issue a ContinueScan.
  * Parallel Scan
  * Limit parameter: specify a number of items at which the scan will stop, before reaching the default limit
* PutItem
* DeleteItem
* DescribeTable

## Capacity
* Contronls charges for rad and write throughput and how capacuity is maanged
* Set at table creation time
* Request capacity costs:
  * **Read requests**: 1 read request unit (**RCU**) = 1 item of up to 4 kB
    * Strong consistency: 1 unit
    * eventually consistent: 1/2 unit
    * transactional: 2 units
    * reading larger items than 4KB requires additional red request units
  * **Write requests**: 1 write request unit (**WCU**) = 1 item of up to 1 kB. Larger items require more units
### On Demand
* Flexible billing options
* Workloads are accomodated as teh ramp up or down to any previously reched traffic level; and rapidly adapt to new peaks
* **Pros**: tables with unkown workloads, handle unpredictable traffic, pay only for what you use
* 
### Provisioned
* Specify the number of reads and writes per second that you require for oyur application
* Adjust **auto-scaling** to automatically respond to traffic changes
  * 2 Cloudwatch alarms are created on the user's behalf when enabled; one for notifying adminsitrators and one for auto-scaling the database
* **Pros**: handle predictable workloads for applciations with consistent or gradual ramps; forecast capacity requirements to control costs
* 
## Dynamodb Streams
* Optional feature that captiures data modification events in Dynamodb tables ina near-realtime ordered stream
* Each event is represented by a **stream record**. Records are written when
  * An item is added; on which all attributes of the item are captured
  * An item is udpated; on which before and after states of modified attributes are capptured
  * An item is deleted, on which all attributes before deletion are captured
* Records ALWAYS contain
  * Table name
  * timestamp
  * Metadata
* Records have a lifetime of 24hours after which they are automatically removed from the stream
* **Trigger**: automaticall yrun a lambda function whenever an event of interest appears in the stream. For example, you could send a welcome email to new users by having the lambda filter new records and using SES (Simple Email Service) to notify new customers.
* Streams can also be used for **data replication** within a region, **view materialization** of tables, and data analysis using **Kinesis**