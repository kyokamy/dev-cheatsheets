# 🏗️ The Ultimate Terraform Cheatsheet

### *Because Infrastructure as Code shouldn't require a PhD in YAML*

---

## 🎬 Act I: Getting Started (a.k.a. "Hello, Infrastructure")

### Installation

```bash
# Install Terraform (Linux)
wget https://releases.hashicorp.com/terraform/1.6.0/terraform_1.6.0_linux_amd64.zip
unzip terraform_1.6.0_linux_amd64.zip
sudo mv terraform /usr/local/bin/

# Install Terraform (macOS)
brew install terraform

# Install Terraform (Windows - via Chocolatey)
choco install terraform

# Verify installation
terraform version
terraform -help
```

### Basic Commands

```bash
# Initialize Terraform (download providers)
terraform init

# Initialize with upgrade
terraform init -upgrade

# Initialize backend
terraform init -backend-config="key=value"

# Format code (make it pretty)
terraform fmt

# Format recursively
terraform fmt -recursive

# Validate configuration
terraform validate

# Plan (preview changes)
terraform plan

# Plan with output file
terraform plan -out=tfplan

# Apply changes
terraform apply

# Apply with plan file
terraform apply tfplan

# Apply with auto-approve (dangerous but convenient)
terraform apply -auto-approve

# Destroy infrastructure
terraform destroy

# Destroy specific resource
terraform destroy -target=aws_instance.example

# Destroy with auto-approve
terraform destroy -auto-approve
```

---

## 📦 Act II: Configuration Files (The Foundation)

### Basic Structure

```hcl
# main.tf - Main configuration
terraform {
  required_version = ">= 1.0"
  
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}

provider "aws" {
  region = "us-east-1"
}

resource "aws_instance" "web" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"
  
  tags = {
    Name = "WebServer"
  }
}
```

### Variables

```hcl
# variables.tf
variable "instance_type" {
  description = "EC2 instance type"
  type        = string
  default     = "t2.micro"
}

variable "region" {
  description = "AWS region"
  type        = string
  default     = "us-east-1"
}

variable "tags" {
  description = "Resource tags"
  type        = map(string)
  default     = {}
}

variable "enable_monitoring" {
  description = "Enable CloudWatch monitoring"
  type        = bool
  default     = true
}

variable "subnet_ids" {
  description = "List of subnet IDs"
  type        = list(string)
}

variable "security_groups" {
  description = "Map of security groups"
  type = map(object({
    name        = string
    description = string
    rules       = list(object({
      type        = string
      from_port   = number
      to_port     = number
      protocol    = string
      cidr_blocks = list(string)
    }))
  }))
}
```

### Using Variables

```hcl
# In your resources
resource "aws_instance" "web" {
  instance_type = var.instance_type
  # ...
  tags = var.tags
}

# Variable files
# terraform.tfvars
instance_type = "t3.small"
region       = "us-west-2"
tags = {
  Environment = "production"
  Project     = "myapp"
}

# terraform.tfvars.json (JSON format)
{
  "instance_type": "t3.small",
  "region": "us-west-2"
}

# Pass variables via command line
terraform apply -var="instance_type=t3.large"
terraform apply -var-file="production.tfvars"
```

### Outputs

```hcl
# outputs.tf
output "instance_id" {
  description = "ID of the EC2 instance"
  value       = aws_instance.web.id
}

output "instance_public_ip" {
  description = "Public IP address"
  value       = aws_instance.web.public_ip
  sensitive   = false
}

output "instance_private_ip" {
  description = "Private IP address"
  value       = aws_instance.web.private_ip
  sensitive   = true
}

output "instance_arn" {
  description = "ARN of the instance"
  value       = aws_instance.web.arn
}

# Complex outputs
output "all_instance_info" {
  value = {
    id         = aws_instance.web.id
    public_ip  = aws_instance.web.public_ip
    private_ip = aws_instance.web.private_ip
  }
}
```

```bash
# View outputs
terraform output
terraform output instance_id
terraform output -json              # JSON format
```

---

## 🏗️ Act III: Resources (The Building Blocks)

### Resource Syntax

