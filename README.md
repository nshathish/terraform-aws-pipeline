# Terraform AWS Pipeline

A Terraform project for managing AWS infrastructure with automated CI/CD using GitHub Actions.

## Overview

This repository contains Terraform configurations for AWS infrastructure and GitHub Actions workflows for automated testing, validation, and deployment.

## Features

- ✅ Automated Terraform validation and formatting checks
- ✅ Terraform plan on pull requests
- ✅ Automated deployment with approval gates
- ✅ Security scanning with TFLint
- ✅ PR comments with plan output

## Project Structure

```
.
├── main.tf           # Main Terraform configuration
├── variables.tf      # Variable definitions
├── outputs.tf        # Output definitions
└── .github/
    └── workflows/
        ├── terraform-ci.yml      # Validation and formatting checks
        ├── terraform-plan.yml    # Plan on pull requests
        └── terraform-apply.yml   # Apply to production
```

## Prerequisites

- Terraform >= 1.0
- AWS Account
- GitHub repository with Actions enabled

## CI/CD Workflows

### 1. Terraform CI (`terraform-ci.yml`)

Runs on every pull request and push to main/develop branches:

- **Terraform Format Check**: Ensures code is properly formatted
- **Terraform Init**: Initializes the Terraform working directory
- **Terraform Validate**: Validates the Terraform configuration
- **TFLint**: Runs linting to catch potential issues
- **PR Comments**: Posts results as comments on pull requests

### 2. Terraform Plan (`terraform-plan.yml`)

Runs on pull requests to main branch:

- Generates a Terraform plan
- Uploads plan as artifact
- Comments the plan output on the PR
- Requires AWS credentials to be configured

### 3. Terraform Apply (`terraform-apply.yml`)

Applies Terraform changes to production:

- Triggered manually via `workflow_dispatch` (for safety)
- Can be configured to run automatically on push to main
- Requires environment approval for production
- Requires AWS credentials to be configured

## Setup Instructions

### 1. Configure AWS Authentication

For GitHub Actions to access AWS, you need to set up OIDC authentication.

📖 **[See detailed AWS OIDC setup guide](docs/AWS_OIDC_SETUP.md)**

#### Quick Steps:

**Option A: Using OIDC (Recommended)**

1. Create an IAM OIDC identity provider in AWS for GitHub Actions
2. Create an IAM role with the necessary permissions
3. Add the role ARN as a repository secret named `AWS_ROLE_ARN`
4. Uncomment the AWS credentials configuration in the workflow files

**Option B: Using Access Keys (Not Recommended)**

1. Create AWS access keys
2. Add them as repository secrets:
   - `AWS_ACCESS_KEY_ID`
   - `AWS_SECRET_ACCESS_KEY`
3. Modify workflows to use access key authentication

### 2. Configure Backend (Optional)

For remote state management, configure a backend in `main.tf`:

```hcl
terraform {
  backend "s3" {
    bucket         = "your-terraform-state-bucket"
    key            = "terraform.tfstate"
    region         = "us-east-1"
    dynamodb_table = "terraform-state-lock"
    encrypt        = true
  }
}
```

### 3. Enable Workflows

1. Uncomment the AWS credentials sections in workflow files
2. For automatic deployments, uncomment the `on: push` trigger in `terraform-apply.yml`
3. Set up environment protection rules in GitHub Settings → Environments → production

### 4. Configure Repository Secrets

Go to Settings → Secrets and variables → Actions and add:

- `AWS_ROLE_ARN`: ARN of the IAM role for OIDC authentication
- Or `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY` for access key authentication

## Local Development

### Initialize Terraform

```bash
terraform init
```

### Format Code

```bash
terraform fmt -recursive
```

### Validate Configuration

```bash
terraform validate
```

### Plan Changes

```bash
terraform plan
```

### Apply Changes

```bash
terraform apply
```

## Contributing

Please see our [Contributing Guide](CONTRIBUTING.md) for detailed information on:

- Development workflow
- Code standards
- Testing guidelines
- Pull request process
- Security guidelines

## Security

- Never commit sensitive data (credentials, keys, etc.)
- Use `.tfvars` files for sensitive variables (already in `.gitignore`)
- Always review Terraform plans before applying
- Use environment protection rules for production deployments

## License

MIT