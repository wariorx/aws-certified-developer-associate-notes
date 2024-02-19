# Kinesis
* Serverkess streaming data service
* Collect, process, and analyze real-time streaming data 
* Ingest real-time data such as video, audio, application logs, website streams, and IoT telemetry data for ML and analytics
* Process and analyze data as it arrives and respond instantly
* **Streaming data**: data taht is emitted at high volume in a continuous, incremental manner with the goal of low-latency processing
  * Chronologically significant
  * Continuously flowing
  * Unique
  * Nonhomogenous
  * Imperfect
* Connects directly into other AWS storage services, like RDS, RedShift, or DynamoDb
* **Encryption** can be configured to use KMS to protect data at rest
* **Capacity mode**: determines how capacity is managed and how charges occur:
  * **On-demand**: AWS maanges shards; charged only for actual throughput that you use
  * **Provisioned**: specift the number of shards for hte stream. Charged at an hourly rate based on the number of shards provisioned
* **Retention period**: length of time data records are accessible after they are added to the stream; defaults to 24hours; up to 365 days (additional charges for longer retentions than default)


## Elements
* **Producers**: continuously push data onto the stream. 
  * Data must contain a stream name, value, and sequence number
  * Must buffer or temporarily group data by stream name
* **Consumers**: process data in real time and move it to a storage layer
  * Storage layer smust support record ordering and strong consistency
  * May also process data and send it back into the stream for other consumers to use
* **Data stream**: group of shards
* **Shards**: uniquely identified sequence of data records in a stream. Each shard has a FIXED unit capacity -> the more shards, the higher the stream capacity
  * Support of to 5 READ transactions per second up to 2 MB
  * Support of up to 1000 WRITE record per second, up to 1 MB
  * **Hot/cold shards**: shards receiving much more or less data than expected (due to partition key hashing MD5) --> solve via Resharding
* **Data record**: unit of data in kinesis; coposed of: 
  * **Partition Key**: used to group data by shard within a stream. Required to put data into the stream; max length of 256 chars
  * **Sequence number**: unique per-partition key and assigned by kinesis; generally increase over time for the same partition key


## Resharding
* Process of modifying the number of shards on a stream to adapt to changes in the data flow
* Always a pairwise process
* Typically done as an administrative task; not by producers or consumers
* Partition range always given by the following:
```
shard.getHashKeyRange().getStartingHashKey();
shard.getHashKeyRange().getEndingHashKey(); 
```
* After resharding, the stream becomes **inacvtive** while the operation is taking place
  
### Shard Split
*  Divide a single shard into 2 shards to a
*  Increase cost, but increase capacity
*  (guide)[https://docs.aws.amazon.com/streams/latest/dev/kinesis-using-sdk-java-resharding.html]
* Specify how hash key values for the parent shard should be distributed to children -> specify value in a range (inclusive)

### Shard Merge
* Combine 2 shards into a single shard
* Reduce cost, but reduce capacity
* Shards must be **adjacent**" the union of the hash key ranges for the two shards froms a contiguous set with no gaps; IE 1,2,3 AND 4,5,6

### Shard Status after resharding
* Parent shards are not deleted after resharding;
  * Data in flight is re-directed to the children
  * Data already in a parent shard can still be accessed using **shard iterator** temporarily
* Status
  * **Open**: shard has not been resharded recently; open for new data and reading current data
  * **Closed**: shard was recently resharded; new data will be redirected
  * **Expired**: all data in the shard has expired; thus so has the shard
  
## Kinesis Data Streams
* [Creating a stream](https://docs.aws.amazon.com/streams/latest/dev/fundamental-stream.html)
  
## Kinesis Video Streams
* Simplify secure video streaming from connected devices to AWS for analytics, ML, playback, and further processing
* Automatically scale infrastructure to ingest video data from millions of devices
* **Features**
  * Server side encryption
  * Video data indexing
  * API data access
  * Playback live and on-demand viewing
  * Integration with Rekognition Video and open source libraries
* For applications that require ultra-low latency and two-way real time communication


### Elements
* **Producers**: video devices usin AWS SDK
* **Stream**: ingest, durably store, encrypt, and index media streams
* **Consumers**: sage maker, tensor flow, custom media processing, etc.

### Use Cases
* Smart homes
* Smart city
* Video applications


## Kinesis Data Firehose
* Capture and automatically load streaming data into S3 and Redshift
* Acquire, transform, and deliver data streams within seconds to data lakes, warehouses, and analytics services
  
### Elements
* **Source**: source data streams can be Aws Managed Streaming for Kafka (MSK), a Kinesis data stream, or Producer data from the Firehose Direct PUT API, Cloudwatch Logs, SNS, etc.
* **Transformation**: can convert data between specific formats, decompress, or perform custom data transformations using AWS Lambda or partition input records based on attributes to deliver to different locations
* **Destination**: S3, OpenSearch, Redshift, Splunk, Snowflake, or a custom HTTP endpoint. Can have multiple destinations for the same stream
* **Stream**: 
* **Record**: data of interest in the stream; can be as large as 1000 KB
* **Buffer**: incoming data is retained to a certain size or for a certain period of time before delivering to the destination. Specified in
  * **Buffer size** 
  * **Buffer interval**
* **Intermediate bucket**: in case of delivery errors or other issues, Firehose can deliver records to an s3 bucket first and then deliver a copy of those records to the intended destination; IE DynamoDb or Redshift

## Kinesis Data Analytics
* Managed Apache Flink; stateful computations over data streams
* Leverage open source 
* Take data from a Producer stream (MSK, S3, kinesis Data Strean), query and analyze the data, the output it to other AWS services or analytics tools
* Use for **real-time analytics**
* Process records **exaactly once**. The service ensures all data is processed even in the event of application disruption