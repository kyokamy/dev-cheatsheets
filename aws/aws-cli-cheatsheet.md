# ☁️ The Ultimate AWS CLI Cheatsheet

### *Because clicking through the console 47 times is not a deployment strategy*

---

## 🎬 Act I: Getting Started (a.k.a. "Hello, Cloud")

### Installation

```bash
# Install AWS CLI (Linux)
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install

# Install AWS CLI (macOS)
brew install awscli

# Install AWS CLI (Windows)
# Download MSI installer from aws.amazon.com/cli

# Verify installation
aws --version
aws --help
```

### Configuration

```bash
# Configure AWS CLI
aws configure
# Prompts for:
# - AWS Access Key ID
# - AWS Secret Access Key
# - Default region (e.g., us-east-1)
# - Default output format (json, text, table)

# Configure named profiles
aws configure --profile myprofile

# List profiles
aws configure list-profiles

# Use specific profile
aws s3 ls --profile myprofile
export AWS_PROFILE=myprofile

# View current configuration
aws configure list

# Set region
aws configure set region us-west-2

# Set output format
aws configure set output json
aws configure set output table
aws configure set output text

# Configure credentials file manually
# ~/.aws/credentials
[default]
aws_access_key_id = YOUR_ACCESS_KEY
aws_secret_access_key = YOUR_SECRET_KEY

[myprofile]
aws_access_key_id = ANOTHER_ACCESS_KEY
aws_secret_access_key = ANOTHER_SECRET_KEY

# Configure config file
# ~/.aws/config
[default]
region = us-east-1
output = json

[profile myprofile]
region = us-west-2
output = table
```

---

## 💻 Act II: EC2 (Elastic Compute Cloud)

### Instance Management

```bash
# List instances
aws ec2 describe-instances
aws ec2 describe-instances --query 'Reservations[*].Instances[*].[InstanceId,State.Name,InstanceType]' --output table

# List running instances only
aws ec2 describe-instances --filters "Name=instance-state-name,Values=running" --query 'Reservations[*].Instances[*].[InstanceId,InstanceType,PublicIpAddress]' --output table

# Start instance
aws ec2 start-instances --instance-ids i-1234567890abcdef0

# Stop instance
aws ec2 stop-instances --instance-ids i-1234567890abcdef0

# Reboot instance
aws ec2 reboot-instances --instance-ids i-1234567890abcdef0

# Terminate instance
aws ec2 terminate-instances --instance-ids i-1234567890abcdef0

# Create instance
aws ec2 run-instances \
    --image-id ami-0c55b159cbfafe1f0 \
    --instance-type t2.micro \
    --key-name my-key-pair \
    --security-group-ids sg-12345678 \
    --subnet-id subnet-12345678 \
    --tag-specifications 'ResourceType=instance,Tags=[{Key=Name,Value=MyInstance}]'

# Get instance details
aws ec2 describe-instances --instance-ids i-1234567890abcdef0
```

### AMIs (Amazon Machine Images)

```bash
# List AMIs
aws ec2 describe-images --owners self
aws ec2 describe-images --filters "Name=name,Values=my-ami-*"

# Create AMI from instance
aws ec2 create-image \
    --instance-id i-1234567890abcdef0 \
    --name "my-custom-ami" \
    --description "My custom AMI"

# Copy AMI to another region
aws ec2 copy-image \
    --source-region us-east-1 \
    --source-image-id ami-12345678 \
    --region us-west-2 \
    --name "my-ami-copy"
```

### Key Pairs

```bash
# List key pairs
aws ec2 describe-key-pairs

# Create key pair
aws ec2 create-key-pair --key-name my-key-pair --query 'KeyMaterial' --output text > my-key-pair.pem
chmod 400 my-key-pair.pem

# Delete key pair
aws ec2 delete-key-pair --key-name my-key-pair
```

### Security Groups

```bash
# List security groups
aws ec2 describe-security-groups

# Create security group
aws ec2 create-security-group \
    --group-name my-sg \
    --description "My security group" \
    --vpc-id vpc-12345678

# Add rule to security group
aws ec2 authorize-security-group-ingress \
    --group-id sg-12345678 \
    --protocol tcp \
    --port 22 \
    --cidr 0.0.0.0/0

# Remove rule from security group
aws ec2 revoke-security-group-ingress \
    --group-id sg-12345678 \
    --protocol tcp \
    --port 22 \
    --cidr 0.0.0.0/0
```

