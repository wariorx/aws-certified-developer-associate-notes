# Availability
* usually represented in the percentage of uptime in a given year or as a number of nines (the more nine the better)
* 90% = 36.53 days; 99% = 3.65 days; 99.999% = 5.26 minutes of downtime and so on.
* **Redundancy** is key to achieving high availability; more servers, more data centers, mroe replication. IE add additional isntances within the same AZ and use a load balancer to distribute traffic
* Challenges:
  * **Replication**: a process is needed to replicate resources and configurations accross instances (patches, applications, data, etc.)
  * **Customer redirection**: notify clients about changes; different servers (use DNS to mask IP changes though propagation and caching can interfere with this), use load balancers
  * **Even distribution**
  * **Type of availability**
    * Active-passive: only one of the 2 instances is available at a time. Good for stateful applications since the customer is always sent to the same instance, but hard to scale
    * Active-active: both servers take some load of the application, thus increasing throughput capacity. Problematic if statefulness is required but great for stateless apps
  
## Traffic routing with Elastic Load Balancing
* Load balancing is the proces of distributing tasks accross a set of resources; IE distribute requests amongst a set of servers
* A load balancer takes in all the traffic and redirects it ot the backedn servers based on an algoritm; often round-robin (one after another)
* Features:
  * **Hybrid**: Can load balance to specific IPs; so can redirect to on-prem servers
  * **High Availability**: AWS managed; must only deploy to multiple AZ
  * **Scalability**: automatically scales to meet demand

## Health Checks
* 2 Types:
  * Establishing a TCP connection to a backend EC2 instance and making it available if successful
  * Making HTTP or HTTPS request to a webpage that you specify and validating the rsponse code
* After determinining an instance is healthy, the load balancer will start sending traffic to it. If it becomes unhealthy, the instance will be unregistered and the load balancer will stop sending traffic
* Can prevent EC2 auto-scaling from terminating an instance if there are still lingering connections to it; this is known as **draining**

## Components
* **Rule**: associate a listener to a target group by mapping source IP of the client to a target group
* **Listener**: clients connect to listeners; defined by a port and protocol (depending on the type of load balancer). A load balancer can have many listeners
* **Target group**: defines the backend servers traffic will be directed to and their type (EC2, lambda, IP). Must include a Health Check

## Types of Load Balancers

### Application load balancer
* Layer 7 - ideal for HTTP and HTTPS
* **Routes Traffic based on request data** Can evaluate listener rules and analyze request bodies to determine which rules to apply; IE path, headers, method, source IP can be used to determine routing
* **Send responses directly to the client** Can send a fixed response such as a custom HTML oage. It can also send redirects; thus removing work from servers
* **TLS offloading** use an SSL certificate to pass HTTPS traffic through to backend applications. The certificate can either be created in ACM or imported via IAM or ACM
* **Authenticate users**: Can authenticate users before they pass through to the backend applications using OpenID Connect (OIDC) protocol and integrates with other AWS services to support:
  * SAML
  * LDAP
  * Microsoft AD
  * Others
* **Secure traffic** by using security groups to specify supported protocols and IP ranges
* **Sticky sessions** to send request that must be sent to teh same backend server for stateful applications by using an **HTTP cookie** to remmeber which server to send traffic to

### Network Load Balancer
* Layer 4 - Ideal for load balancing TCP and UDP traffic
* **Sticky sessions**
* **Low latency** for latency sensitive applications
* **Source IP** preserves teh client-side source IP
* **Static IP** automatically available per AZ
* **Elastic IP address** allows users to assign a custom, fixed IP address per AZ
* **DNS failover**: uses Route 53 to redirect traffic to load balancer nodes in other zones

### Gateway Load balancer
* Layer 3 Gateway; Layer 4 LB
* Deploy, scale, and manage 3rd party products like firewalls, intrusion detection tools, and prevention systems
* **High availability**: routes traffic to healthy virtual appliances
* **Monitoring** using cloudwatch
* **Streamlined deployments** use AWS marketplace to deploy new virtual appliances
* **Private connectivity** connects internet gateways, VPCS, and other network resources over a private network

### Chosing
| Feature | ALB | NLB | GLB |
| ------- | --- | --- | --- |
| Type    | layer 7 | layer 4 | layer 3 (gateway) layer 4 (load balancer) |
| Target type | IP, instance, lambda | IP, instance, ALB | IP, instance |
| Protocols| HTTP, HTTPS | TCP, UDP, TLS | IP |
| Static IP and Elastic IP | No | Yes | No |
| Preserce source IP | Yes | Yes | Yes |
| Fixed Response | Yes | No | No |
| User authentication | Yes | No | No |