# API Gateway
* Service that facilitats the creation, publishing, maintenance, monitoring, and security of APIs at scale
* Deploy private (in VPC), regional, or global APIs
  

## Features
* Traffic management
* TEST methods with sample requests and responses
* CORS
* Authorization & Access control
  * IAM
  * Cognito
  * OpenID Connect and OAuth2
  * Custom (Lambda authorizer)
* API Keys
  * Tracking
  * Usage plans + Throttling
  * Secondary auth mechanism
* Throttling
* Monitoring through Cloudwatch
* Version Management: run multiple versions of the same API simultaneously
* SDK generation: Generate client SDKs for testing and quickly distributing SDKs to third-party developers
* Custom domain name support
* Transform and/or validate request and response data on the fly before it reaches the intended destination
* Connect to CloudFront
* Import from an OpenApi2 json or yaml file


## API Components
* **Routes**: exposed paths clients can connect to
* **Integrations**: backend endpoints connected to routes
  * **Lambda function**: require IAM role with permissions to call the lambda
  * **HTTP**: if not proxy; integration request and responses must be configured.
  * **AWS Service**: expose service actions such as posting a message to SQS
  * **Mock**: return a response without sending the request to a backedn; such as a custom 404 response
  * **VPC Link**: connect API to a resource within a private VPC
* **Stages**: snapshot of the API used to defined which deployment is available; allows you to have multiple versions or rollback changes
  * Caching
  * Throttling
  * Usage plans
  * Generate SDK
  * Use different stages per-environment or customer
  * Canary deployments to test new versions
  * Use **stage variables** to injext stage-dependent changes such as 
    * URLs
    * Lambda functions (IE call different lambda aliases base on stage var)
* **Authorizers**
* **Custom hostnames**: map a url domain to your API for a more user-friendly experience
  * Integrates with ACM 
  * Still require Route53 record
  * Default url: https://{unique_id}.exececute-API.{region}.amazon.com/{stage}/{path}/{resource}
* **Resources**: addressable resources as a tree of API entities; relative ot the base url path
  * **HTTP proxy**: any http endpoint. Must specify method. IE EC2, load balancer (require VPC Link if within VPC )
  * **Lambda proxy**: any lambda. API must be able to assume a role with **lambda-invoke** permissions (same for lambda-alias if using one)
  * Option to include a proxy resource as well, the greedy parameter **{proxy+}** which will automatically create a special HTTP method ANY 
* **Methods**: HTTP verbs associated with a resource

## Options
### **REST API**
  * Stateless
  * Proxy to backend
  * API management feratures (usage plans, keys, publishing, monetization)
  * Ideal for edge-optimized or private APIs

#### Endpoint Types
* **Regional**: reduce latency from within the same AWS region. No cloudfront distribution infront of the API. Allows customer to deploy their own CDN infront of the API for custom scenarios
* **Edge-Optimized**: reduce latency anywhere on the internet; automatically configure a managed CloudFront endpoint - all included in API pricing
* **Private**: Expose APIs only within a VPC of the user's control. Designed for applications that have very secure workloads that cannot be publically exposed. Private Link charges apply, but no data transfer costs
* APIs may change 
  * edge -> regional OR private
  * regional -> edge or private
  * private -> regional

#### Caching
* Cache endpoint responses to minimize calls to backends and improve response
* Additional charge, but worth consideration:
* Config options (stage level)
  * **Provisioned space** between 0.5 GB and 237 GB
  * **TTL**; defaults to 300s. Min 0 (off), max 3600
  * **Encryption** if security is a concern
  * **ONLY GET methods** when option is selected
  * **Per-method configuration** configurations at the method level will override stage level. You can use **parameters** to form cache keys to store separately depending on parameter values
* API gateway metrics: **CacheHitCount**, **CacheMissCount**
### **HTTP API**
    * Lower latency and lower cost than REST APis
    * Proxy request to any HTTP endpoint
    * Optimized for building APIS thtat proxy to Lmabda or HTTP backends, ideal for serverless
    * NO API management
