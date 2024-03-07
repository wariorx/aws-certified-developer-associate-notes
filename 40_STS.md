# Security Token Service
* Web service that enables you to request temporary, limited-privilege crdentials for users, roles, services, etc.
* Global service; single http. Default is us-east-1 abd nade accessible through regional endpoints
* Default session expiration time of 15minutes, extandable (per-role basis) up to 12h by root user. Note that if a role is assumed by temporary credentials, the hard limit becomes 1h.

## Actions
* **AssumeRole**: Returns a set of temporary credentails taht can be used to access AWS resources; key ID, secret access key, and security token `aws sts assume-role ${rolename}`
* **AssumeRoleWithSAML**: Assume a role using SAML credentials
* **AssumeRoleWithWebIdentity** ASsume a role using OAuth2 credentials
* **GetFederationToken**: returns a set of temporary credentials for a user; usually used in a proxy applciation that gets temporary security credentials on behalf of a distributed applications.
  * Must be called using long-term security credentials of an IAM user
* **GetSessionToken**: used to obtain temporary credentials for use on MFA calls for restricted operations