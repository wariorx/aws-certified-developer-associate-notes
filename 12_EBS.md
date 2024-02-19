# EC2 Instance Storage
* Temporari block-level storage for an instance.
* Storage is located on disks taht are physically attached to the host computer; therefore **the lifecycle of the data is tied to the lifecycle of the EC2 instance** aka deleting OR stopping instance = deleting storage
* Considered ephimeral storage
* Ideal for applications that **replicate data to other EC2 instances** such as Hadoop clusters, where the **speed** of locally attached volumes is required and **resiliency of distributed data** hepls achieve high-performance
* Also ideal for **temporary information storage that changes frequently**; buffers, cahces, etc.

# EBS
* Block-level storage that can be attached to an EC2 instance. 
* EBS Volumes act similar to external hard drives:
  * **Detachable**
  * **Distinct**: separate from the computer; remains even if the instance it's attached to is destroyed
  * **Size-limited**:
  * **1-to-1 connection**: Most EBS volumes can only be connected with one compute instance at a time. However, an EC2 instance can be connected to multiple EBS Volumes
**Note**: a multi-attach feature has been announced that permits providioned IOPS SSD (io1 and io2) volumes to be attached to multiple EC2 instances at a time; though this is not compatible with all instance types and instances must be within the same AZ
* **Scaling**:
  * **Vertically**: increase the volume size limit; max of 64 TiB
  * **Horizontally**: attach multiple volumes to the same EC2 instance since the relationship is one-to-many
* **Use cases**: retrieve data quickly that needs to be persisted long-term
  * **OS**: boot and root volumes can be used to store OS. AMI root devices are typically EVS volumes referred as **EBS-backed AMIs**
  * **Database storage**: can scale with performance needs of the database
  * **Enterprise apps**: which require high availability and durability
  * **Big data and analytics**: data persistence, dynamic performance adjustments, and ability to detach and reattach volumes as clusters resize

## EBS Volume Types
* **SSD**: used for transactional workloads with frequent read/write operations but small I/O size
  | Type | Families | Description | Size | Max IOPS | Max throughput | Multi-attach |
  | ------ | ------ | ------ | ------ | ------ | ------ | ------ |
  | General Purpose |gp3, gp2 | balance of price and performance for transactional workloads | 1GiB-16TiB | 16,000 | 250 MiB/s - 1,000 MiB/s | No
  | Provisioned IOPS | io2 Block Express, io2, io1 | high-performace SSD desigend for latency-sensitive transactional workloads | 4 GiB-64 TiB | 64,000 - 256,000 | 1,000 MiB/s - 4,000 MiB/s | Yes
  
* **HDD**: used for large streaming workloads that need high throuput performance
  | Type | Families | Description | Size | Max IOPS | Max throughput | Multi-attach |
  | ------ | ------ | ------ | ------ | ------ | ------ | ------ |
  | Throughput Optimized | st1 | low-cost HDD designed for frequently accessed, throughput-sensitive workloads | 125 GiB - 16 TiB | 500 | 500MiB/s | No|
  | Cold HDD | sc1 | Lowest cost HDD designed for infrequently accessed worklods | 125 GiB - 16 TiB | 250 | 250 MiB/s | No |

## EBS Snapshots
* Backups of EBS volumes.
* Incremental backups that only save the blocks on the volume that have changed after the most recent snapshot
* Written and stored in S3 - Redundantly stored in multiple AZs (handled by AWS; user does not interact with S3 this way)
* Snapshots can also be used to create new volumes **regardless of AZ or Region** since snapshots can be moved around. Therefore, this can be used to migrate data from one AZ to another

## Benefits
* **High Availability**: automatically replicated within its AZ
* **Persistent storage**
* **Encryption**: activated by user
* **Flexibility**: make changes on the fly to volume size, IOPS, etc.
* **Backup and recovery**: take snapshots of volumes and restore them