### Snapshots & Volumes

```bash
# List snapshots
aws ec2 describe-snapshots --owner-ids self

# Create snapshot
aws ec2 create-snapshot \
    --volume-id vol-1234567890abcdef0 \
    --description "My snapshot"

# List volumes
aws ec2 describe-volumes

# Create volume
aws ec2 create-volume \
    --availability-zone us-east-1a \
    --size 100 \
    --volume-type gp3

# Attach volume
aws ec2 attach-volume \
    --volume-id vol-1234567890abcdef0 \
    --instance-id i-1234567890abcdef0 \
    --device /dev/sdf
```

---

## 🪣 Act III: S3 (Simple Storage Service)

### Bucket Operations

```bash
# List buckets
aws s3 ls

# Create bucket
aws s3 mb s3://my-bucket-name
aws s3 mb s3://my-bucket-name --region us-west-2

# Delete bucket
aws s3 rb s3://my-bucket-name
aws s3 rb s3://my-bucket-name --force  # Delete with contents

# List bucket contents
aws s3 ls s3://my-bucket-name
aws s3 ls s3://my-bucket-name/path/ --recursive
```

### File Operations

```bash
# Copy file to S3
aws s3 cp file.txt s3://my-bucket-name/
aws s3 cp file.txt s3://my-bucket-name/path/file.txt

# Copy file from S3
aws s3 cp s3://my-bucket-name/file.txt ./
aws s3 cp s3://my-bucket-name/path/file.txt ./local-file.txt

# Copy directory to S3
aws s3 cp ./local-dir s3://my-bucket-name/remote-dir --recursive

# Copy directory from S3
aws s3 cp s3://my-bucket-name/remote-dir ./local-dir --recursive

# Sync directory (like rsync)
aws s3 sync ./local-dir s3://my-bucket-name/remote-dir
aws s3 sync s3://my-bucket-name/remote-dir ./local-dir

# Move file
aws s3 mv file.txt s3://my-bucket-name/

# Remove file
aws s3 rm s3://my-bucket-name/file.txt
aws s3 rm s3://my-bucket-name/path/ --recursive

# Preserve metadata
aws s3 cp file.txt s3://my-bucket-name/ --metadata "key=value"
aws s3 cp s3://my-bucket-name/file.txt ./ --metadata-directive REPLACE
```

### S3 Advanced Features

```bash
# Set bucket versioning
aws s3api put-bucket-versioning \
    --bucket my-bucket-name \
    --versioning-configuration Status=Enabled

# Set bucket encryption
aws s3api put-bucket-encryption \
    --bucket my-bucket-name \
    --server-side-encryption-configuration '{
        "Rules": [{
            "ApplyServerSideEncryptionByDefault": {
                "SSEAlgorithm": "AES256"
            }
        }]
    }'

# Set bucket policy
aws s3api put-bucket-policy \
    --bucket my-bucket-name \
    --policy file://policy.json

# Set CORS configuration
aws s3api put-bucket-cors \
    --bucket my-bucket-name \
    --cors-configuration file://cors.json

# Generate presigned URL
aws s3 presign s3://my-bucket-name/file.txt
aws s3 presign s3://my-bucket-name/file.txt --expires-in 3600  # 1 hour

# Set object ACL
aws s3api put-object-acl \
    --bucket my-bucket-name \
    --key file.txt \
    --acl public-read
```

---

## 👤 Act IV: IAM (Identity and Access Management)

### User Management

```bash
# List users
aws iam list-users

# Create user
aws iam create-user --user-name myuser

# Delete user
aws iam delete-user --user-name myuser

# Get user details
aws iam get-user --user-name myuser

# List user policies
aws iam list-user-policies --user-name myuser
aws iam list-attached-user-policies --user-name myuser
```

### Policy Management

```bash
# List policies
aws iam list-policies

# Create policy
aws iam create-policy \
    --policy-name my-policy \
    --policy-document file://policy.json

# Attach policy to user
aws iam attach-user-policy \
    --user-name myuser \
    --policy-arn arn:aws:iam::123456789012:policy/my-policy

# Detach policy from user
aws iam detach-user-policy \
    --user-name myuser \
    --policy-arn arn:aws:iam::123456789012:policy/my-policy

# Create inline policy
aws iam put-user-policy \
    --user-name myuser \
    --policy-name my-inline-policy \
    --policy-document file://policy.json
```

