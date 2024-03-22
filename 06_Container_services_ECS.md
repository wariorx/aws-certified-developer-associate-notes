# Containers
* Standardized unit that packages code and its dependencies and separates them from the rest of processes in the system through isolation
* Designed to run reliably in any platform since they are independent of their own environment.
* Simplify the process of exporting tasks from one environment to another; IE on-prem to cloud
* Containers share the same OS and Kernel s the host they exist on; as oposed to VMs where each one gets their own guest OS

## Container Orchestration
* The management of coordination, placement, error handling (containers and instances) and monitoring.
* In AWS, we can use EKS or ECS

## ECS
* Container orchestration service that allows you to create task definitions used to run an individual task or tasks within a service.
* Can be run in a serverless infrastructure maanged by AWS (Fargate) or run them on EC2 instance clusters managed by yourself.
* **Cluster**: logical grouping of services, tasks, and capacity providers in a region
* **Service**: one or more identical tasks. Are in charge of checking and replacing unhealthy tasks (always running)
* **Task**: on or more containers. Specify compute, networking, IAM role, and other configurations
* **Network modes**:
  * **Host**: most basic mode; the networking of the container is tied directly to the underlying host
    * The container will receive traffic in the same port as the instance
    * You can't run more than a single instance of the task on each host
    * No way to remap container port 
    * Allows containers to specify the host and connect to private loopback networks
    * Only supported for **EC2** instances
  * **Bridge**: use a virtual network bridge to creat a layer betwee nthe host and the networking of the container
    * Remap a host port to a container port
    * If port mapping is unspecified, Docker will choose a random unused port for the container to listen to -> ECS will automatically updated load balancer target groups and C loudMap service discovery with the list of task IP and ports
    * Cons: it's difficult to lock down service to service communcations because services may be assigned to any random unused port
    * Supported only for **EC2** instances
  * **AWSVPC**:  ECS creates and manages underlying ENI ( Elastic Network Interface) for each task and each task receives its own private IP addr within the VPC. The ENI is separate from the underlying hosts ENI - and if multiple tasks are run within an instance, each task receives it own ENI
    * Each task can have different security groups allowing or denying traffic
    * Supported for both EC2 and Fargate tasks (and required for Fargate)
    * Compatible with VPCs configured for IPv6 dual stack mode (Ipv4 cannot be disabled) - this allows you to use the Ipv6 space as well for networkimg
    * Can present additional challenges - see **ENI trunking** for solutions:
      * Could reach the limit of ENIs that can be attached to an EC2 instance 
      * IP address exhaustion

### Compute Options
* **EC2**: aka Container instances. Offers most control and user responsibility. ECS container agent must be installed on the EC2 instances to be able to work as an ECS cluster. The ECS agent runs in both Windows and Linux AMIs
* **EC2 Spot**
* **Fargate**: serverless ECS; instances are managed by AWS
* **Fargate Spot**

### Cluster Actions
* Laund and/or stop containers
* Get cluster state
* Scale in and out
* Schedule container placement
* Assigning permissions
* Meeting availability requirements (IE always 2 tasks running per service)

### Task Definition
* JSON text file that describes one or more containers; along with their configurations such as CPU, memory, ports, networking, or storage.
```json
{
    "family": "webserver",
    "containerDefinitions": [ {
    "name": "web",
    "image": "nginx",
    "memory": "100",
    "cpu": "99"
    } ],
    "requiresCompatibilities": [ "FARGATE" ],
    "networkMode": "awsvpc",
    "memory": "512",
    "cpu": "256"
}
```

## Scheduling
* 2 provided schedulers;
    * Service scheduler for long-running tasks and applications
    * Standalone scheduler for batch jobs, scheduled tasks, or single-run tasks
* You may also use EventBridge or create a custome scheduler that calls RunTask when needed

### Service Scheduler
* Ensures that the desired scheduling strategy is followed and that failing tasks are rescheduled as needed.
* Replaces tasks considered to be unhealthy via load balancer healthchecks or container health checks. Replacement depends on `desiredCount` and  `maximumPercent`.
    * The scheduler will start a new task whenever a task becomes unhealthy. If the new task is deemed healthy, it will replace the unhealthy one. Otherwise one of them will be terminated and the process will repeat periodically
* Available strategies:
    * **Replica**: replace and maintain the desired number of tasks across the cluster. You may add task replacement strategies and constraints to customize the replacement strategy
    * **Daemon**: deploy exactly one task on each active container instance that meets all of the task placement constraints that you specify in your cluster. Therefore, there is no need to specify the desired number of tasks, a placement strategy, or use service autoscaling policies 

### Standalone Tasks
* You may have a process call RunTask, allowing for default task placement strategy
* You may also specify task placement strategies and constraints
  
## Container stragegies

### Sidecar
* Deploying components of an application into a separate process or container for isolation and encapsulation
* Implement peripheral tasks that can be reused by many services as their own standalone components then attach them as a side-car to other taks (IE logging, monitoring, telemetry, etc.)
* Similar to agents and some daemons
* Need to specify networking logic for the tasks to communicate with one another - this also adds overhead and complexity
* Good for implementing common functionality that will be used for applications written in different languages or deployed into different environments
```json
[
     {
       "name": "my-sidecar-container",
       "image": "ECR image name",
       "memory": "256",
       "cpu": "256",
       "essential": true,
       "portMappings": [
         {
           "containerPort": "50051",
           "hostPort": "50051",
           "protocol": "tcp"
         }
       ],
       "links": [
         "app"
       ]
     },
     {
       "name": "app",
       "image": "<app image URL here>",
       "memory": "256",
       "cpu": "256",
       "essential": true
     }
]
```