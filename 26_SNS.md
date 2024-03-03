# SNS - Simple notification service
* Fully managed Publisher/Subscription service for application-to-application and application-to-person notifications
* Decouple distriburd applications
* Notify customers with SMS, push notifications, and emails
* High-throughput, push based many to many messaging between distributed systems
* **Publishers** post messages into a queue topic using API calls

## A2A
* Compatible Subscribers
  * SQS
  * Kinesis dat afirehose
  * AWS Lambda 
  * HTTPS endpoints
## A2P
* Email notifications
* SMS notifications
* Push notifications

## Features
* **FIFO topics**: message duplication may occur; ordering is not guaranteed
* **Standard topics**: message de-duplication is managed by AWS. order is guaranteed
* **Message durability**
  * Messages are stored accross multiple datacenters
  * **Delivery retry policy** used if a subscruber is not availalbe
  * Use **DLQ** to preserve messages taht aren't delivered before the retry policy ends
  * **Message attributes** provide any arbitrary metadata about the message
  * **Message filtering** each subscriber receives every message pushed to the queue by default. Subscribers may also define message filtering policies based on  
    * Payload
    * Attributes
  * **Server side encryption** using AWS KMS
  * **Private connection** between SNS and VPC when needed

## Fanout strategy
* Messages published to an SNS topic are replicated and pushed accross to multiple endpoints; such as Kinesis Firehose or SQS queues
* Allows for parallel async processing
* For example, one queue can be used for request fulfilment, and another one for parallel processing
* Duplicate data accross encironments for testing and validation

