# Cloud Formation


## Essential functions
**Ref**: This function is used to reference other resources within the template. It returns the value of the specified resource.

**Fn::GetAtt**: This function retrieves the value of an attribute from a resource in the template. It allows you to access specific attributes of resources.

**Fn::Join**: This function concatenates a list of values into a single string using a delimiter.

**Fn::Sub**: This function substitutes variables in a string with their actual values. It's useful for dynamic string generation.

**Fn::FindInMap**: This function retrieves a value from a mapping declared in the Mappings section of the CloudFormation template.

**Fn::Base64**: This function encodes a string to Base64. It's often used for UserData in EC2 instances.

**Fn::Select**: This function selects a single object from a list of objects using its index.

**Fn::Split**: This function splits a string into a list of substrings based on a delimiter.

**Fn::ImportValue**: This function imports the value of an exported output from another CloudFormation stack.

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