```hcl
resource "provider_resource_type" "resource_name" {
  # Resource configuration
  attribute = value
  
  # Nested blocks
  block {
    nested_attribute = value
  }
  
  # Multiple blocks
  tags = {
    Name = "Example"
  }
  
  # Dynamic blocks
  dynamic "block_name" {
    for_each = var.items
    content {
      key = block_name.value
    }
  }
}
```

### Common Resource Patterns

```hcl
# EC2 Instance
resource "aws_instance" "web" {
  ami           = data.aws_ami.ubuntu.id
  instance_type = var.instance_type
  
  vpc_security_group_ids = [aws_security_group.web.id]
  subnet_id              = aws_subnet.public.id
  
  user_data = <<-EOF
    #!/bin/bash
    apt-get update
    apt-get install -y nginx
    systemctl start nginx
  EOF
  
  tags = {
    Name = "WebServer"
  }
}

# Security Group
resource "aws_security_group" "web" {
  name        = "web-sg"
  description = "Security group for web servers"
  vpc_id      = aws_vpc.main.id
  
  ingress {
    description = "HTTP"
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
  
  ingress {
    description = "HTTPS"
    from_port   = 443
    to_port     = 443
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
  
  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
  
  tags = {
    Name = "web-sg"
  }
}

# S3 Bucket
resource "aws_s3_bucket" "data" {
  bucket = "my-unique-bucket-name-${random_id.bucket_suffix.hex}"
  
  tags = {
    Name = "Data Bucket"
  }
}

resource "aws_s3_bucket_versioning" "data" {
  bucket = aws_s3_bucket.data.id
  
  versioning_configuration {
    status = "Enabled"
  }
}
```

---

## 🔍 Act IV: Data Sources (Query Existing Resources)

### Data Source Syntax

```hcl
# Get AMI
data "aws_ami" "ubuntu" {
  most_recent = true
  owners      = ["099720109477"] # Canonical
  
  filter {
    name   = "name"
    values = ["ubuntu/images/hvm-ssd/ubuntu-jammy-22.04-amd64-server-*"]
  }
  
  filter {
    name   = "virtualization-type"
    values = ["hvm"]
  }
}

# Get VPC
data "aws_vpc" "default" {
  default = true
}

# Get availability zones
data "aws_availability_zones" "available" {
  state = "available"
}

# Get caller identity
data "aws_caller_identity" "current" {}

# Get region
data "aws_region" "current" {}

# Use data sources
resource "aws_instance" "web" {
  ami           = data.aws_ami.ubuntu.id
  instance_type = "t2.micro"
  # ...
}
```

---

## 🔄 Act V: State Management (The Memory)

### State Commands

```bash
# Show state
terraform state list
terraform state show aws_instance.web
terraform state pull > state.json

# Move resource (rename in state)
terraform state mv aws_instance.old aws_instance.new

# Remove from state (don't destroy)
terraform state rm aws_instance.web

# Import existing resource
terraform import aws_instance.web i-1234567890abcdef0

# Refresh state
terraform refresh

# List resources
terraform state list
```

### Backend Configuration

```hcl
# Backend in main.tf
terraform {
  backend "s3" {
    bucket         = "my-terraform-state"
    key            = "path/to/terraform.tfstate"
    region         = "us-east-1"
    encrypt        = true
    dynamodb_table = "terraform-locks"
  }
}

# Backend with variables (use backend config file)
# backend.hcl
bucket         = "my-terraform-state"
key            = "prod/terraform.tfstate"
region         = "us-east-1"
encrypt        = true
dynamodb_table = "terraform-locks"

# Initialize with backend config
terraform init -backend-config=backend.hcl
```

---

## 🎯 Act VI: Workspaces (Environment Management)

### Workspace Commands

```bash
# List workspaces
terraform workspace list

# Show current workspace
terraform workspace show

# Create workspace
terraform workspace new production
terraform workspace new staging
terraform workspace new dev

# Select workspace
terraform workspace select production

# Delete workspace
terraform workspace delete dev
```

### Using Workspaces

```hcl
# In your configuration
resource "aws_instance" "web" {
  instance_type = terraform.workspace == "production" ? "t3.large" : "t2.micro"
  
  tags = {
    Environment = terraform.workspace
  }
}

# Workspace-specific variables
locals {
  instance_type = terraform.workspace == "production" ? "t3.large" : "t2.micro"
  instance_count = terraform.workspace == "production" ? 3 : 1
}
```

