# CloudTrail
* Service that helps enable operational and risk auditing, governance, and compliance within AWS accounts
* Actions taken by a user, role, or AWS service are recorded as events in Cloudtrail
* Always active, no manual setup required
* Three ways to record events:
  * **Event history** provides viewable, searchable, downloadable and immutable record of the past 90 days of events within a Region. You may filter events on a single attribute and is automatically enabled by degault
  * **Clodtrail Lake**: managed data lake for capturing, storing, accessing, and analyzing user and API activity on AWS/ Events are converted into row-based JSON format based on **Apache Orc** - optimized for fast retrieval of data
    * Princing varies based on retetion store - 10, 7 or 1 year
    * Event data stores may be per-account or accros accounts in an AWS Organization
    * Events can also be imported from existing Cloudtrail logs in an S3 bucket
  * **Trails**: events are automatically exported and stored into an S3 Bucket, with optional delivery to Cloudwatch logs and EventBridge for additional monitoring and automation.
    * May also use third party solutions or services like Athena to search and Analyze Cloudtrail logs
    * Can be used for single accounts or accross multipel accounts within an AWS org
    * You can use **log insights events** to analyze anomalous behavior and/or error rates
    * Encrypted using S3-SS3, but you may also use your own KMS key

## CloudTrail Channels
* Bring events from outside AWS into CloudTrail
* **Channels for Cloudtrail Lake integrations**: from external partners or custom sources. Choose from several data store options. The channel must have a resource policy attached that allows events through the channel
* **Service-linked channels**: AWS Services may receive CloudTrail evetns on your behalf 

## Event Types
* **Management**: also known as conytol plsnr oprtsyiond; provide information on account management operations on resources
  * Configuring security (IE AttachRolePolicy actions)
  * RegisteringDevices (EC2 createDefaultVpc)
  * Configuring Rules for Routing Data (EC2:CreateSubent)
  * Setting up loggign (CloudTrail:CreateTrail)
* **Data**: also known as data plane operations, often high-volume activites on operations performed on or in a resource
  * S3-Object activities
  * Lambda Invoke
  * PutAuditEvents
  * SNS Publish events
* **Insights**: capture unusal API call rate or error activity by analyzing Cloudtrail management activity. Provide releventa information such as API, error code, time, and statistics
  * Excessive use of DeleteBucket