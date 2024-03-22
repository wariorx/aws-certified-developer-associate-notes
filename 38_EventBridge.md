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
* Every account has a default event bus which automatically receives events from AWS services

## Eventbridge scheduling
* Create, run, and manage tasks at scale without provisioning or managing underlying infrastructure
* Provides at-least-once event delivery to targets, and you can create schedules that adjust to different delivery patterns by setting
  * **Time window**: allows you to start a schedule within a window of time - tasks are dispersed accross the time window to reduce the impact of multiple requests on downstream services
  * **Maximum retention time**: maximum time to keep an unprocessed event in the scheduler. If the target is not responding, the event is either dropped or sent to a DLQ
  * **Retries**: with exponential bacjoff to help retry failed task with delayed attempts
  * **Dead letter queue**: leverage SQS where events that fail to get delivered to the target get rerouted
* By default, the event will be tried for 24h a maximum of 185 times
* Encrypted by default with a AWS owned key, but you may also use your own
* Eventbridge **rules** can also be used to schedule tasks, but at scale scheduler is best suited
* Event bridge will usually need both an **assume role** to invoke aws services and a **pass role** that it can assign to downstream services called (IE ECS fargate tasks)

### One-time schedule
* One time tasks can also be scheduled using the event bridge scheduler
* To schedule a one time job, use the **at()** function when specifying the schedule-expression, thus indicated that the event should be run at a specific date and time
```bash
aws scheduler create-schedule --name SendEmailOnce \ 
--schedule-expression "at(2022-11-01T11:00:00)" \ 
--schedule-expression-timezone "Europe/Helsinki" \
--flexible-time-window "{\"Mode\": \"OFF\"}" \
--target "{\"Arn\": \"arn:aws:sns:us-east-1:xxx:test-chronos-send-email\", \"RoleArn\": \" arn:aws:iam::xxxx:role/sam_scheduler_role\" }"
```

### Scheduling groups
* Organize schedules by using group tags that can be used for cost allocation, access control, and resource organization


### Scheduling recurring schedule\
* On the other hand, recurring jobs can be scheduled using cron and rate expressions:
```bash
aws scheduler create-schedule --name SendEmailTest \
--group-name ScheduleGroupTest \
--schedule-expression "rate(5minutes)" \
--flexible-time-window "{\"Mode\": \"OFF\"}" \
--target "{\"Arn\": \"arn:aws:sns:us-east-1:xxxx:test-chronos-send-email\", \"RoleArn\": \" arn:aws:iam::xxxx:role/sam_scheduler_role \" }"
```
  

