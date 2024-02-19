# Shared Responsibility Model

## Customer Responsibility - Security IN the cloud
* Customer Data
* Platform and applications
* Identity and access management
* Operating systems and patches
* Networking and firewall configuration
* Encryption (client and server-side)
* Network traffic protection

## AWS Responsibility - Security OF the cloud
* Software
* Compute
* Storage and Database
* Networking
* Hardware and Infrastructure - Regions, AZ, Edge locations

# Protecting Root User
  * Initial user of the account; associated with email and password
  * Has complete access to ALL services and resources on the account
  * 2 Sets of credentials:
    * User name + password
    * Access Keys: **Access Key ID + Secret Access Key** + Access Token (optional)
  * Best practices:
    * Strong password
    * Multi-factor auth
    * Never share
    * Disable and/or delete any access keys associated with root
    * Create a user for administrative tasks
    * 
  ## MFA
  * Something you know + something you have
  * Supported:
    * Virtual MFA: app that runs on a phone or device which provides a one time code; such as Microsoft Authenticator
    * Hardware TOTP token: key or dob that generates a one-time six-digit numeric one-time password
    * FIDO security keys: FIDO-certified hardware security keys, such as Yubico, which are inserted into a USB port [https://fidoalliance.org/certification/fido-certified-products/](https://fidoalliance.org/certification/fido-certified-products/)