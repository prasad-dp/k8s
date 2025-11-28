# Terraform - Infrastructure as Code

## Table of Contents
1. [What is Terraform?](#what-is-terraform)
2. [Why Do We Need Terraform?](#why-do-we-need-terraform)
3. [Providers - How Terraform Connects to Clouds](#providers)
4. [Resources - The Building Blocks](#resources)
5. [Variables - Making Code Flexible](#variables)
6. [Outputs - Getting Information Back](#outputs)
7. [State File - How Terraform Remembers](#state-file)
8. [Data Sources - Using Existing Resources](#data-sources)
9. [Modules - Reusing Code](#modules)
10. [Lifecycle Rules - Controlling Behavior](#lifecycle-rules)
11. [Plan and Apply - The Safe Process](#plan-and-apply)

---

## What is Terraform?

Terraform is a tool that lets you write code to create and manage cloud infrastructure. Instead of clicking buttons in AWS, Azure, or Google Cloud, you write simple text files describing what resources you want.

**Simple Example:**
Instead of manually creating a web server through AWS console (clicking many buttons), write:

```hcl
resource "aws_instance" "my_server" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"
}
```

Then run: `terraform apply` → Done!

---

## Why Do We Need Terraform?

### 1. **Speed & Automation**
- Create 100 servers in seconds instead of hours
- No more repetitive clicking in consoles

### 2. **Consistency**
- Everyone creates infrastructure the same way
- Dev, Staging, and Production all identical

### 3. **Version Control**
- Store infrastructure code in Git
- Track who changed what and when
- Easy code review before applying changes

### 4. **Easy Disaster Recovery**
- Recreate entire infrastructure by running code again
- No lost resources or forgotten steps

### 5. **Cost Control**
- See everything you created
- Easy to identify and delete unused resources

### 6. **Multi-Cloud Support**
- Same code works with AWS, Azure, Google Cloud
- No need to learn different tools

### 7. **Documentation**
- Code IS the documentation
- No need for separate "How to setup" guides

---

## Providers - How Terraform Connects to Clouds

A **provider** is a plugin that lets Terraform talk to cloud services (AWS, Azure, Google Cloud, etc).

**Simple Example:**

```hcl
provider "aws" {
  region = "us-east-1"
}
```

This tells Terraform: "I want to use AWS in the US-East-1 region"

**Using Multiple Clouds:**

```hcl
provider "aws" {
  region = "us-east-1"
}

provider "azurerm" {
  features {}
}
```

Now you can create resources in both AWS and Azure!

---

## Resources - The Building Blocks

A **resource** is something you want to create (server, database, storage, etc).

**Simple Example - Create a Web Server:**

```hcl
resource "aws_instance" "my_web_server" {
  ami           = "ami-0c55b159cbfafe1f0"   # Linux OS
  instance_type = "t2.micro"                 # Small size
}
```

**Simple Example - Create Storage:**

```hcl
resource "aws_s3_bucket" "my_storage" {
  bucket = "my-unique-bucket-name"
}
```

**Simple Example - Create Database:**

```hcl
resource "aws_db_instance" "my_database" {
  identifier     = "mydb"
  engine         = "mysql"
  allocated_storage = 20  # 20 GB
}
```

---

## Variables - Making Code Flexible

Variables let you change settings without modifying code. Perfect for reusing configurations.

**Simple Example - Server Size:**

```hcl
# Define variable
variable "server_size" {
  description = "Size of the server"
  default     = "t2.micro"
}

# Use it
resource "aws_instance" "web_server" {
  instance_type = var.server_size
}
```

**Change it without modifying code:**

```bash
terraform apply -var="server_size=t2.large"
```

**Real Example - Create Multiple Servers:**

```hcl
variable "instance_count" {
  description = "How many servers to create"
  default     = 1
}

resource "aws_instance" "servers" {
  count         = var.instance_count
  instance_type = "t2.micro"
}
```

Now create 1, 2, or 10 servers just by changing the variable!

---

## Outputs - Getting Information Back

Outputs show you important information after Terraform creates resources.

**Simple Example - Get Server IP:**

```hcl
resource "aws_instance" "web_server" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"
}

output "server_ip" {
  description = "The public IP of the server"
  value       = aws_instance.web_server.public_ip
}
```

**After running terraform apply, you see:**

```
Outputs:
server_ip = "54.123.45.67"
```

Now you can SSH into your server!

---

## State File - How Terraform Remembers

Terraform keeps a record (state file) of everything it created. It's like Terraform's memory.

**How it works:**

**First time:**
```bash
terraform apply
# Creates: EC2 server
# Stores: "server123 exists with ID i-12345"
```

**Second time:**
```bash
terraform apply
# Checks state: "Server i-12345 already exists"
# Does nothing (smart!)
```

**Why it's important:**
- Prevents creating duplicate resources
- Tracks what you created
- Knows what to delete when you run `terraform destroy`

**Where is state stored:**
- Local: `terraform.tfstate` file on your computer
- Remote: S3 bucket, Terraform Cloud (better for teams)

---

## Data Sources - Using Existing Resources

Data sources let you read information about resources that already exist (not created by Terraform).

**Simple Example - Use Existing VPC:**

```hcl
# Your company already has a VPC
data "aws_vpc" "existing_vpc" {
  id = "vpc-12345678"
}

# Use it to create a server
resource "aws_instance" "my_server" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"
  subnet_id     = data.aws_vpc.existing_vpc.main_route_table_id
}
```

**Simple Example - Get Latest Ubuntu Image:**

```hcl
# Find the latest Ubuntu automatically
data "aws_ami" "ubuntu" {
  most_recent = true
  
  filter {
    name   = "name"
    values = ["ubuntu/images/hvm-ssd/ubuntu-focal-20.04-amd64-server-*"]
  }
}

# Use it
resource "aws_instance" "server" {
  ami           = data.aws_ami.ubuntu.id  # Latest Ubuntu
  instance_type = "t2.micro"
}
```

---

## Modules - Reusing Code

Modules are reusable packages of Terraform code. Like blueprints!

**Folder Structure:**

```
my_project/
  modules/
    web_server/
      main.tf
      variables.tf
  main.tf
```

**modules/web_server/main.tf:**

```hcl
resource "aws_instance" "server" {
  ami           = var.ami_id
  instance_type = var.instance_type
}
```

**modules/web_server/variables.tf:**

```hcl
variable "ami_id" {
  type = string
}

variable "instance_type" {
  type    = string
  default = "t2.micro"
}
```

**main.tf (Use the module):**

```hcl
module "prod_server" {
  source        = "./modules/web_server"
  ami_id        = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.large"
}

module "dev_server" {
  source        = "./modules/web_server"
  ami_id        = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"
}
```

**Benefits:**
- Write once, use everywhere
- Easy to maintain
- Promotes best practices

---

## Lifecycle Rules - Controlling Behavior

Lifecycle rules control how resources are created, updated, or destroyed.

**Simple Example - Prevent Accidental Deletion:**

```hcl
resource "aws_db_instance" "production_db" {
  allocated_storage = 20
  engine            = "mysql"
  
  lifecycle {
    prevent_destroy = true  # Cannot delete production database!
  }
}
```

If someone tries `terraform destroy`, it will refuse!

**Simple Example - Zero Downtime Updates:**

```hcl
resource "aws_instance" "web_server" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"
  
  lifecycle {
    create_before_destroy = true
  }
}
```

When updating:
1. Creates new server first
2. Then destroys old server
3. No downtime!

---

## Plan and Apply - The Safe Process

Terraform uses a two-step process to safely make changes.

**Step 1: Plan (Preview changes)**

```bash
terraform plan
```

Shows what will happen:
```
# aws_instance.my_server will be created
+ resource "aws_instance" "my_server" {
    + ami           = "ami-0c55b159cbfafe1f0"
    + instance_type = "t2.micro"
  }

Plan: 1 to add, 0 to change, 0 to destroy.
```

**Step 2: Apply (Make changes)**

```bash
terraform apply
```

Asks for confirmation:
```
Do you want to perform these actions? (yes/no)
```

After approval:
```
aws_instance.my_server: Creating...
aws_instance.my_server: Creation complete [id=i-1234567890abcdef0]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

**Why this is great:**
- See exactly what will happen
- Review before making changes
- Much safer than clicking buttons!

---

## Quick Reference

**Basic Commands:**

```bash
terraform init        # Initialize (run first time)
terraform plan        # Preview changes
terraform apply       # Make changes
terraform destroy     # Delete all resources
terraform show        # See current state
terraform validate    # Check for errors
```

**That's it! Now you understand Terraform!**
