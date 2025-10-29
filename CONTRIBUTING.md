# Contributing to Terraform AWS Pipeline

Thank you for your interest in contributing! This document provides guidelines for contributing to this project.

## Development Workflow

1. **Fork and Clone**
   ```bash
   git clone https://github.com/YOUR_USERNAME/terraform-aws-pipeline.git
   cd terraform-aws-pipeline
   ```

2. **Create a Feature Branch**
   ```bash
   git checkout -b feature/your-feature-name
   ```

3. **Make Your Changes**
   - Edit Terraform files
   - Ensure code follows best practices
   - Test your changes locally

4. **Test Locally**
   ```bash
   # Format code
   terraform fmt -recursive
   
   # Validate configuration
   terraform init -backend=false
   terraform validate
   
   # Check plan (requires AWS credentials)
   terraform plan
   ```

5. **Commit Your Changes**
   ```bash
   git add .
   git commit -m "feat: add descriptive commit message"
   ```

6. **Push and Create PR**
   ```bash
   git push origin feature/your-feature-name
   ```
   Then create a pull request on GitHub.

## Code Standards

### Terraform Code Style

- Use `terraform fmt` to format all `.tf` files
- Follow [Terraform style conventions](https://www.terraform.io/docs/language/syntax/style.html)
- Use meaningful resource names
- Add comments for complex logic
- Use variables for configurable values

### Commit Messages

Follow [Conventional Commits](https://www.conventionalcommits.org/):

- `feat:` for new features
- `fix:` for bug fixes
- `docs:` for documentation changes
- `chore:` for maintenance tasks
- `refactor:` for code refactoring

Examples:
```
feat: add RDS database module
fix: correct S3 bucket versioning configuration
docs: update AWS OIDC setup guide
```

## Pull Request Process

1. **Ensure CI Passes**
   - All GitHub Actions checks must pass
   - Terraform validation must succeed
   - Format check must pass

2. **Review Terraform Plan**
   - Check the plan output in PR comments
   - Verify changes match expectations
   - Ensure no unintended resource destruction

3. **Update Documentation**
   - Update README if adding new features
   - Add comments to complex Terraform code
   - Update variable descriptions

4. **Get Approval**
   - At least one approval required
   - Address review comments
   - Keep PR focused and small

## Testing Guidelines

### Local Testing

Before submitting a PR:

1. **Format Check**
   ```bash
   terraform fmt -check -recursive
   ```

2. **Validation**
   ```bash
   terraform init -backend=false
   terraform validate
   ```

3. **Plan (if AWS credentials available)**
   ```bash
   terraform plan
   ```

### CI/CD Testing

GitHub Actions will automatically:
- Check Terraform formatting
- Validate Terraform configuration
- Run TFLint for best practices
- Generate Terraform plan (if AWS credentials configured)

## Adding New Features

### Adding a New Resource

1. Define the resource in `main.tf` or create a new module
2. Add necessary variables to `variables.tf`
3. Add outputs to `outputs.tf` if needed
4. Update README with usage examples
5. Test thoroughly before submitting PR

### Adding a New Workflow

1. Create workflow file in `.github/workflows/`
2. Test workflow syntax with YAML validator
3. Document the workflow in README
4. Ensure proper permissions are set

## Security Guidelines

- Never commit sensitive data (credentials, keys, etc.)
- Use `.tfvars` files for sensitive variables (already in `.gitignore`)
- Follow AWS security best practices
- Use encryption for sensitive resources
- Implement least privilege access

## Getting Help

- Open an issue for bugs or feature requests
- Check existing issues before creating new ones
- Provide detailed information in issue descriptions
- Include error messages and logs when reporting bugs

## License

By contributing, you agree that your contributions will be licensed under the same license as the project (MIT).
