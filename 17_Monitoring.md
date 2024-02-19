# Monitoring
* Collect and analyze operational data
* Near real-time pulse on systems; trouble shoot issues, resource usage, security events, etc.

## CludWatch
* Centralized location for monitoring service metrics such as logs, network traffic, and events
* Provides actionable insights into your applications
  * Detect anomalous behavior
  * Set alarms to alert of unusual or unexpected scenarios
  * Visualize logs and metrics
  * Take automated actions
  * Troubleshoot
* **Metrics**: Data about erformance system; such as the CPU usage of an EC2 instance. Always associated with a **timestamp** (created by cloudwatch if not provided by user or service)
* **Dimensions**: name/value pair that is part of a metric's identity. can be used to further filter results in cloudwatch

## Custom Metrics
* High-resolution custom metrics make it possible to stream metrics down to a granularity of one second (AWS services use standard resolution of 1 minute by default)
* Use **aws sdk or cli** to put custom metrics to cloudwatch; then attach dimensions as needed
```
aws cloudwatch put-metric-data --metric-name Buffers --namespace MyNameSpace --unit Bytes --value 231434333 --dimensions InstanceId=1-23456789,InstanceType=m1.small
```
Or publish a single data point
```
aws cloudwatch put-metric-data --metric-name PageViewCount --namespace MyService --value 2 --timestamp 2016-10-20T12:00:00.000Z
```

## Cloudwatch dashboards
* Customizable home pages you can configure to visualize cloudwatch metrics
  * Graphs
  * Text-based widgets
* Can pull data accross regions to get a better overview accross your account
* Specify a period of time to aggreate statistics
* Export cloudwatch data using the **GetMetricData API**, then use IAM policies to limit access.

## Cloudwatch Logs
* Centralized place for logs to be stored and analyzed
* Monitor, store, query, and access logs of apps in ec2, lambdas, etc.
* **Event**: a reocrd of activity recorded by the application or resource monitored - composed of a timestamp and event message
* **Stream**: group of events that belong to the same resource being monitored
* **Group**: log stream that all share the same retention and permission settins. 
* You can encrypt logs using KMS and restrict access and permissions using IAM

## Cloudwatch Alarms
* Alarms can be configured to trigger based on: 
  * Metric
  * Threshold 
  * Time period 
* IE more than 10 messages in a SQS queue for 5 mins = in alarm
* Alarms can initiate actions after being invoked; such as sending an SNS notification message OR triggering auto-scaling or running Lambdas
* Alarm status"
  * OK
  * ALARM
  * INSUFFICIENT_DATA: alarm has just started, so the metric is not availalble yet to determine status
* Use metric filters to turn **log data** into metrics that you can use to set an alarm
  * IE HTTP error responses sustained for a certain amount of time
  