### **WEBSOCKET API**
  * Allow bidirectional communications using WebSocket protocol
  * Often used in real-time applications such as such, collaboration platforms, multiplayer games, and trading platforms
  * Maintain a persistent conenction between connected clietns to facilitate real-time message communication
  * Backend:
    * Lambda
    * Kinesis
    * HTTP endpoint
    * Dynamodb
  * Manages persistence and state needed to keep the communication with the client flowing
  * No built-in caching available

  #### Routes
  * JSON requests are routed to invoke a specific user-defined backedn
  * Non-JSON requests are sent to pre-defined routes:
    * **$connect**: invoked when a connection route is successfully invoked. Upgrade request will be left as pending until the connect route is completed. Once teh connection is live, client messages can be rotued to specific backend through a route
    * **$disconnect**: invoked after the connection is closed; best-effort event. The backed may also initate a disconnectioin through the @connections API
    * **$default**: fallback if no otehr matches; 
      * Specify route keys for specific fallback routes or mock integrations
      * No routes to delegate to a backend component
      * As a route for non-JSON payloads

#### Integrations
* **Integration request**: attached for one-way communication. No response is returend over the WebSocket channel
  * Chose route
  * Chose Backend
  * Configure transformation of request data if needed
* **Integration response**: attached for two-way communication
  * Helps configure transformations on the returned message payload; similar to REST integration responses

## Access Control
* Authorize access at a granular level and control the amount of access through throttling

| Authentication | Authorization | Sigv4 | Cognito User Pools | Third Party Auth | Multiple Header Support | Additional Costs |
| ---- | ---- | ---- | ----- | ---- | ---- | ---|
| IAM | yes | yes | yes | no | no | none |
| lambda authorizer token | yes | yes | no | yes | yes | per-invoke|
| lambda authorzier request | yes | yes | no | yes | yes| per-invoke |
| Cognito | yes | yes | no | yes | no | based on monthly active users |

### IAM Authorization
* For internal services or a limited number of customers. IE applications tah use IAM to interact with other AWS services
1. Requests sent are signed using AWS Sigv4
2. IAM processes the user access key and secret to compute an HMAC SHA256 signature
3. The key inforamtion is added to the Authorization header. API gateway will take the signed request and determine wether the user who signed it has permission to invoke the API or API route
   
### Lambda Authorization
* For apps already using OAuth strategies or that want to use third-party authorization
* Configured per-API Method
* API Gateway must be able to assume a role which can invoke the authorizer lambda
* Supports an optional **policy cache**  to increase performance by reducing the number of requests that ahve to be made to the Authroizer for previously authrozed token. Customizable TTL
* **Token Authorizers**
1. API gateway passes the source token to the lambda function as JSON input
2. The lambda determines wether the token is allowed or not. If yes, it will return an **IAM policy** that allows execute-API:invoke on the particular API resources specified by the lambda.
  * If denied or lambda errrors, a 403 is returned
* **Request Authorizers**
* Useful if additional request information is needed. This will be included in the JSON input to the lambda
* Just like token authorizers, an IAM policy will be returned

### Cognito Authorization
* Use a cognito user pool to manage API access
* Great for mobile and web applications that allow for user sign up
* Once a COGNITO_USER_POOLs authroizer type is attached to a route method:
1. Logged in user will receuve an OpenID Connect (OIDC) formatted JWT (Json web token)
2. The token will be passed in the Authorization header of requests. API gateway will verify that the token suplied is valid, and reject otherwise


## Throttling and Usage plans
* Manage the volume of API calls and monetize
* Prevent a consumer from abusing or overusing APIs
* Uses **token-bucket algorithm** to measure throttling. Requests taht come into the bucket are fulfilled at the steady rate at which the bucket is filled. This allows for small bursts of requests when needed
  * Return error **429 Too many requests** when a requests reaches an empty bucket
  * API gateway sets a default limit of 10000 requests per second and up to 5000 burt requests accross all APIs within an account
* Hierarchy
  * Per-client, per-method
  * Per-client
  * Per-method
  * Account level
  
### API Keys
* Used to identiy the consumer and apply desired usage, charges, and throttling to their requests. 
* Passed through ab **x-API-key** request header
  
### Usage Plans
* Attach to API keys to limit their APi usage in some way
  * Throttling (per-second or burst)
  * Quota (day/week/month)
  * Usage (daily usage records)
* Allow for more granular throttling and burst prevention

## IAM Permissions
* **execute-api**: who can invoke an api
  * permit a caller to incoke a desired API method:
  ```json
  {
    "Version": "2012-10-17",
    "Statement": [
      {
        "Effect": "Allow",
        "Action": [
          "execute-api:Invoke"
        ],
        "Resource": ["{api_arn}:*:{account-id}/{stage}/{method}/{route}/*"]
      }
    ]
  }
  ```
  * API gateway will expect an IAM Sigv4 request for any requests that come to that API method
