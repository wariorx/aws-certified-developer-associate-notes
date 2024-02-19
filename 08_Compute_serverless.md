# Fargate
* Abstract the EC2 instances so that management of the underlying OS and compute infrastructure are no longer customer responsibility
* Natively integrates with IAM and VPC; allowing for seamless permission and networking managements
* Allocates the right amount of compute required for the container tasks, so that the developer focus is on code.
* Supports bot ECS and EKS
* Provides workload isolation and security by design
  
# Lambda
* Upload and run workloads and applications without having to amange any instances or containers
* Only run when triggered; IE through an HTTP request or s3 file upload
* Run code for almost any application or backend service in a fully managed environment (high availalbility compute infrastructure)
* Lambda functions can be configured using Lambda API, AWS console, Cloud Formation, or the Serverless Application Model (SAM)
* Calls can be made using the AWS console, lambda API or a lambda trigger or invoke
* **Function**: resource taht can be invoked to run the Lambda code. Can be pcreated from scratch, from an AWS blueprint, using a container image, or from the AWS Serverless Application Repository
* **Trigger**: describe when a lambda function should run. Integrate Lambda with other AWS services and event source mapping; IE API call, or reading from a stream or queue.
* **Event**: json formatted payload to the lambda function. The event structure and contents can be determined by the user and/or the trigger service
* **Application Environment**: Provides a secure and isolated runtime environment for the lambda function. It manages the process and resources to launch the function (global, VPC subnets, etc.)
* **Deployment Package**: code package used to deploy the lambda. Allows for .zip files (uploaded or from S3) or from a **Open Container Initiative (OCI)** compatible image to which the function code and dependencies have been added along with the required OS and Lambda runtime
* **Runtime**: provides the language-specific environment taht runs in an application enviroment. You can specify from one of the built-in languages ( Python, Node.js, Ruby, Go, Java, or .NET Core) oruse a custom or community supported runtime.
* **Function Handler**: Method in the function taht processes the event. Lambda will run this method anytime the lambda is invoked. After exitting or returning a response, the handler moves on to processing the next event if any

## Lambda Billing
* Charged based on the number of times that the code is invoked AND for the time that the code runs; rounded to the nearest 1 ms of duration
* Can be more cost-effective than ECS when the execution time is very low

# Choosing the Right Compute Service
* EC2 might mean minimum refactoring and offering scalability
* Lambda would be best for infrequent, short tasks
* ECS is best for minimizing risks of prod deployments through rolling deployments, container portability (easy to spin up lower enviroments), ease of scalability (quicker than EC2 instances) 