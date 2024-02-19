# Route53
https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/route-53-concepts.html
* Managed reliable DNS service
* Globally available and highly scalable
* Highest availability SLA of AWS services (?)
  
**Note**: when working with DNS records, beware of caching of DNS records in local machines. You can configure the **TTL** of records to be values that you would be OK with waiting for the changes to take effect; IE 60 to 172800 seconds is the recommended range. You may also clear your local cache with commands like `ipconfig /flushdns` on Windows

## Features
* **Route resolver**: recursive DNS for VPCs in both regions and Outposts, or any other on-premises networks
  * Conditional forwarding rules can be used to resolve custom names mastered in a private hosted zones or on-prem
* **Domain registration**:  register a cutom domain using Amazon Registrar or an associate
  * A **public hosted zone** will be automatically creted along with the record
  * For traffic to be directed, you still need to creat records
* **Import**: records (zone file) and domains from outside AWS
* **DNS Firewall**: create domain lists and build firewall rules that filter outbound DNS traffic against these rules 
* **DNSSec**: signing available for all private and public hosted zones
* **Traffic flow management**: global traffic management; send users to the best endpoint for your application based on 
  * **Simple routing**: single resource in your ddomain
  * **Health and Failover**: configure active-passive failover
  * **Latency**: route to resources hosted in multiple regiosn to direct trafic to the Region that provides the best latency
  * **Geoproximity**: route traffic based on the location of your resources and, optionally, shift traffic from resources in one location to another
  * **GeoLocation**: route traffic based on the location of your users
  * **IP based**: route traffic based on the location of users and have IP addresses that the traffic originte from
  * **Multivalue policies**: respond to DNS queries with up to 8 healthy records selected at random
  * **Weighted policies**: route traffic to multiple resources in proportions that you specify. If all resources are weighted the same, Round Robin will be sued

**Note**: EDNS0 is used to estimate user location for latency, geoproximity, geolocation, and IP-based routing
  
* **Health checks, Monitoring, and DNS Failover**: monitor application health and automatically reroute visitors to alrenate locations to avoid outages - done by periodically sending requests to the target using HTTP, HTTPS or TCP
  * **Trigger Cloudwatch alarms** and notify support whenever underlying resources go down
* **Private DNS** for amazon VPC; manage custom domains for internal AWS resources wihtout exposing them to the internet
* **Integration** with other AWS services
  * ELB
  * S3
  * Lambda
  * API-Gateway
  * more
  
## Concepts
* **Domain name**: address name to accessa website or service over the internet
* **Domain registrar** company accredited by ICANN to process domain registrations for a specific top level domain
* **Domain registry** companyu that owns the right to sell domains that have a specfic top level domain and define the rules for registering a domain
* **Top-level domain**: last part of a domain name, types"
  * generic: give users an indeaof what the site or service is about; IE .io, .gg, .org
  * geographic: associated with a geographic area; often have geographic requirements 
* **Hosted zone**: a cpmtaomer fpr records that contain traffic routing information for a domain and all its subdomains. It has the same name as teh corresponding domain
* **Records**: include information about how to route traffic; domain name (must end with the hosted zone name), type (see below), and value (varies based on type, could be an IP addr or a name server for example)
  * **A**: Address record; most important type as it is the one that maps host name to IP addr
  * **AAAA**: A record for an IPv6 addr
  * **NS**: Name Server; it specifies the authoritative name server for adomain and helps determine where to find the IP addr for a record
  * **MX**: Mail exchange; shows where emails for a domain should be routed to - often a name server
  * **CNAME**: Cannonical name. point one domain to another domain. Beware of increased latency from having to resolve multiple domain names
  * **Alias**: record created within Route53 to route traffic to **AWS resources** such as Cloudfront distributions or S3 buckets