### Role Management

```bash
# List roles
aws iam list-roles

# Create role
aws iam create-role \
    --role-name my-role \
    --assume-role-policy-document file://trust-policy.json

# Attach policy to role
aws iam attach-role-policy \
    --role-name my-role \
    --policy-arn arn:aws:iam::aws:policy/AmazonS3ReadOnlyAccess

# Assume role
aws sts assume-role \
    --role-arn arn:aws:iam::123456789012:role/my-role \
    --role-session-name mysession
```

### Access Keys

```bash
# List access keys
aws iam list-access-keys --user-name myuser

# Create access key
aws iam create-access-key --user-name myuser

# Delete access key
aws iam delete-access-key \
    --user-name myuser \
    --access-key-id AKIAIOSFODNN7EXAMPLE
```

---

## 🌐 Act V: VPC (Virtual Private Cloud)

### VPC Operations

```bash
# List VPCs
aws ec2 describe-vpcs

# Create VPC
aws ec2 create-vpc \
    --cidr-block 10.0.0.0/16

# Delete VPC
aws ec2 delete-vpc --vpc-id vpc-12345678

# List subnets
aws ec2 describe-subnets

# Create subnet
aws ec2 create-subnet \
    --vpc-id vpc-12345678 \
    --cidr-block 10.0.1.0/24 \
    --availability-zone us-east-1a

# List internet gateways
aws ec2 describe-internet-gateways

# Attach internet gateway
aws ec2 attach-internet-gateway \
    --internet-gateway-id igw-12345678 \
    --vpc-id vpc-12345678

# List route tables
aws ec2 describe-route-tables

# Create route
aws ec2 create-route \
    --route-table-id rtb-12345678 \
    --destination-cidr-block 0.0.0.0/0 \
    --gateway-id igw-12345678
```

---

## ⚡ Act VI: Lambda (Serverless Functions)

### Function Management

```bash
# List functions
aws lambda list-functions

# Get function details
aws lambda get-function --function-name my-function

# Create function
aws lambda create-function \
    --function-name my-function \
    --runtime python3.11 \
    --role arn:aws:iam::123456789012:role/lambda-role \
    --handler index.handler \
    --zip-file fileb://function.zip

# Update function code
aws lambda update-function-code \
    --function-name my-function \
    --zip-file fileb://function.zip

# Update function configuration
aws lambda update-function-configuration \
    --function-name my-function \
    --timeout 30 \
    --memory-size 512

# Invoke function
aws lambda invoke \
    --function-name my-function \
    --payload '{"key":"value"}' \
    response.json

# Delete function
aws lambda delete-function --function-name my-function

# List function versions
aws lambda list-versions-by-function --function-name my-function

# Create alias
aws lambda create-alias \
    --function-name my-function \
    --name production \
    --function-version 1
```

---

## 📋 Act VII: CloudFormation (Infrastructure as Code)

### Stack Operations

```bash
# Create stack
aws cloudformation create-stack \
    --stack-name my-stack \
    --template-body file://template.yaml \
    --parameters ParameterKey=KeyPairName,ParameterValue=my-key

# Update stack
aws cloudformation update-stack \
    --stack-name my-stack \
    --template-body file://template.yaml

# Describe stack
aws cloudformation describe-stacks --stack-name my-stack

# List stacks
aws cloudformation list-stacks

# Delete stack
aws cloudformation delete-stack --stack-name my-stack

# Validate template
aws cloudformation validate-template \
    --template-body file://template.yaml

# Describe stack events
aws cloudformation describe-stack-events --stack-name my-stack

# Describe stack resources
aws cloudformation describe-stack-resources --stack-name my-stack
```

---

## 🔍 Act VIII: Other Useful Services

### CloudWatch

```bash
# List metrics
aws cloudwatch list-metrics

# Get metric statistics
aws cloudwatch get-metric-statistics \
    --namespace AWS/EC2 \
    --metric-name CPUUtilization \
    --dimensions Name=InstanceId,Value=i-1234567890abcdef0 \
    --start-time 2024-01-01T00:00:00Z \
    --end-time 2024-01-02T00:00:00Z \
    --period 3600 \
    --statistics Average

# Put metric data
aws cloudwatch put-metric-data \
    --namespace MyNamespace \
    --metric-name MyMetric \
    --value 123.45

# List alarms
aws cloudwatch describe-alarms
```

