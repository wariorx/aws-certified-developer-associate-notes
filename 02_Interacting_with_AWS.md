# AWS Console
Web-based interface: [https://aws.amazon.com](https://aws.amazon.com)
# Programatically

## AWS CLI
```aws s3api list-buckets```
## AWS SDK
```python
import boto3
ec2 = boto3.client('ec2')
response = ec2.describe_instances()
print(response)
```