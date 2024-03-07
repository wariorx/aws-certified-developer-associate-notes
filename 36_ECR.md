# Elastic Container Registry
* Managed container image resitry servuce
* Secure, scalable, reliable
* Supports public and private repositories via IAM permissions
  
## Components
* **Registry**: provided for each acoount; it hosts the repositories
* **Authorization token**: token used to determine actions allowed on a private repository
* **Repository**: stores docker images, OCI containers, and artifacts
* **Repository policy** controls access to repositories and its contents. 
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "ECRRepositoryPolicy",
            "Effect": "Allow",
            "Principal": {"AWS": "arn:aws:iam::account-id:user/username"},
            "Action": [
                "ecr:DescribeImages",
                "ecr:DescribeRepositories"
            ]
        }
    ]
}
```
* **Image**: standalone executable files that can be used to create containers; these can be pulled and pushed to ECR
  
## Private Resitry Authentication
`aws ecr get-login-password --region region | docker login --username AWS --password-stdin aws_account_id.dkr.ecr.region.amazonaws.com`

## Lifecycle Policies
* Provide rules that automate image management and expiration based on an age or count.
* Rules are applied in order of priority, lowest to highest
* As of right now, only expire actions are allowed
```json
{
    "rules": [
        {
            "rulePriority": 1,
            "description": "string",
            "selection": {
                "tagStatus": "tagged"|"untagged"|"any",
                "tagPatternList": [],
                "tagPrefixList": [],
                "countType": "imageCountMoreThan"|"sinceImagePushed",
                "countUnit": "string",
                "countNumber": 1
            },
            "action": {
                "type": "expire"
            }
        }
    ]
} 
```
## Scanning 
* Help identify software vulnerabilities on images
* * **Basic**: Scans for CVEs on push or manually at any time
* **Enhanced**: Use Amazon Inspector to automated continuous scanning of repositories; scan both  SO and package vulnerabilities and updat results as new vulnerabilities are reported.
* **Filtering** can be used to determine which repositories in the registry undergo the scans
  * Basic scan repos not matching the filtering rules will be set to manual scan only
  * Enhanced scan repos not matching the filtering rules will have scaning disabled