### Route 53 (DNS)

```bash
# List hosted zones
aws route53 list-hosted-zones

# List records
aws route53 list-resource-record-sets --hosted-zone-id Z1234567890ABC

# Create record
aws route53 change-resource-record-sets \
    --hosted-zone-id Z1234567890ABC \
    --change-batch file://change-batch.json
```

### RDS (Relational Database Service)

```bash
# List DB instances
aws rds describe-db-instances

# Create DB instance
aws rds create-db-instance \
    --db-instance-identifier my-db \
    --db-instance-class db.t3.micro \
    --engine mysql \
    --master-username admin \
    --master-user-password password \
    --allocated-storage 20

# Create DB snapshot
aws rds create-db-snapshot \
    --db-instance-identifier my-db \
    --db-snapshot-identifier my-snapshot
```

### ECS (Elastic Container Service)

```bash
# List clusters
aws ecs list-clusters

# List services
aws ecs list-services --cluster my-cluster

# List tasks
aws ecs list-tasks --cluster my-cluster

# Run task
aws ecs run-task \
    --cluster my-cluster \
    --task-definition my-task:1 \
    --count 1
```

### ECR (Elastic Container Registry)

```bash
# List repositories
aws ecr describe-repositories

# Create repository
aws ecr create-repository --repository-name my-repo

# Login to ECR
aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 123456789012.dkr.ecr.us-east-1.amazonaws.com

# Push image
docker tag my-image:latest 123456789012.dkr.ecr.us-east-1.amazonaws.com/my-repo:latest
docker push 123456789012.dkr.ecr.us-east-1.amazonaws.com/my-repo:latest
```

---

## 🎯 Act IX: Useful One-Liners

```bash
# Get account ID
aws sts get-caller-identity --query Account --output text

# Get current user/role
aws sts get-caller-identity

# List all EC2 instances with tags
aws ec2 describe-instances --query 'Reservations[*].Instances[*].[InstanceId,Tags[?Key==`Name`].Value|[0],State.Name]' --output table

# List all S3 buckets with creation date
aws s3api list-buckets --query 'Buckets[*].[Name,CreationDate]' --output table

# Copy S3 bucket to another bucket
aws s3 sync s3://source-bucket s3://destination-bucket

# Find large files in S3
aws s3 ls s3://my-bucket --recursive --human-readable --summarize | sort -k5 -hr | head -10

# List all IAM users with their groups
aws iam list-users --query 'Users[*].[UserName]' --output text | xargs -I {} sh -c 'echo {}: $(aws iam list-groups-for-user --user-name {} --query "Groups[*].GroupName" --output text)'

# Get instance public IP
aws ec2 describe-instances --instance-ids i-1234567890abcdef0 --query 'Reservations[0].Instances[0].PublicIpAddress' --output text

# List all security groups with their rules
aws ec2 describe-security-groups --query 'SecurityGroups[*].[GroupName,GroupId]' --output table

# Find unused security groups
aws ec2 describe-security-groups --query 'SecurityGroups[?length(Instances)==`0`].[GroupId,GroupName]' --output table

# List all Lambda functions with their runtime
aws lambda list-functions --query 'Functions[*].[FunctionName,Runtime]' --output table

# Get CloudFormation stack outputs
aws cloudformation describe-stacks --stack-name my-stack --query 'Stacks[0].Outputs' --output table

# List all RDS instances
aws rds describe-db-instances --query 'DBInstances[*].[DBInstanceIdentifier,Engine,DBInstanceStatus]' --output table

# Get ECS task definition
aws ecs describe-task-definition --task-definition my-task:1 --query 'taskDefinition.containerDefinitions' --output json

# List all ECR repositories
aws ecr describe-repositories --query 'repositories[*].[repositoryName,repositoryUri]' --output table

# Find resources by tag
aws resourcegroupstaggingapi get-resources --tag-filters Key=Environment,Values=production

# Get CloudWatch metric for EC2 CPU
aws cloudwatch get-metric-statistics --namespace AWS/EC2 --metric-name CPUUtilization --dimensions Name=InstanceId,Value=i-1234567890abcdef0 --start-time $(date -u -d '1 hour ago' +%Y-%m-%dT%H:%M:%S) --end-time $(date -u +%Y-%m-%dT%H:%M:%S) --period 300 --statistics Average
```

