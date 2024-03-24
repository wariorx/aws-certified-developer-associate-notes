# Elastic File System (EFS)
* Set-and-forget file system that automatically growsand shrinks as files are added or removed
* No provisioning or managing storage capacity and performance
* Use with **compute services and on-prem**
* Can connect hundreds or thousands of compute instances to an EFS instance at the same time while providing consistent performance to each instance
* Quickly create and configure file systems; pay only for the storage used from a range of classes
* **Standard storage** EFS Standard and EFS Standard-Infrequent Access (Standard-IA) offer multi-AZ resilience and highest levels of durability and availability
* **Zone storage**: EFS One Zone and One Zone-IA provide additional savings by saving data in a single AZ

# FSx
* Fully managed service that offers reliability, security, scalability, and a broad set of capabilities
* Provides **native compatibility** with third-party file systems
* Make it convenient and cost-effective to launch, run, and scale hihg-performance file systems in the cloud
* **Lustre**: Open-source file system designed for apps that require fast storage. Can be linked to data repos in **S3** or on-premise data stores.
  * Delivers throughput up to 1+ TB/s and millions of IOPS
* **NetApp ONTAP**: fully-managed service that combines familiar features, capabilities, and API operations of on-premise NetApp file systems with the agility and scalability of AWS. Can serve as a drop-in replacement for existing ONTAP deployments
* **OpenZFS**: Fully-managed file storage services that helps move data from aan on-prem ZFS or other **Linux-based** file servers to AWS without change app code or data management. 
  * Delivers leading performance for latency-sensitive and small-file workloads with popular NAS data management capabilities (snapshots, cloning)
* **Windows File Server**: fully-managed Windows file servers backed by a fully-native Windows file system. Provides file storage accessible over Service Message Block (SMB) Protocol and can serve as a drop-in replacement for on-prem Windows file servers
  * Removes the administrative tasks of setting up and provisioning file servers ansd storage volumes for Winodws apps