* **apigateway**: who can manage an api
  ```json
  {
    "Version": "2012-10-17",
    "Statement": [
      {
        "Effect": "Allow",
        "Action": [
          "apigateway:Get"
        ],
        "Resource": ["{api_arn}/*"]
      }
    ]
  }
  ```
## Resource Policies
* Help further refine access to APIs
* Resource policy applied directly to API Gateway; as opposed to IAM policies affecting roles, users, or groups
* Useful for providing acces to **another AWS account**
```json
  {
    "Version": "2012-10-17",
    "Statement": [
      {
        "Effect": "Allow",
        "Principal": {
          "AWS": ["{account_arn/some_role}"]
        },
        "Action": [
          "execute-api:Invoke"
        ],
        "Resource": ["{api_arn}:*:{account-id}/{stage}/{method}/{route}/*"]
      }
    ]
  }
  ```
* May also be used to **limit by IP address or VPC IP** using the ```aws:SourceIP``` condition or ```aws:SourceVpc```
* **Explicit denials** will immediately deny access to the resource without even being sent to the underlying authorizer or integration 
* Work **together with authentication methods** to provide granular access management for  APIS
  * **Only resoirce policy** = explicit allow required
  * **Lambda + resource policy** = explicit deny will result in denial, otherwise forward to lambda for further authentication
  * **IAM + resource policy** = if separte accounts, 2 explicit allow required. If same account, only 1 explicit allow required
  * **Cognito + resource policy**: evaluated indenpendently. If explicit allow and authentication passses, caller may proceed

## Monitoring and Troubleshooting
* Cloudwatch default metrics
  * Count: total number of requests in a period
  * Latency: time between request recieved and resposne sent (includes integration latency and other overheads)
  * IntegrationLatency: time between request relay and response
  * 4xxError: client-side errors within a period
  * 5xxErrro: server-side errors within a period
  * CacheHitCount: number of requests served from the API cache wihtin a period
  * CacheMissCount: number of requests served form teh backedn within a period - only when caching is ON
* Additional cloudwatch logs for API Gateway
  * **Execution logging**
    * Logs what is happening on the roundtrip of a request: when the request was amde, parameters, everything in between the requests, and the returned results,
    * Beware of logging sensitive data - do not enable requests/responses logging for production APIs 
    * Additional cost
  * **Access logging**: details about who is invoking an API. Includes IP addresss, method, user protocol, and user agent. Fully customizable in JSON format
* **X-Ray**: can be used to **trace** and alalyze user requets as they traverse through API Gatway APIS to underlying services.
  * Help understand how requests are performaing and track issuess accross micro-services
  * End-to-end view of a request
  * Analyze latencies
  * Configure sampling rules
* **Cloud Trail**: captures all API calls for API Gateway events, including calls form the console and code.
  * Determine teh request that was made to API gateway, IP address, who made the request, when it was made,a nd additional details
  * View event history
  * Create a trail to send events to S3 buckets

## Data mapping and Request Validation
* Take payloads in a different format from the corresponding integration request payload as required by the integration and vice versa
  * Transform incoming requests or outgoing responses
* use cases:
  * JSON -> XML
  * Error handling:
    * Change the status code
    * Modify body content
    * Add headers
  * Request Validation offload: verify required values are present and meet simple validations; IE "make" valye must be "Tesla"
    * Required parameters in the ULR, query string, and headers are present
    * The applicable payload adheres to the configured request model of the method
* Key variables:
  * $input (body, json, params, path)
  * $stageVariables
  * $util (escapeJavaScript(), parseJson(), urlEncod/Decode(), base64Encode/Decode*())
## Best Practices
  * **Use stages with lambda aliases**:
    * Enable lambda versioning and refernce them through an alias
    * USe API Gateway stages for environments
    * Point stage variables to lambda aliases
  * **Use canary deployments**: send a percentage of traffic to the cannary while leaving a bulk of the traffic on a known good version fo the API. Once everything's been verified, promote the cannary and move all trafic
  * **Use SAM to simplify deployments**: use the open-source framework for serverless applications to build your APIs as part of a deployment package
    * use SAM Templates to define your functions, permissions, and configurations (extension of CloudFormation)
    * Use **Swagger and OpenAPI** for more complex APIS. You may use Swagger2.0 or OpenApi3.0 files to import API configurations