# File Storage - EFS
* Treats files as a singular unit; similar to NAS (Network Attached Storage)
* Files ar organized in a tre-like hierarchy of folders and sub-folders.
* Files have metadata attached to them; file name, size, date created, etc.
* Ideal for **centralized access** to files taht must be easily shared and managed by multiple host computers. 
* Typically mounted onto multiple hosts and requires file locking and integration with existing file system communication protols
* **Use Cases**:
  * **Web Serving**: Common file protocols, naming conventions, and permissions are used which makes this ideal for web applications
  * **Analytics**: Analytocs tools interact with data through a file interface and rely on fetaures like file locks or writing to files. Cloud file storage also has the ability to scale capacity and performance
  * **Media and entertainment**:  Storage of rich media content for processing and collaboration can eb integrated for content production, digital supply chains, media streaming, broadcasting, archiving, and analytics
  * **Home directories**: Easy to lift and shift applications that need home directories since cloud storage addheres to many protocols commonly used for this.

# Block Storage - EBS
* Splits files into fixed-size chunks of data called blocks that have their own address; with each block being an individual piece of data storage.
* Similar to DAS (Directly Attached Storage)
* Blocks can be retrieved efficiently; thus offering a more direct route for data access
* When data is requested, addresses are used to organize blocks
* Only address is associated with a block; no additional metadata
* Change a character in a 1gb file will only affect the 1 block in which the character changed; thus being faster and using less bandwith
* Optimized for **low-latency operations**; thus prefered for high-performance enterprise workloads and transcation, I/O intensive operations and/or applications where **portability** is required.
* Use Cases:
  * **Tramsactional workloads**: low-latency, high-capacity, fault-tolerant databases. Allows for robust, scalable, and highly efficient transactions since each block is its own storage units
  * **Containers**: Block storages are flexible, scalable, and efficient to accomodate containers and make it easy to migrate them between servers, locations, and environments
  * **Virtual Machines**: OS, files, and resources can be storaged on block storage by formatting the block storage volume and turning it into a VM file system. Makes it easier to modify the virtual drive size and transfer the virtualize storage from one host to another
  
# Object Storage - S3 Buckets
* Files are stored as objects; each object treated as a single unit of data
* Objects are stored ina **flat structure** unlike the hierarchycal system of file storage.
* Each object has a **unique identifier** along with any additional metadata bundled with the data stored.
* Changing 1 characer in an object means the entire object must be updated, unlike in block storage
* Use Cases:
  * **Data archiving**: Long term data retention since it's cost effective to maintain objects (IE for regulatory purposes) for an extended period of time. Provides data durability, immediate retrieval times, security, and compliance.
  * **Backup and recovery**: Replicate contents so that if a physical device fails, duplicate objects become available Ensures that applications continue to run smoothly and/or replicate data accross multiple data centers
  * **Rich media**: Accelerate aplicatioins and reduce costs of storing rich media files such as videos and images. Easy to create cost effective and globally-replicated architecture to deliver media