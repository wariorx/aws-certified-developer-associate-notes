# Step Functions

* Serverless orchestration service that integrates Lambda functions and AWS services to build business-critical applications
* Organize tasks (states) into state machines (workflow), a series of event-driven steps.

* **Standard workflows**: each step in the workflow executes exactly-once workflow execution, running up to one year. Ideal for long-running, auditable workflows
* **Express workflows**: each step has at-least-ince workflow execution, running up to five minutes. Thus, each step could execute more than once. Ideal for high-event-rate workloads like data streaming.=

## States
* **Pass**: passes its input to its output without performing work - useful for development and debugging or transforming JSON input using filters
* **Task**: single unit of work performed in the state machine by either Lambda or integrating with AWS services or third-party APIs
  * Activity: worker implemented and hsote by user
  * Lambda
  * AWS service: such as ECS Fargate (.sync)
  * HTTP task
* **Choice**: adds logical condition to the state machine using an array of choices and conditional functions
  ```json
  "And": [
  {
  "Variable": "$.keyThatMightNotExist",
  "IsPresent": true
  },
  {
  "Variable": "$.keyThatMightNotExist",
  "StringEquals": "foo"
  }
  ```
* **Wait**: Delays the state machine from continuing for a specified time, euther relative or specified in seconds freom the time the sate begins, or an absolute end time
* **Succeed**: stops an execution as successful
* **Fail**: stops an execution as a failure, unless caught by a catch block. Allows the user to add custom comments to the output
* **Parallel**: adds separate branches of execution to the state machine. Each branch is executed at the same time, starting with the sate named in that branch's StartAt and concurrently as possible. The state will wait until ALL paths finish execution before proceding - thus if either branch fails the entire Parallel step fails
```json
{
  "Comment": "Parallel Example.",
  "StartAt": "LookupCustomerInfo",
  "States": {
    "LookupCustomerInfo": {
      "Type": "Parallel",
      "End": true,
      "Branches": [
        {
         "StartAt": "LookupAddress",
         "States": {
           "LookupAddress": {
             "Type": "Task",
             "Resource":
               "arn:aws:lambda:us-east-1:123456789012:function:AddressFinder",
             "End": true
           }
         }
       },
       {
         "StartAt": "LookupPhone",
         "States": {
           "LookupPhone": {
             "Type": "Task",
             "Resource":
               "arn:aws:lambda:us-east-1:123456789012:function:PhoneFinder",
             "End": true
           }
         }
       }
      ]
    }
  }
}
```
* **Map**: Runs a set of workflow steps for each item in a dataset. Its iterations are run in parallel, making it possible to quickly process datasets
  * Inline processing: 
    * Default
    * Accepts only JSON input and receives an array from a previous step
    * Each iteration runs in the context of the workflow which contains the map
    * Upto 40 concurrent iterations
    * Execution history will be part of the parent state's
  * Distributed processing:
    * Ideal for large-scale parallel workloads like on-demand processing
    * Process large data stored in S3 in for example JSON or CSV files
    * Iterations will be processed in **child workflows** with their own separate history
    * Upto 10,000 parallel child workflows
  ```json
  "Validate All": {
    "Type": "Map",
    "InputPath": "$.detail",
    "ItemProcessor": {
        "ProcessorConfig": {
            "Mode": "INLINE"
        },
        "StartAt": "Validate",
        "States": {
            "Validate": {
                "Type": "Task",
                "Resource": "arn:aws:states:::lambda:invoke",
                "OutputPath": "$.Payload",
                "Parameters": {
                    "FunctionName": "arn:aws:lambda:us-east-2:123456789012:function:ship-val:$LATEST"
                },
                "End": true
            }
        }
    },
    "End": true,
    "ResultPath": "$.detail.shipped",
    "ItemsPath": "$.shipped"
  }
  ```

