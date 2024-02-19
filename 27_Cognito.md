# Cognito
* Customer identity and access management.
* Scale to millions of users accross devices


## Features
* Compromised credential protextion
* Logging and monitoring using CloudTrail and Cloudwatch
  * Cloudtrail captures API calls to the cognito API
  * Cloudwatch metrics allow you to monitor, report, and take actions ot events in real time
  * Cloudwatch insights to configure CloudTrail to send metrics and events to cloudwatch for further actions
* Migration options; 
  * batch import: using a csv file process
  * just-in-time migration: using lambda triggers to tie migration into the authentication lifecycle
* Machine-to-machine authentication using OAuth credentials
* Access token customization OAuth2.0 scopes and clains to make application-specific advanced authroization decisions using custom attributes

## Cognito user pools
* Secure user and identity storage 
  * Store up to 50 custom attributes per user
  * Enforced lenght and mutability constraints
* Supports user-name password authentication by default
* Option to use Fedetrated Identity ptoviders such as Facebook, Google, SAML or OpenID
* Different sign in options
  * User name
  * Email
  * Phone number
* Add requirements for username and passwords
* Supports **MFA**
  * Authenticator apps
  * SMS MFA
* Supports **Account recovery**
* Supports **Email and phone number verification**
* Supports usage of cognito domains and custom domains for **OAuth2.0** verification
* Provides a default client for faster development
  * Public client: browser or mobile app not trusted with a client secret
  * Confidential client: server-side applicatioin that can securely store a client secret
  * Other: custom app with its own grant, auth flow, and client-secret settings


## Integrations

### API Gateway
* Leverage built-in policy enforcements points to provide access based on Cognito tokens and scopes
  
### ALB
* Leverage built-in policy enforcements points to provide access based on Cognito tokens and scopes
  
### AppSync

### Lambda
* Lambda triggers can be used to customize the lifecycle of authentication stages;
  * Before sign-up
  * After sign-up
  * Before token issuance
  * Message customization and 3rd party integration
  
### Other AWS services
* Provides single sign-on to other aws resources
* Map users dunamically to different roles to support least privilege access to a service
* DynamoDb
* S3 buckets
* Lambda
* More