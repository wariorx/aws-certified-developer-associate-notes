# KMS
* AWS Encryption key management service
* Create, maange, and control cryptographic keys accross applications

## Features
* Protect data at rest
* Encrypt and decrypt data
* Sign and verify signatures
* Secure multi-tenant databases
* Cannot export keys (except assymetric public keys)
* Import own crtypto keys into a KMS key
* Supports multi-region; same keys can be re-used accross regions
* Create custom key store to use key material in AWS CloudHSM or in an external key manager
* Audit key usage in CloudTrail

## Concepts
* **KMS Key**: primary resource in KMS; a logical representaiton of a cryptographic key used to encrypt, decrypt, generate data keys to use outside KMS. Typically symmetric, but asymetric keys and HMACs are also available. Never leaves AWS KMS. Metadata:
  * key ID
  * Key Spec
  * Key Usage
  * Creation Date
  * Description
  * **Key state**
* **Data Keys**: symmetric keys derived from a KMS key that can be used for cryptographic operations outside KMS
  * When generated, return a PLAIN TEXt and encrypted copy of the data key to store safely. When ready to use, ask KMS to decrypt the encrypted key --> GeneratedataKey or GenerateDataKeyWIthoutPlainText API
  * Not stored, managed, or tracked in KMS and KMS cannot use it to encrypt data
  * To **decrypt**, pass the encrypted data key to KMS and specify the KMS key to use to decrypt. this will return the plain text key to be used
* **Unusable KMS keys**: all request to an unusable key fail. however, the effect on data keys and data encrypted by the KMS key is delayed until next use.
  * Disabled key
  * Scheduled fir deletion
  * Key material (underlying crypto key) deleted for custom keys
  * Disconnecting AWS CloudHSM key store that hosted it
  * Disconnection the external key store that hosted it
* **Aliases**: friendly name for a KMS key to make it easier to identify it. It can even be used to identify the key in some operations in place of the key id
  * Allow or Deny key access based on their alias without editing policies - using [ABAC](https://docs.aws.amazon.com/kms/latest/developerguide/abac.html)
    ```json
    {
        "Version": "2012-10-17",
        "Statement": [
            {
            "Sid": "AliasBasedIAMPolicy",
            "Effect": "Allow",
            "Action": [
                "kms:Decrypt",
                "kms:Encrypt",
                "kms:GenerateDataKey*",
                "kms:DescribeKey"
            ],
            "Resource": "arn:aws:kms:*:111122223333:key/*",
            "Condition": {
                "StringEquals": {
                "aws:ResourceTag/Purpose": "Test"
                }
            }
            }
        ]
    }
    ```

## Customer and AWS Keys
* **Customer keys** are created and maanged by a user
  * Will be marked as CUSTOMER in DescribeKey API calls
  * Can be aliased
  * Can have key policies and IAM policies attached
  * Enabled / disabled
* **AWS managed Keys** services often create and manage KMS keys in behalf of the user. user can view their properties and policies, but not modify them
* **AWS Onwned keys**: keys that services create in a service account
  * Good choice unless Auditing is required
  * Do not count against KMS quotas

| KMS Key | View Metadata | Manage | Only for account | Automatic Rotation | Pricing |
| ---- | ---- | ---- | ---- | ---- | ---- |
| customer managed | yes | yes | yes | optional - recommended yearly | monthly fee and per-use fee |
| aws managed | yes | no | yes | required every year | no monthly fee, per-use fee (some services take on this cost for the user) |
| aws owned | no | no | no | varies | no fees |

## Key Policies
* Can be attached to **customer keys** to determine who can use and managed key in json format. Format is that of a Resource based policy
* **Grants** are temporary policy instruments that allows a AWS principal to use AWS KMS keys in cryptographic operations without having to modify IAM policies or Key policies. They are taken into account alongside them at runtime.

## Encryption Context
* Non-secret key-value paris that can contain additional contextual information about the data.
* All crypttographic operations usin SYMMETRIC KMS keys accept an encryption context
* Used alongside [AAD](https://docs.aws.amazon.com/crypto/latest/userguide/cryptography-concepts.html#term-aad) (additional information passed to the encryption operation that must be matched after the decryption operation) to support authenticated encryption -> used to verify decrypted data hasn't been tampered with
* Used for Additional Authenticated Data, Audit Trail, and Authorization context
* Key and value pairs must be simple, literal strings and will be interpreted as such
* Can include unicode characters; though if characters that are not valid in IAM policies are used, you won't be able to specift the encryption context in policy condition keys like ```kms:EncryptionContext:context-key```
* Use in policies as follows to control Access (IE only allowed when request context includes teh AppName ExampleApp):
```json
{
  "Effect": "Allow",
  "Principal": {
    "AWS": "arn:aws:iam::111122223333:role/RoleForExampleApp"
  },
  "Action": "kms:Decrypt",
  "Resource": "*",
  "Condition": {
    "StringEquals": {
      "kms:EncryptionContext:AppName": "ExampleApp"
    }
  }
}
```

  * **Grant constraints** like ```EncryptionContextEquals ``` can be used in a similar manner as policy conditions
* Logged in PLAIN TEXT in CloudTrail
* Can be stored alongside encrypted data; though recommended to store only enough so that its purpose is not invalidated (build full context from partially stored context)

## Envelope Encryption
* Practice of encrypting plaintext data with a data key, then encrypt the data key under another key
* Encrypt the **data key** used to encrypt the data using your KMS key
### Use Cases
* Safely store data keys since they will be protected behind a layer of encryption
* Encrypt the same data using multiple keys -> save time instead of re-encrypting data multiple times with diffferent keys -> Useful for avoiding quotas and charges for applications that require heavy use of cryptographic operations
* Combine the strength of multiple algorithms (IE symmetric and assymetric encryption; where you would encrypt the private key when stored until needed)

## Quotas
* Adjustable via support request, except for the key policy document size resource quota
* LimitExceededException
  
### Resource Quotas
* Some apply to resources created by user; but not to those created by AWS
* **KMS Keys** 100,000
* **Aliases per KMS key** 50
* **Grants per KMS key** 50,0000
* **key policy size** 32 Kb
* **Custom store key  quota** 10

### Request quotas
* Limit number of API operatiosn per second; varies per operation and other factors such as region and key type
* When limits exceeded, KMS will **throttle** requests -> use [Exponential backoff](https://docs.aws.amazon.com/sdkref/latest/guide/feature-retry-behavior.html)
* **Generate data key** operation limit exceeded => consider cashing data keys to minimize the number of requests to KMS
* Apply to both customer and AWS managed keys
* Applied accross all regions and pricinpals in the account -> this includes API requests made in the users behalf, such as S3 decrypting an object before sending it to customer
* Shared quotas for crypto operations:
  * **Cryptographic operations (symmetric) request rate**
  * **Cryptographic operations (RSA) request rate**
  * **Cryptographic operations (ECC) request rate**
  * **Cryptographic operations (SM — China Regions only)**
  * **Custom key store request quota**
* Cross-account request are throttled based on the quota of the account making the request, not of the account containing the resource