# AWS EC2 Nginx Deployment using Terraform

A hands-on **AWS + Terraform practice project** that demonstrates how to provision a complete public web-server infrastructure on AWS using Infrastructure as Code (IaC).

The project creates an Ubuntu EC2 instance, installs Nginx automatically using Terraform `user_data`, and hosts a simple web page displaying **"Hello to KIET"**.

> 🚧 **This is a practice/learning repository**, created to understand AWS networking, EC2, Nginx, and Terraform fundamentals.

---

## 🏗️ Architecture

```text
                         Internet
                            │
                            │ HTTP :80
                            ▼
                  ┌─────────────────────┐
                  │  Internet Gateway   │
                  └──────────┬──────────┘
                             │
                             ▼
                  ┌─────────────────────┐
                  │   Public Route      │
                  │       Table         │
                  │  0.0.0.0/0 → IGW    │
                  └──────────┬──────────┘
                             │
                             ▼
              ┌──────────────────────────────┐
              │       Public Subnet          │
              │        10.1.1.0/24           │
              │                              │
              │    ┌────────────────────┐    │
              │    │    EC2 t3.micro    │    │
              │    │   Ubuntu 24.04 LTS │    │
              │    │                    │    │
              │    │      Nginx :80     │    │
              │    └────────────────────┘    │
              └──────────────────────────────┘
                             │
                             ▼
                     "Hello to KIET"
```
## Tech Stack

- AWS
- VPC
- EC2
- Internet Gateway
- Public Subnet
- Route Table
- Security Group
- Terraform
- Ubuntu 24.04 LTS
- Nginx
- AWS CLI

## 📁 Project Structure
```
terraform_practice/
│
├── main.tf
├── providers.tf
├── variables.tf
├── .terraform.lock.hcl
├── terraform.tfstate
└── terraform.tfstate.backup
```
# File Description

| File | Purpose |
|------|---------|
| `main.tf` | Defines AWS infrastructure resources |
| `providers.tf` | Configures Terraform and AWS provider |
| `variables.tf` | Defines configurable variables |
| `.terraform.lock.hcl` | Locks provider versions |
| `terraform.tfstate` | Tracks infrastructure managed by Terraform |
| `terraform.tfstate.backup` | Backup of Terraform state |

## AWS Infrastructure

### 1. VPC

- Name: `main-vpc`
- CIDR: `10.1.0.0/16`
- Region: `us-east-1`

### 2. Public Subnet

- Name: `public-subnet`
- CIDR: `10.1.1.0/24`
- Availability Zone: `us-east-1a`
- Auto-assign Public IP: Enabled

Connected to the Internet Gateway through the public route table.

### 3. Internet Gateway

- Name: `main-igw`

### 4. Public Route Table

| Destination | Target |
|-------------|--------|
| `0.0.0.0/0` | Internet Gateway |

### 5. Route Table Association
```
Public Subnet
     │
     ▼
Public Route Table
     │
     ▼
Internet Gateway
```

## Security Group

**Name:** `KIET_CSE_SSH_HTTP`
**Description:** Allow SSH and HTTP

**Inbound Rules**

| Type | Protocol | Port | Source | Purpose |
|------|----------|------|--------|---------|
| SSH | TCP | 22 | 0.0.0.0/0 | SSH access |
| HTTP | TCP | 80 | 0.0.0.0/0 | Web server access |

**Outbound Rules**

| Type | Protocol | Port | Destination |
|------|----------|------|-------------|
| All Traffic | All | All | 0.0.0.0/0 |

> ⚠️ Practice configuration: SSH is open to `0.0.0.0/0` for learning purposes. In production, restrict SSH to a trusted IP or network.

## EC2 Instance

| Configuration | Value |
|----------------|-------|
| AMI | Ubuntu 24.04 LTS |
| Instance Type | t3.micro |
| Region | us-east-1 |
| Availability Zone | us-east-1a |
| Public IP | Enabled |
| Web Server | Nginx |
| HTTP Port | 80 |
| SSH Port | 22 |

## Nginx Auto-Installation

Installed automatically via EC2 `user_data`:

```bash
#!/bin/bash
apt update -y
apt install nginx -y
systemctl start nginx

echo "Hello to KIET" > /var/www/html/index.nginx-debian.html
```

This updates packages, installs Nginx, starts the service, and replaces the default page.

## Accessing the Web Server

### Manual AWS EC2 Deployment

Before implementing the infrastructure with Terraform, Nginx was also configured manually on an AWS EC2 instance for practice and understanding.

```text
http://54.172.181.98
```

### Terraform AWS EC2 Deployment

The final Nginx server was provisioned automatically using Terraform.
```
http://44.220.87.78
```
Both deployments successfully displayed:
```
Hello to KIET
```
## Terraform Workflow

```bash
# Initialize working directory and providers
terraform init

# Validate configuration syntax
terraform validate

# Preview planned changes
terraform plan

# Apply and create infrastructure
terraform apply

# View current state
terraform show

# Destroy infrastructure when done
terraform destroy
```

## 🔄 Terraform Workflow
```
Write Terraform Configuration
            │
            ▼
      terraform init
            │
            ▼
    terraform validate
            │
            ▼
      terraform plan
            │
            ▼
      terraform apply
            │
            ▼
     AWS Infrastructure
            │
            ▼
       Test Nginx
            │
            ▼
    terraform destroy
```

The Nginx server successfully returned:

Hello to KIET
