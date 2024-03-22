# Cloud Formation

## Failure options
* **Create** operations set to **Preserve successfully provisioned resource**s preserves the state of successful resources, while failed resources will stay in a failed state until the next update operation is performed.

* **Retry**: rery tempalte operations on faield resources until successful completion or operation failure - good when the template failed not due to template errors but because of race conditions or misconfigurations
* **Update**: update resources provisioned; create or retry updates on failed resources. If the stack is in a a FAILED state - you must select **Preserve successfully provisioned resources** for stack options to continue updating
* **Roll back**: rollback changes to the last known stable state


## Moving resources between stack
* First set the **Retain** deletion policy to the resource you want to move to ensure thath te resource is preserved upon being moved

## Exporting resources

## Components of a Template
```yaml
AWSTemplateFormatVersion: "version date"
# CloudFormation template version that the template conforms to
Description:
  #A text string that describes the template. This section must always follow the template format version section.
Metadata:
  #Objects that provide additional information about the template.
Parameters:
  #Values to pass to your template at runtime (when you create or update a stack). You can refer to parameters from the Resources and Outputs sections of the template.
Rules:
  #Validates a parameter or a combination of parameters passed to a template during a stack creation or stack update.
  Rule01:
    RuleCondition:
      rule-specific intrinsic function: Value01
    Assertions:
      - Assert:
          'Fn::Contains':
          - - a1.medium
          - !Ref InstanceType
        AssertDescription: Information about this assert
      - Assert: !Equals 
        - !Ref Environment
        - prod
        AssertDescription: Information about this assert
  Rule02:
    Assertions:
      - Assert:
          rule-specific intrinsic function: Value04
        AssertDescription: Information about this assert
Mappings:
  #A mapping of keys and associated values that you can use to specify conditional parameter values, similar to a lookup table. You can match a key to a corresponding value by using the Fn::FindInMap intrinsic function in the Resources and Outputs sections. 
  RegionMap: 
    us-east-1: 
      "HVM64": "ami-0ff8a91507f77f867"
    us-west-1: 
      "HVM64": "ami-0bdb828fd58c52235"
    eu-west-1: 
      "HVM64": "ami-047bb4163c506cd98"
    ap-southeast-1: 
      "HVM64": "ami-08569b978cc4dfa10"
    ap-northeast-1: 
      "HVM64": "ami-06cd52961ce9f0d85"
Conditions:
  #Conditions that control whether certain resources are created or whether certain resource properties are assigned a value during stack creation or update. For example, you could conditionally create a resource that depends on whether the stack is for a production or test environment.
  CreateProdResources: !Equals 
    - !Ref EnvType
    - prod
  Resources:
    MountPoint:
      Type: 'AWS::EC2::VolumeAttachment'
      Condition: CreateProdResources
      Properties:
        InstanceId: !Ref EC2Instance
        VolumeId: !Ref NewVolume
        Device: /dev/sdh
Transform:
  #For serverless applications (also referred to as Lambda-based applications), specifies the version of the AWS Serverless Application Model (AWS SAM) to use. When you specify a transform, you can use AWS SAM syntax to declare resources in your template. The model defines the syntax that you can use and how it's processed.

  #You can also use AWS::Include transforms to work with template snippets that are stored separately from the main AWS CloudFormation template. You can store your snippet files in an Amazon S3 bucket and then reuse the functions across multiple templates.
Resources:
  #Specifies the stack resources and their properties, such as an Amazon Elastic Compute Cloud instance or an Amazon Simple Storage Service bucket. You can refer to resources in the Resources and Outputs sections of the template.
Outputs:
  # Describes the values that are returned whenever you view your stack's properties. For example, you can declare an output for an S3 bucket name and then call the aws cloudformation describe-stacks AWS CLI command to view the name.
  PublicSubnet:
    Description: The subnet ID to use for public web servers
    Value:
      Ref: PublicSubnet
    Export:
      Name:
        'Fn::Sub': '${AWS::StackName}-SubnetID'
  WebServerSecurityGroup:
    Description: The security group ID to use for public web servers
    Value:
      'Fn::GetAtt':
        - WebServerSecurityGroup
        - GroupId
    Export:
      Name:
        'Fn::Sub': '${AWS::StackName}-SecurityGroupID'
```
## Essential functions
**Ref**: This function is used to reference other resources within the template. It returns the value of the specified resource.

