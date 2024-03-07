# Eventbridge
* Serverless service that uses events to connect application components together
* Allow services to become loosely-coupled by emitting and responding to events - **event driven architecture**
* Ingest, filter, transform, and deliver events accross applications, AWS services, and third-party software
* Pipes often have event Buses as their targets

## Event Pipes
* Point to point integrations that connect sources to targets
* Support advanced transformations and enrichments
* Redyce the need for integration code when developing event-driven architectures
* Components
  * **Source**: data from a variety of resources; Dynamodb stream, Kinesis stream, SQS, Kafka, etc.
  * **Filters** can be setup to pipe only a subset of events
  * **Transformation**: map or enhance the event data before delivering it to the targets
    * **Enrichment**: use lambda functions to add additional data to events
  * **Target**: destination of the event data; Event Bus, API Gateway or API destinations, Cloudwatch logs, ECS tasks, Kinsesi Firehose, SNS, SQS, Step functions, etc.
* IAM permissions can be used to grant any permissions needed for the pipe (IE post SQS message to a queue)
  
## Event Buses
* Type of pipeline that receives events and delivers them to zero or more destinations aka Targets
  * Some AWS resources create their own managed Event Buses
  * Third party SAS events require Partner Busses
* Route events to multiple sources
* Resource-based policies can be added to event buses
* Associate Rules to evaluate events as they arrive and determine targets to send event to AND **trigger actions defined in the rule**

