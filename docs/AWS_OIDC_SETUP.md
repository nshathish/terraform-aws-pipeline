# AWS OIDC Setup for GitHub Actions

This guide explains how to set up AWS authentication for GitHub Actions using OpenID Connect (OIDC), which is more secure than using long-lived access keys.

## Benefits of OIDC

- No need to store AWS credentials as GitHub secrets
- Temporary credentials that automatically expire
- Better security posture
- Audit trail through AWS CloudTrail

## Prerequisites

- AWS account with admin access
- GitHub repository

## Step 1: Create OIDC Identity Provider in AWS

1. Go to AWS IAM Console → Identity providers → Add provider
2. Choose "OpenID Connect"
3. Configure:
   - **Provider URL**: `https://token.actions.githubusercontent.com`
   - **Audience**: `sts.amazonaws.com`
4. Click "Add provider"

## Step 2: Create IAM Role

Create an IAM role that GitHub Actions will assume:

```bash
# Trust policy for the role (replace YOUR_ORG and YOUR_REPO)
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Federated": "arn:aws:iam::YOUR_ACCOUNT_ID:oidc-provider/token.actions.githubusercontent.com"
      },
      "Action": "sts:AssumeRoleWithWebIdentity",
      "Condition": {
        "StringEquals": {
          "token.actions.githubusercontent.com:aud": "sts.amazonaws.com"
        },
        "StringLike": {
          "token.actions.githubusercontent.com:sub": "repo:YOUR_ORG/YOUR_REPO:*"
        }
      }
    }
  ]
}
```

## Step 3: Attach Permissions Policy

Attach a policy to the role with the permissions needed for Terraform. Example:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:*",
        "dynamodb:*",
        "ec2:Describe*"
      ],
      "Resource": "*"
    }
  ]
}
```

**Important**: Follow the principle of least privilege. Only grant the permissions your Terraform code needs.

## Step 4: Configure GitHub Repository

1. Go to your GitHub repository
2. Navigate to Settings → Secrets and variables → Actions
3. Add a new repository secret:
   - **Name**: `AWS_ROLE_ARN`
   - **Value**: The ARN of the role you created (e.g., `arn:aws:iam::123456789012:role/GitHubActionsRole`)

## Step 5: Update Workflow Files

The workflow files are already configured to use OIDC. Simply uncomment the AWS credentials sections:

In `terraform-plan.yml` and `terraform-apply.yml`, uncomment:

```yaml
- name: Configure AWS Credentials
  uses: aws-actions/configure-aws-credentials@v4
  with:
    role-to-assume: ${{ secrets.AWS_ROLE_ARN }}
    aws-region: us-east-1
```

## Step 6: Test the Setup

1. Create a pull request with a small Terraform change
2. Check the GitHub Actions logs to verify authentication works
3. Review the Terraform plan in the PR comments

## Troubleshooting

### Error: "Not authorized to perform sts:AssumeRoleWithWebIdentity"

- Verify the trust policy includes your repository
- Check that the OIDC provider is correctly configured
- Ensure the role ARN in GitHub secrets is correct

### Error: "Access Denied" when running Terraform

- Review the IAM policy attached to the role
- Ensure all required permissions are granted
- Check for any Service Control Policies (SCPs) that might restrict access

## Additional Resources

- [AWS OIDC Documentation](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_providers_create_oidc.html)
- [GitHub Actions OIDC](https://docs.github.com/en/actions/deployment/security-hardening-your-deployments/configuring-openid-connect-in-amazon-web-services)
- [AWS Configure Credentials Action](https://github.com/aws-actions/configure-aws-credentials)