---

## 🔧 Act X: Query & Output Formatting

### Using --query (JMESPath)

```bash
# Basic query
aws ec2 describe-instances --query 'Reservations[*].Instances[*].InstanceId'

# Filter and format
aws ec2 describe-instances \
    --query 'Reservations[*].Instances[*].[InstanceId,InstanceType,State.Name]' \
    --output table

# Filter by condition
aws ec2 describe-instances \
    --query 'Reservations[*].Instances[?State.Name==`running`].[InstanceId,InstanceType]' \
    --output table

# Get specific value
aws ec2 describe-instances \
    --instance-ids i-1234567890abcdef0 \
    --query 'Reservations[0].Instances[0].PublicIpAddress' \
    --output text
```

### Output Formats

```bash
# JSON (default)
aws ec2 describe-instances --output json

# Table (human-readable)
aws ec2 describe-instances --output table

# Text (tab-separated)
aws ec2 describe-instances --output text

# YAML
aws ec2 describe-instances --output yaml

# YAML stream
aws ec2 describe-instances --output yaml-stream
```

---

## 🆘 Emergency Commands (When Everything is on Fire)

```bash
# Stop all running EC2 instances
aws ec2 describe-instances \
    --filters "Name=instance-state-name,Values=running" \
    --query 'Reservations[*].Instances[*].InstanceId' \
    --output text | xargs -I {} aws ec2 stop-instances --instance-ids {}

# Delete all objects in S3 bucket
aws s3 rm s3://my-bucket --recursive

# Delete all versions in versioned bucket
aws s3api delete-objects \
    --bucket my-bucket \
    --delete "$(aws s3api list-object-versions \
        --bucket my-bucket \
        --query '{Objects: Versions[].{Key:Key,VersionId:VersionId}}')"

# Terminate all instances with specific tag
aws ec2 describe-instances \
    --filters "Name=tag:Environment,Values=test" \
    --query 'Reservations[*].Instances[*].InstanceId' \
    --output text | xargs -I {} aws ec2 terminate-instances --instance-ids {}

# Delete all CloudFormation stacks (careful!)
aws cloudformation list-stacks \
    --query 'StackSummaries[?StackStatus!=`DELETE_COMPLETE`].[StackName]' \
    --output text | xargs -I {} aws cloudformation delete-stack --stack-name {}

# Get detailed error for failed stack
aws cloudformation describe-stack-events \
    --stack-name my-stack \
    --query 'StackEvents[?ResourceStatus==`CREATE_FAILED`]' \
    --output table
```

---

## 📝 Quick Reference Card

| What You Want | Command |
|---------------|---------|
| Configure | `aws configure` |
| List EC2 instances | `aws ec2 describe-instances` |
| Start instance | `aws ec2 start-instances --instance-ids i-xxx` |
| Stop instance | `aws ec2 stop-instances --instance-ids i-xxx` |
| List S3 buckets | `aws s3 ls` |
| Copy to S3 | `aws s3 cp file.txt s3://bucket/` |
| Copy from S3 | `aws s3 cp s3://bucket/file.txt ./` |
| Sync S3 | `aws s3 sync ./dir s3://bucket/dir` |
| List IAM users | `aws iam list-users` |
| List Lambda functions | `aws lambda list-functions` |
| Invoke Lambda | `aws lambda invoke --function-name name output.json` |
| List stacks | `aws cloudformation list-stacks` |
| Get account ID | `aws sts get-caller-identity` |
| Set region | `aws configure set region us-west-2` |
| Use profile | `aws s3 ls --profile myprofile` |

---

## 🎬 The End Credits

Remember:
- Always use `--dry-run` when available for destructive operations
- Use `--query` to filter output (saves time and bandwidth)
- Use `--output table` for human-readable output
- Set up profiles for multiple AWS accounts
- Use IAM roles instead of access keys when possible
- Enable MFA for production accounts
- Use `--region` flag or set default region
- Check `aws <service> help` for service-specific commands
- Use `--cli-read-timeout` and `--cli-connect-timeout` for slow connections

**Now go cloud something awesome!** ☁️

---

*Made with ☕ and the frustration of AWS console timeouts*

*Last updated: When you finally got that IAM policy right*

