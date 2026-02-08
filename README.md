# Terraform CI/CD Pipeline

A skeletal template for implementing automated Terraform infrastructure deployments using CI/CD pipelines.

## Overview

This repository provides a foundational structure for deploying infrastructure as code (IaC) using Terraform with automated CI/CD workflows. It demonstrates best practices for staging infrastructure deployments through validation, planning, and application stages.

## Table of Contents

- [Prerequisites](#prerequisites)
- [Repository Structure](#repository-structure)
- [Pipeline Stages](#pipeline-stages)
- [Getting Started](#getting-started)
- [Configuration](#configuration)
- [Deployment Process](#deployment-process)
- [Contributing](#contributing)
- [References](#references)

## Prerequisites

Before using this pipeline, ensure you have:

- [ ] Terraform >= 1.0
- [ ] Git for version control
- [ ] CI/CD platform (GitHub Actions, GitLab CI, Jenkins, etc.)
- [ ] Cloud provider credentials (AWS, Azure, GCP, etc.)
- [ ] Appropriate IAM permissions for infrastructure deployment

## Repository Structure

```
├── .github/workflows/          # CI/CD pipeline definitions
│   ├── plan.yml               # Terraform plan workflow
│   └── apply.yml              # Terraform apply workflow
├── terraform/
│   ├── main.tf                # Primary Terraform configuration
│   ├── variables.tf           # Input variables
│   ├── outputs.tf             # Output definitions
│   ├── terraform.tfvars       # Variable values (example)
│   ├── backend.tf             # Remote state configuration
│   └── modules/               # Reusable Terraform modules
├── scripts/                   # Helper scripts for CI/CD
│   ├── validate.sh           # Terraform validation
│   ├── plan.sh               # Generate Terraform plan
│   └── apply.sh              # Apply Terraform changes
├── docs/                      # Documentation
├── .gitignore                # Git ignore rules
└── README.md                  # This file
```

## Pipeline Stages

### 1. **Validation Stage**
- Syntax checking (`terraform validate`)
- Code formatting verification (`terraform fmt`)
- Security scanning (optional: tfsec, checkov)
- Linting validation

**Trigger:** On every commit/pull request

### 2. **Plan Stage**
- Initialize Terraform working directory (`terraform init`)
- Generate execution plan (`terraform plan`)
- Save plan output for review
- Display plan in CI/CD interface

**Trigger:** On pull requests

### 3. **Apply Stage**
- Review and approve plan
- Execute Terraform apply (`terraform apply`)
- Log deployment results
- Update infrastructure state

**Trigger:** Manual approval or push to main branch

## Getting Started

### 1. Clone the Repository

```bash
git clone <repository-url>
cd cicd-pipeline-basic
```

### 2. Initialize Terraform

```bash
cd terraform/
terraform init
```

### 3. Configure Backend

Update `terraform/backend.tf` with your remote state storage:

```hcl
tf {
  backend "s3" {
    bucket = "your-terraform-state-bucket"
    key    = "prod/terraform.tfstate"
    region = "us-east-1"
  }
}
```

### 4. Set Environment Variables

Create a `.env` file (do not commit):

```bash
export TF_VAR_environment="dev"
export TF_VAR_region="us-east-1"
export AWS_ACCESS_KEY_ID="***"
export AWS_SECRET_ACCESS_KEY="***"
```

## Configuration

### Variables

Edit `terraform/variables.tf` to define input variables:

```hcl
variable "environment" {
  description = "Deployment environment"
  type        = string
}

variable "region" {
  description = "AWS region"
  type        = string
  default     = "us-east-1"
}
```

### Outputs

Define outputs in `terraform/outputs.tf`:

```hcl
output "resource_id" {
  description = "ID of deployed resource"
  value       = aws_resource.example.id
}
```

## Deployment Process

### Local Development

```bash
# Validate configuration
tf validate

# Format check
tf fmt -check

# Generate plan
terraform plan -out=tfplan

# Review plan and apply
terraform apply tfplan
```

### CI/CD Pipeline

1. **Developer:** Creates a feature branch and commits changes
2. **Validation:** Automated checks run on pull request
3. **Plan:** Terraform plan generated and posted as PR comment
4. **Review:** Team reviews plan and approves
5. **Apply:** Merge to main triggers automated deployment
6. **Verify:** Pipeline logs deployment results

## Best Practices

- [ ] Always use remote state management
- [ ] Implement state locking to prevent concurrent modifications
- [ ] Use workspaces for environment separation (dev/staging/prod)
- [ ] Document all variables and outputs
- [ ] Implement approval gates for production deployments
- [ ] Maintain detailed audit logs of all infrastructure changes
- [ ] Use `.gitignore` to exclude sensitive files:
  ```
  *.tfstate
  *.tfstate.*
  .terraform/
  .terraform.lock.hcl
  *.tfvars
  !terraform.tfvars.example
  ```

## Contributing

1. Create a feature branch: `git checkout -b feature/your-feature`
2. Validate your changes: `terraform validate && terraform fmt`
3. Commit with clear messages
4. Push to remote: `git push origin feature/your-feature`
5. Open a pull request for review
6. Address feedback and merge after approval

## References

- [Terraform Documentation](https://www.terraform.io/docs)
- [Terraform Best Practices](https://www.terraform.io/cloud-docs/recommended-practices)
- [State Management](https://www.terraform.io/language/state)
- [CI/CD Integration Guide](https://www.terraform.io/cloud-docs/vcs)