---

## 🧩 Act VII: Modules (Reusability)

### Using Modules

```hcl
# Call a module
module "vpc" {
  source = "./modules/vpc"
  
  vpc_cidr = "10.0.0.0/16"
  name     = "my-vpc"
  
  tags = {
    Environment = "production"
  }
}

# Use module output
resource "aws_instance" "web" {
  subnet_id = module.vpc.public_subnet_id
  # ...
}

# Remote module
module "vpc" {
  source = "terraform-aws-modules/vpc/aws"
  version = "~> 5.0"
  
  name = "my-vpc"
  cidr = "10.0.0.0/16"
  
  azs             = ["us-east-1a", "us-east-1b"]
  private_subnets = ["10.0.1.0/24", "10.0.2.0/24"]
  public_subnets  = ["10.0.101.0/24", "10.0.102.0/24"]
}

# Git module
module "vpc" {
  source = "git::https://github.com/terraform-aws-modules/terraform-aws-vpc.git?ref=v5.0.0"
  # ...
}
```

### Creating Modules

```hcl
# modules/ec2/main.tf
variable "instance_type" {
  type = string
}

variable "ami_id" {
  type = string
}

variable "subnet_id" {
  type = string
}

variable "security_group_ids" {
  type = list(string)
}

resource "aws_instance" "this" {
  ami                    = var.ami_id
  instance_type          = var.instance_type
  subnet_id              = var.subnet_id
  vpc_security_group_ids = var.security_group_ids
}

output "instance_id" {
  value = aws_instance.this.id
}

output "private_ip" {
  value = aws_instance.this.private_ip
}
```

---

## 🎨 Act VIII: Functions & Expressions

### Common Functions

```hcl
# String functions
locals {
  name = "my-resource"
  upper_name = upper(local.name)           # "MY-RESOURCE"
  lower_name = lower(local.name)           # "my-resource"
  title_name = title(local.name)           # "My Resource"
}

# Numeric functions
locals {
  max_value = max(1, 2, 3)                 # 3
  min_value = min(1, 2, 3)                # 1
  abs_value = abs(-5)                     # 5
}

# Collection functions
locals {
  list = ["a", "b", "c"]
  first = element(local.list, 0)           # "a"
  length = length(local.list)              # 3
  contains = contains(local.list, "b")     # true
  join = join("-", local.list)             # "a-b-c"
  split = split("-", "a-b-c")              # ["a", "b", "c"]
}

# Map/Object functions
locals {
  map = {
    a = 1
    b = 2
    c = 3
  }
  keys = keys(local.map)                   # ["a", "b", "c"]
  values = values(local.map)                # [1, 2, 3]
  lookup = lookup(local.map, "a", 0)       # 1
}

# Type conversion
locals {
  string_to_number = tonumber("42")        # 42
  number_to_string = tostring(42)          # "42"
  bool_to_string = tostring(true)          # "true"
}

# Conditional expressions
locals {
  instance_type = var.environment == "prod" ? "t3.large" : "t2.micro"
  count = var.enable_feature ? 1 : 0
}

# For expressions
locals {
  doubled = [for i in [1, 2, 3] : i * 2]  # [2, 4, 6]
  mapped = {for k, v in var.tags : k => upper(v)}
}

# File functions
locals {
  user_data = file("${path.module}/scripts/init.sh")
  template = templatefile("${path.module}/templates/config.tpl", {
    name = "example"
  })
}
```

---

## 🔧 Act IX: Advanced Features

### Locals

```hcl
# locals.tf
locals {
  common_tags = {
    Project     = "MyProject"
    Environment = var.environment
    ManagedBy   = "Terraform"
  }
  
  instance_count = var.environment == "production" ? 3 : 1
  
  # Computed values
  vpc_cidr = "10.${var.vpc_number}.0.0/16"
}

# Use locals
resource "aws_instance" "web" {
  count = local.instance_count
  tags  = local.common_tags
}
```

### Count & For Each

