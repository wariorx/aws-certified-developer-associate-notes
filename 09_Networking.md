# Networking
* AWS uses CIDR notation for IP addresses
* the smallest CIDR available for a network in AWS  is /28 (16 IP addresses); and the largest is /16 which provides 65,536 IP addresses.

# VPC
* Isolated network created in the AWS cloud
* Specify:
  * Name
  * Region
  * IP range in CIDR notation; up to 5 CIDRS - one primary and 4 secondaries

## Subnets
* Networks within the network; aka VLANs
* Used to provide high availability and connectivity options for resources;
* Can be public (internet access) or private (isolated from the internet)
* Must specify:
  * VPC
  * AZ within VPC region
  * IPv4 CIDR block for the subnet; which must be a subset of the VPC CIDR

## Achieving High Availability within a VPC
* At least 2 subnets; each in a different AZ

## Reserved IPs
* 5 IP addresses reserved by AWS for routing, DNS, and network management. For a network in the range 10.0.0.0/22
  1. 10.0.0.0 Network address
  2. 10.0.0.1 VPC local router
  3. 10.0.0.2 DNS server
  4. 10.0.0.3 future use
  5. 10.0.2.255 network broadcast
   
## Gateways
* Acts as a modem for the subnet; it allows for internet connectivity.
* Gateway in subnet = public subnet
* Highly availalbe

### Virtual Private Gateway
* Connects a VPC to another private network
* VPC Gateway on the VPC side of the connection; custeomer gateway on the other side of the connection
* Customer gateway: physical device or software application that allows for a directly, encrypted connection with an AWS VPC

## AWS Direct Connect
* Establishes a secure physical connection between an on-premise data center and AWS VPC; it's a firect connection to an AWS data center over standard fiber optic ethernet. 
* Allows users to create virtual interfaces directly to public AWS services or to a VPC

## Routing
* Route = rules that determine how to direct network traffic
* **Route table**: contains a set of routes that are used to determine where network traffic is directed
* **Main Route Table**: default route table created by AWS when a VPC is added. AWS assumes that network traffic is allowed between subnets in the VPC; if a subnet does not have its own route table, then the main route table is used
  * Cannot be deleted
  * Gateway route tables CANNOT be the main route table
  * Can be replaced with a custom subnet route table
  * Routes can be added, deleted, or modified
  * Can be explicitly associated with a subnet; though implicit association is also allowed
* **Custom route tables**: allows you to provide different routes on a subnet basis; IE to access resources outside of the VPC
  * New custom route tables will always have a local route already in them to allow for communication between all resources and subnets within the VPC
  * BEST practice: specify route table for each subnet and leave the main route table in its original state.

## VPC Security

### Network ACLS
* Virtual firewall at the subnet level
* Controls what kind of traffic is allowed to enter or leave teh subnet
* Configured through filtering rules
* **Stateless**; even responses to a request that was allowed will be filtered out if no outbound rule exists to match the response.
* **Default**: 
  * Inbound: Allow ALL IPV4; all Protocols, All port ranges from 0.0.0.0/0 (internet). 
  * Outbound: Allow ALL IPV4; all Protocols, All port ranges to 0.0.0.0/0 (internet). 
* **Example Custom ACL**:
  Inbound
  | Rule # | Source IP | Protocol | Port | Allow/Deny | Comment |
  | ------ | --------- | -------- | ---- | ---------- | ------- |
  | 100    | All IPv4  | TCP      | 443  | ALLOW      | Allows inbound HTTPS from anywhere |
  | 130 | 192.0.2.0/24 | TCP | 3389 | ALLOW | Allows inbound RDP traffic to the webswerver form home newtork public IP range |
  | * | All IPv4 | All | All | Deny | Denies all inbound traffic not already handled |

  Outbound
  | Rule # | Destination IP | Protocol | Port | Allow/Deny | Comment |
  | ------ | --------- | -------- | ---- | ---------- | ------- |
  | 120    | 0.0.0.0/0  | TCP      | 1025-65635  | ALLOW      | Allows outbound responses to clients requesting the website |
  | * | 0.0.0.0/0 | All | All | Deny | Denies all outbound traffic not already handled |

### Security Groups (EC2)
* Virtual firewalls 
* **Stateful**: temporarily allow traffic reponses inbound as along as it was initiated by the EC2 instance
* **Default**: BLOCK all inbound but ALLOW all outbound
* To accept internet traffic, the inbound ports must be opened
* **Custom inbound rules**:
  | Type | Protocol | Port Range | Source |
  | ------ | --------- | -------- | ---- |
  | Http (80) | TCP (6) | 80 | 0.0.0.0/0 |
  | Http (80) | TCP (6) | 80 | ::/0 |
  | Https (443) | TCP (6) | 443 | 0.0.0.0/0|
  | Http (443) | TCP (6) | 443 | ::/0 |

* Can also be used to segreate traffic between computers; organize resources into different groups and create security groups for each to control communication between them.
* Don't need to specify a Source IP; OTHER security groups can also be specified as a source/destionation