## Error handling
* Many error identifiers provided in States.TaskFailed format
* **Retrying after error** Task, Parallel, and Map states can make use of the Retry field (retry array). The retry option with the matching error name will be used
```json
"Retry": [ 
  {
    "ErrorEquals": [ "States.Timeout" ],
    "MaxAttempts": 0,
    "BackoffRate": 1
  }, 
  {
    "ErrorEquals": [ "States.ALL" ]
  } 
]
```
* **Fallback states**: Task, Map, and Parallel states can each have a field named Catch. When a state reports an error and there's either no retry or retrying fails to resolve the error,  Step Functions will scan the catchers. If there's a match, it'll transition to the Next field.
```json
"Catch": [ {
 "ErrorEquals": [ "java.lang.Exception" ],
 "ResultPath": "$.error-info",
 "Next": "RecoveryState"
}, {
  "ErrorEquals": [ "States.ALL" ],
  "Next": "EndState"
} ]
```
For error monitoring, Catchers return human-readable payloads as part of their outputs which can be then passed to downstream tasks or loggers:
```json
"Handle escaped JSON with JSONtoString": {
"Type": "Pass",
"Parameters": {
  "Cause.$": "States.StringToJson($.Cause)"
},
"Next": "Pass State with Pass Processing"
},
```
 
## Use cases
* **Function orchestration**: functions running one after another to perform different tasks
* **Branching**: run different functions based on the outcome of another
* **Error handling**
  * **Retry**
  * **Catch**: if a function fails, trigger another function to, for example, reset a process or log additional information
* **Human in the loop**
* **Parallel processing**
* **Dynamic parallelism**


## Service integrations
* **Request/response - default**: Step Functions will wait for an HTTP response and then progress to the next state. Step Functions will not wait for a job to complete
```json
"Send message to SNS":{  
   "Type":"Task",
   "Resource":"arn:aws:states:::sns:publish",
   "Parameters":{  
      "TopicArn":"arn:aws:sns:us-east-1:123456789012:myTopic",
      "Message":"Hello from Step Functions!"
   },
   "Next":"NEXT_STATE"
}
```
* **.sync job**: wait for a request to complete before progressing to the next state. The workflow will be paused until the sync task completes - if the step function is unable to cancel an aborted task, additional charges occur
  * State execution is stopped
  * A different parallel branch fails
  * An iteration Map state fails with an uncaught error
```json
"Manage Batch task": {
  "Type": "Task",
  "Resource": "arn:aws:states:::batch:submitJob.sync",
  "Parameters": {
    "JobDefinition": "arn:aws:batch:us-east-2:123456789012:job-definition/testJobDefinition",
    "JobName": "testJob",
    "JobQueue": "arn:aws:batch:us-east-2:123456789012:job-queue/testQueue"
  },
  "Next": "NEXT_STATE"
}
```
* **.waitForTaskToken job**: pause a workfliw until a task token is returned - for example pausing until human approval or a third party integration.

```json
"Send message to SQS": {
  "Type": "Task",
  "Resource": "arn:aws:states:::sqs:sendMessage.waitForTaskToken",
  "Parameters": {
    "QueueUrl": "https://sqs.us-east-2.amazonaws.com/123456789012/myQueue",
    "MessageBody": {
        "Message": "Hello from Step Functions!",
        "TaskToken.$": "$$.Task.Token"
     }
  },
  "Next": "NEXT_STATE"
}
```

  * The task token can be accessed in the `Parameters` field of a state definition  with teh designation `$$.Task.Token` , where $$ = context object. On the other hand, using $ = input field
    * So to specify a path in the parameters object, you can do something like:
    ```json
    "Parameters": {
      "Input.$": "$",
      "TaskToken.$": "$$.Task.Token"
    },
    ```
  * **Context object**: internal JSON object that contains information about the execution
  * **Configuring a heartbeat**: since a task waiting for atoken could potentially wait until execution reqches the one-year quota, you can configure a hearbeat timeout interval as part of the definition to prevent that:
  ```json
  {
    "StartAt": "Push to SQS",
    "States": {
      "Push to SQS": {
        "Type": "Task",
        "Resource": "arn:aws:states:::sqs:sendMessage.waitForTaskToken",
        "HeartbeatSeconds": 600,
        "Parameters": {
          "MessageBody": { "myTaskToken.$": "$$.Task.Token" },
          "QueueUrl": "https://sqs.us-east-1.amazonaws.com/123456789012/push-based-queue"
        },
        "ResultPath": "$.SQS",
        "End": true
      }
    }
  }
  ```