# Secrets Manager
* Service for managing, retrieving, and rotating credentials, tokens, api keys and other secrets.
* **Secret** consists of a secret value plus metadata about the secret. The value may be a string or binary.
    ```json
    {
    "host"       : "ProdServer-01.databases.example.com",
    "port"       : "8888",
    "username"   : "administrator",
    "password"   : "EXAMPLE-PASSWORD",
    "dbname"     : "MyDatabase",
    "engine"     : "mysql"
    }  
    ```
  * Metadata: ARN, name ,description, resource policy, encryption key arn, and rotation strategy if any.
  * Secrets are encrypted with KMS
  * **Secret version**: holds copeis of the encrypted secret value. Upon changes to the secret, secrets manager creates a new version.
    * AWSCURRENT: there's always 1 version with this label for any secret
    * AWSPREVIOUS
    * AWSPENDING (during rotation)
    * Custom labels

## Secret Rotation
* Process of periodically updating a secret to make it more difficult for an attacker to access the credentials.
* Can be automatically triggered periodically - updating both the secret and the recipient, if any (IE datbase user password)

### Single User Rotation
* Updates credentials for a single user in one secret
* Admin credentials must be provided in a separate secret, if this secret does not have admin permissions to change its user's password
* Simplest rotation strategy, appropriate for most cases.
* There is a short period of time in between changing the password in a database and updating the secret - mitigate using a **retry strategy**
  
### Alternating User Rotation
* Updates credentials for 2 users in one secret
* Create a first user, then during rotation clone the user into a second user. The rotation will alternate between the users each time it rotates
* Appropriate with databases with permissions models where one role owns teh database tables and a second role has permissions to access the database tables

### Managed rotation
