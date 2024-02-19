# SQS
* Fully managed Message queuing service for app integrations and de-copupling for micro-services, distributed systems, and serverless applications
* Messages in the queue can be encrypted at rest using KMS keys
* **best-effort ordering** Order is not guaranteed; 
* **Unlimited throuhgput**; scales as needde to deliver any number of transactions per second per API action
* **At least once delivery**: messages delivered at elast once, but occasionally more than one copy pf a message is delivered

## FIFO Queues
* Guarantee ordered messages and messages are delivered exactly once
* Lower throughput than regular SQS queues
* **High throughput**; up to 3000 nessages per seocnd with batching up to 300 messages per second without batching -> option to enable high throughput FIFO queues if needed
* **Exactly once processing** messages ar delivered once and remain available until a consumer processes and deletes it. Duplicates aren't allowed in the queue
* **First-in-first-out deliver**

## Features
* **Unlimited queues and messages in any region**
* **Payload size** of up to 256kb; billet for each 64kb. For extended messages, SQS extended library can be used which stores message payload in S3 (message sends a reference to that object)
* **Batches** send, recieve, ro delete messages in batches of up to 10 messages. Cost the same as single messages
* **Long polling**: reduce extraneous polling to minimize cost while receiving new messages as quickly as possible; long polling waits up to 20seconsd while the queue is empty for hte next message to arrive
* **14 day retention period**
* **Send and receive messages simultaneously**
* **Message locking** received messages become locked during processing. Other computers processing the emssage simultaneously will not be able to receive the message. If processing fails, the message becomes available again
* **Queue sharing** share queues anonymously or with specific accounts; restrivt by IP and time of day
* **Server Side Encryption** using KMS; the queue must have permissions to perform KMS actions using the key of choice
***Dead Letter Queuess**: handle messages that a consumer has not successfully processed with DLQ. When a message maximum recieve count is exceeded, SQS will automatically move the message to the DLQ associated with the original queue. DLQ must be hte same type as the source queue. Once issues have been remediated, messages from the DLQ can be moved back to the respective queue
    * DLQ metrics can be used to **trigger cloudwatch alarm** to take actions such as sending an email using SNS
* Integrate with other services such as
  * DynamoDB (via lambda)
  * RDS (via lambda)
  * EC2
  * ECS
  * Lambda
  * SNS
  * S3
