# Open Search
* Open source distributed search and analytics suite derived from Elasticsearch
* AWS offers a managed version of OpenSearch
* Supports multiple query languages, not just Open Search DSL (domain specific language)
* In-place upgrades of underlying OpenSearch version with no downtime

## Use Cases
* Perform interactive log analytics, real-time application monitoring, webs searches, etc.
* Visualize data using OpenSearch dashboards
* Search unstructured and semi-structured data; document, messages, databases, etc.
* Detect anomalies usin ML and trigger alerts

## Elements
* **Input**: log files, messsages, metrics, document lists, and config info
* **Output**" search, analyze, and visualize data and/or act as a Producer to downstream data streaming services such as Kinesis
  
## Storage tiereing
* **Ultra warm**: jot storage for fast retrieval of frequently accessed data. compliments Hot Storage tier by providing less expensive storage for older and less frequently used data. Data is stored in S3 and a cache is used to prefetch and quickly query data
  * Up to 3 PB of data per cluser
* **Hot storage**:
* **Cold storage**: lowest cost storage option to retain infrequently accessed data in S3 and pay only for ocmpute when needed


## Instance types
* **OR1**
* **I3**
* **C5**
* **T3**
* more