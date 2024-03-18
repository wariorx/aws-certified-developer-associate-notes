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

### Service Scheduler
* Ensures that the desired scheduling strategy is followed and that failing tasks are rescheduled as needed.
* Replaces tasks considered to be unhealthy via load balancer healthchecks or container health checks. Replacement depends on `desiredCount` and  `maximumPercent`.
    * The scheduler will start a new task whenever a task becomes unhealthy. If the new task is deemed healthy, it will replace the unhealthy one. Otherwise one of them will be terminated and the process will repeat periodically
* Available strategies:
    * **Replica**: replace and maintain the desired number of tasks across the cluster. You may add task replacement strategies and constraints to customize the replacement strategy
    * **Daemon**: deploy exactly one task on each active container instance that meets all of the task placement constraints that you specify in your cluster. Therefore, there is no need to specify the desired number of tasks, a placement strategy, or use service autoscaling policies 

### Standalone Tasks

### Eventbridge scheduling


## Container stragegies

### Sidecar
https://spacelift.io/blog/kubernetes-sidecar-container#:~:text=A%20sidecar%20container%20can%20implement,load%20on%20the%20primary%20containers.
