# Serverless Application Model
* Set of command lines and tools designed to simply and minimize the amount of code and time required to manage cloud infrastructure
  * Extends CloudFormation
  * Manages the conmplex work of transofrming a template and provisioning infrastrcture
  * Perform local debugging using the CLI 
* Compatible with third party tools such as Terraform
```yaml
AWSTemplateFormatVersion: 2010-09-09
Transform: AWS::Serverless-2016-10-31
Resources:
  getAllItemsFunction:
    Type: AWS::Serverless::Function
    Properties:
      Handler: src/get-all-items.getAllItemsHandler
      Runtime: nodejs12.x
      Events:
        Api:
          Type: HttpApi
          Properties:
            Path: /
            Method: GET
    Connectors:
      MyConn:
        Properties:
          Destination:
            Id: SampleTable
          Permissions:
            - Read
  SampleTable:
    Type: AWS::Serverless::SimpleTable
```
## SAM CLI
* Sync local changes to the cloud
* Build your application for deployment
* Perform local debugging `sam local invoke {Lambda}`
  * Simulate events, test APIs, invoke functions, etc.
* Deploy applications
* Configure CI/CD
* Monitor and troubleshoot