```hcl
# Using count
resource "aws_instance" "web" {
  count = 3
  
  ami           = data.aws_ami.ubuntu.id
  instance_type = "t2.micro"
  
  tags = {
    Name = "WebServer-${count.index + 1}"
  }
}

# Using for_each
resource "aws_instance" "web" {
  for_each = {
    web1 = "us-east-1a"
    web2 = "us-east-1b"
    web3 = "us-east-1c"
  }
  
  ami           = data.aws_ami.ubuntu.id
  instance_type = "t2.micro"
  availability_zone = each.value
  
  tags = {
    Name = each.key
  }
}

# Reference resources created with count/for_each
aws_instance.web[0].id              # count
aws_instance.web["web1"].id         # for_each
```

### Lifecycle Rules

```hcl
resource "aws_instance" "web" {
  # ...
  
  lifecycle {
    create_before_destroy = true
    prevent_destroy       = false
    ignore_changes = [
      tags,
      user_data
    ]
    replace_triggered_by = [
      aws_security_group.web.id
    ]
  }
}
```

### Provisioners

```hcl
resource "aws_instance" "web" {
  # ...
  
  # Local-exec provisioner
  provisioner "local-exec" {
    command = "echo ${self.private_ip} >> hosts.txt"
  }
  
  # Remote-exec provisioner
  provisioner "remote-exec" {
    inline = [
      "sudo apt-get update",
      "sudo apt-get install -y nginx"
    ]
    
    connection {
      type        = "ssh"
      user        = "ubuntu"
      private_key = file("~/.ssh/id_rsa")
      host        = self.public_ip
    }
  }
  
  # File provisioner
  provisioner "file" {
    source      = "config.conf"
    destination = "/etc/config.conf"
    
    connection {
      type        = "ssh"
      user        = "ubuntu"
      private_key = file("~/.ssh/id_rsa")
      host        = self.public_ip
    }
  }
}
```

---

## 🎭 Act X: Useful One-Liners

```bash
# Format all files
terraform fmt -recursive

# Validate all
terraform validate

# Plan with detailed output
terraform plan -detailed-exitcode

# Show only changes
terraform plan -compact-warnings

# Graph visualization
terraform graph | dot -Tsvg > graph.svg

# Get resource address
terraform state list | grep instance

# Show resource in state
terraform state show 'aws_instance.web[0]'

# Taint resource (force recreation)
terraform taint aws_instance.web
terraform untaint aws_instance.web

# Force unlock (if state is locked)
terraform force-unlock <lock-id>

# Console (interactive)
terraform console

# Version constraints check
terraform version -json
```

---

## 🆘 Emergency Commands (When Everything is on Fire)

```bash
# Force unlock state
terraform force-unlock <lock-id>

# Remove from state (resource already deleted)
terraform state rm aws_instance.web

# Import missing resource
terraform import aws_instance.web i-1234567890abcdef0

# Refresh state (sync with reality)
terraform refresh

# Plan destroy (see what will be destroyed)
terraform plan -destroy

# Show current state
terraform show
terraform show -json

# Backup state
terraform state pull > backup.tfstate

# Restore state
terraform state push backup.tfstate
```

---

## 📝 Quick Reference Card

| What You Want | Command |
|---------------|---------|
| Initialize | `terraform init` |
| Plan | `terraform plan` |
| Apply | `terraform apply` |
| Destroy | `terraform destroy` |
| Format | `terraform fmt` |
| Validate | `terraform validate` |
| Show state | `terraform state list` |
| Show resource | `terraform state show <resource>` |
| Import | `terraform import <resource> <id>` |
| Workspace list | `terraform workspace list` |
| Workspace select | `terraform workspace select <name>` |
| Output | `terraform output` |
| Refresh | `terraform refresh` |

---

## 🎬 The End Credits

Remember:
- Always run `terraform plan` before `apply`
- Use version constraints for providers
- Never commit `.tfstate` files (use remote backend)
- Use workspaces for environments
- Modules are your friend (DRY principle)
- Variables make your code reusable
- `terraform fmt` before committing
- State locking prevents conflicts
- Import existing resources carefully
- Destroy with caution (especially in production!)

**Now go build some infrastructure!** 🏗️

---

*Made with ☕ and the frustration of managing infrastructure manually*

*Last updated: When you finally got that terraform apply to work*

