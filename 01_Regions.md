# Regions

* Named after a **geographical location and region code**. They also have a unique geographical name; IE us-east-1 aka N.Virginia
  
## Choosing regions
* **Latency**: regions that are closer geographically offer lower latency. Async workloads can be affected by high latency
* **Pricing**: vary by region
* **Service Availability**: not all services are available in all regions
* **Data Compliance**: must comply with physical storage location regulations

# Availablility Zones

  * Consist of one or more datacenters with redundant power, networking, and connectivity
  * Named by appending a letter to the end of the region code; IE us-east-1a, us-east-2b

# Service Scope
 * Services are deployed at either the **Global, Region, or AZ level**
 * For Region and Global services, AWS manages resiliency and availablility
 * For AZ level services, always use **at least 2 AZ**
  
# Edge Locations
* Global locations where content is **cached** to provide users with the lowerst possible latency regardless of their physical location