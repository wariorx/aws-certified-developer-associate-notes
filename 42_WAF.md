# Web Application Firewall
* Firewall that lets you monitor HTTP request forwarded to your protected web application resources
* Compatible with CloudFront, API Gateway REST, load balaancer, AppSync, Cognito user pools, etc.
* Usage capacity measured in Waf Capacity Units. Each Rule has a set amount of capacity units required to run it, and running more than a certain amount of WCUs per ACL or Group will net additional costs
* **Intelligent mitigation** options available for bot control, fraud detection, account take over attempts, etc.
  
## Concepts
* **Web ACL**: used to protect a set of AWS resources by defining rules for inspecting web requests and identifying actiosn to take on matching requests
  * Default actions can be configured to indicate whether to block ro allow all requests that haven't been blocked or allowed
  * Rules will be evaluated by priority (lowest to highest)
* **Rules**: Statement that defines the inspection criteria and an action to take if a web request meets the criteria. Can be defined directly under a Web ACL or under a Group
  * Allow: forward to the protected service
  * Block: bloc kthe request and erply with 403 forbidden by default
  * Count: count the request. This does not determine whethe to allow or block
  * CAPTCHA and Challenge: veridy that the request is not coming from a bot
* **Rule Groups**: Reusable sets of rules that can be used accross multiple WebACLS
  * Rules in a group can be overriden by configuring the group in the ACL, without affecting other ACLs using the same group
  
## WAF Shield
* Further leverage web ACLS to mitigate the effects of DDoS attacks
* **AWS Shield Standard**: automatically included at no extra cost beyond WAF charges
* **Shiled Advanced**: provides expanded DDoS protection for load balancers, cloudfront, route 53 hosted zones, and AWS Global Accelerator for additional costs.
  
## AWS Firewall Manager
* Simplifies administration and maintenance tasks accross multiple accounts and resources for a variety of protections such as WAF, Security Groups, and Network firewalls.
* Protect reosurces for all accounts
* Automatically add protection to resources added to an account