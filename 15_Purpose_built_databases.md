# DynamoDB
* Fully Manged NoSQL database
* Fast and consistent performance ast scale
* Flexible billing, Infrastructure as Code integraion, and hands-off operational model
* Database of choice for high-scale apps and serverless apps
  
# ElastiCache
* Fully-managed in-memory caching solution
* Supports two open-source caches; **Redis and Memcached**
* Manages instance failovers, backups, restores, and software upgrades

# MemoryDB for Redis
* Durable in-memory database taht delivers ultra-fast performance
* Achieve single-digit milisecond write latency with high throughput
* Multi-AZ
* Can be used as a fully-managed primary database for high-performance apps without having to manage cache and database separately
  
# DocumentDB (MongoDB compatibility)
* Fully-managed documetn database you can use to store query-rich documents in your app
* Works well for content management systems, profile management, and web and mobile applications.
* Compatible with MongoDB; you can also migrate existing MongoDb data DocumentDB

# Keyspaces (Apache Cassandra)
* Scalable, highly available managed Cassandra compatible database service
* Popular for high-scale applications taht need top-tier performance
* Good option for high-volume applications with straightforward access patterns
* Uses the same Cassandra Query Language, Apache 2.0 Drivers, and any other common Cassandra Tools
  
# Neptune
* Fully-managed graph database offered in AWS
* Goof choice for highly connected data with a rich variety of relationships
* Recommended for fraud detection and knowledge graphs

# Timestream
* Fast and scalable serverless time series database service for IoT and operational applications
* It makes it easy to store and analysze trillions of events per day up to 1000 times faster and for as little as one-tenth of the cost of relational databases.
* Ideal for measuring events that change over time, such as stock prices or temperature measurements

# Quantum Ledger Database (QLDB)
* Facilitates the tracking of transationcs and operations 
* Provides complete cryptographically verifiable history of all changes made to application data
* Ideal for aplpications that want to maintain change history and/or audit trails that must be verifiable

# Redshift
* Anakyze structured and semi-structured data accross data warehouses, operational databases, and data lakes
* Leverate AWS-designed hardware and machine learning

## Choosing the right database

| AWS Service | Database Type | Use Cases |
| ----------- | ------------- | --------- | 
| RDS, Aurora, Redshift | Relational | Traditional apps, ERP, CRM, e-commerce |
| DynamoDb | key-value | high-traffic web apps, e-commerce, gaming |
| Elasticache (Redis or Memcached) | In-memory | caching, session management, gaming leaderboards, geospatial apps |
| Document Db | Document | Content Mangement, catalogs, user profiles |
| Keyspaces | Wide column | High-scle industrial applications for equipment maintenance, fleet management, and route optimization |
| Neptune | Graph | Fraud detection, social networking, recommendation engines |
| Timestream | Time Series | IoT applications, DevOps, Telemetry |
| QLDB | Ledger | Systems of records, supply chain, banking transactions | 
| RedShift | Columnar | AI and ML applications, Business analysis |

