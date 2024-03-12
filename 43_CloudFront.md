# CloudFront

* Web service tha speeds up distribution of static and dynamic web content
* Uses a worldwide network of edge locations (distributed data centers) to deliver content to users
* User requests are routed to the edge location that would provide the lowest latency

## Caching
* Edge locations / POPs handle request and have their own cache
* Regional edge cache helps bring more content closer to the viewers by storing content used accross POPs in the region
* When a request is received, the POP will first check its own cache, then the edge cache, and if all fails it'll request the content from the origin that matches the request (and cache the response back)
* TTL determined via combination of Cache-Control header from source plus cloudfront min and max ttl specifications
* Cache can be invalidated at any time via an invalidation request (sdk/cli/console)
  
## Use Cases
* **Accelerate static website content delivery**; ie S3 + cloudfront which has the additional benefit of restricting the origin access control to only the distribution
* **Video streaming**
  * Video on demand for streaming common formats like DASH
  * Broadcasting by caching media fragments at teh edge so multiple requests for the manifest file can be combined - thus minimizing load on origin server
* **Field-level encryption**: protect specific fdata fields throughout system processing in addition to the HTTPs security layer - so that only applications at  the origin can see those data fields
  * Add public key to cloudfront, then specify that you want to be encrypted with the key
* **Edge customization aka Lambda@edge**: running serverless code at the edge to optimize content customization and experiences for viewers
  * You can also serve private content from a custom origin in addition to using signed urls and cookies

## Cache Optimization
* **Specifying TTL**
* **Using origin shield**
* **Caching based on query string params, cookies, or headers**
* **Remove accent-encoding header when compression is not needed**