**Fn::GetAtt**: This function retrieves the value of an attribute from a resource in the template. It allows you to access specific attributes of resources.

**Fn::Join**: This function concatenates a list of values into a single string using a delimiter.

**Fn::Sub**: This function substitutes variables in a string with their actual values. It's useful for dynamic string generation.

**Fn::FindInMap [ MapName, TopLevelKey, SecondLevelKey ]**: This function retrieves a value from a mapping declared in the Mappings section of the CloudFormation template.
```yaml
Resources: 
  myEC2Instance: 
    Type: "AWS::EC2::Instance"
    Properties: 
      ImageId: !FindInMap [RegionMap, !Ref "AWS::Region", HVM64]
      InstanceType: m1.small
```

**Fn::Base64**: This function encodes a string to Base64. It's often used for UserData in EC2 instances.

**Fn::Select**: This function selects a single object from a list of objects using its index.

**Fn::Split**: This function splits a string into a list of substrings based on a delimiter.

**Fn::ImportValue**: This function imports the value of an exported output from another CloudFormation stack.
```yaml
Fn::ImportValue: 
  'Fn::Sub': '${NetworkStackNameParameter}-SecurityGroupID'
```

**Fn::If**: This function conditionally returns one value based on whether a specified condition evaluates to true or false.

**Fn::Not**: This function negates a condition.

**Fn::Equals**: This function checks if two values are equal.

**Fn::And and Fn::Or**: These functions perform logical AND and OR operations on conditions, respectively.

**Fn::Transform**: This function allows you to perform a macro transformation on a template.

## Example Templates

```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: Sample CloudFormation template with various functions

Resources:
  MyBucket:
    Type: 'AWS::S3::Bucket'
    Properties:
      BucketName: !Sub 'my-bucket-${AWS::Region}-${AWS::AccountId}'
      AccessControl: PublicRead

  MyBucketPolicy:
    Type: 'AWS::S3::BucketPolicy'
    Properties:
      Bucket: !Ref MyBucket
      PolicyDocument:
        Version: '2012-10-17'
        Statement:
          - Effect: Allow
            Principal: '*'
            Action: 's3:GetObject'
            Resource: !Sub 'arn:aws:s3:::${MyBucket}/*'

  MyQueue:
    Type: 'AWS::SQS::Queue'
    Properties:
      QueueName: MyQueue

  MyTopic:
    Type: 'AWS::SNS::Topic'
    Properties:
      TopicName: MyTopic

  MyFunction:
    Type: 'AWS::Lambda::Function'
    Properties:
      FunctionName: MyFunction
      Handler: index.handler
      Role: !GetAtt MyLambdaExecutionRole.Arn
      Code:
        S3Bucket: !Ref MyBucket
        S3Key: lambda.zip
      Runtime: nodejs12.x

  MyLambdaExecutionRole:
    Type: 'AWS::IAM::Role'
    Properties:
      AssumeRolePolicyDocument:
        Version: '2012-10-17'
        Statement:
          - Effect: Allow
            Principal:
              Service: lambda.amazonaws.com
            Action: 'sts:AssumeRole'
      Policies:
        - PolicyName: LambdaExecutionPolicy
          PolicyDocument:
            Version: '2012-10-17'
            Statement:
              - Effect: Allow
                Action: 's3:GetObject'
                Resource: !GetAtt MyBucket.Arn
              - Effect: Allow
                Action: 'logs:CreateLogGroup'
                Resource: '*'
              - Effect: Allow
                Action:
                  - 'logs:CreateLogStream'
                  - 'logs:PutLogEvents'
                Resource: !Sub 'arn:aws:logs:*:*:*'
  
  MyParameter:
    Type: 'AWS::SSM::Parameter'
    Properties:
      Name: '/my-app/param1'
      Type: String
      Value: 'parameter-value'
      Description: 'My parameter'

Outputs:
  MyBucketName:
    Value: !Ref MyBucket
    Description: Name of the created S3 bucket

  MyQueueURL:
    Value: !GetAtt MyQueue.QueueUrl
    Description: URL of the created SQS queue

  MyTopicARN:
    Value: !Ref MyTopic
    Description: ARN of the created SNS topic

  MyFunctionARN:
    Value: !GetAtt MyFunction.Arn
    Description: ARN of the created Lambda function

```