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