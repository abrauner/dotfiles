# Security Policy

## Supported Versions

This project follows a simple versioning approach. Security updates will be applied to the latest version.

| Version | Supported          |
| ------- | ------------------ |
| Latest  | :white_check_mark: |
| Older   | :x:                |

## Reporting a Vulnerability

### What to Report

Please report security vulnerabilities if you discover:

- Exposed secrets or credentials in templates
- Unsafe 1Password CLI usage patterns
- Template logic that could leak sensitive information
- Insecure file permissions or configurations
- Vulnerabilities in dependencies

### How to Report

**DO NOT** create a public issue for security vulnerabilities.

Instead:

1. **Email**: Create a private vulnerability report through GitHub's security tab
2. **Include**:
   - Description of the vulnerability
   - Steps to reproduce
   - Potential impact
   - Suggested fix (if known)

### Response Timeline

- **Initial Response**: Within 96 hours
- **Status Update**: Within 1 week
- **Resolution**: Depends on complexity, typically within 2-4 weeks

### What to Expect

1. **Acknowledgment** of your report
2. **Investigation** and validation of the issue
3. **Fix Development** if vulnerability is confirmed
4. **Public Disclosure** after fix is available
5. **Credit** in release notes (if desired)

## Security Considerations

### 1Password Integration
- All secrets are retrieved via 1Password CLI
- No secrets are stored in Git repository
- Templates fail safely if 1Password is unavailable

### SSH Key Management
- SSH keys stored in 1Password vaults
- Active key rotation strategy documented
- No private keys in repository

### Template Security
- Templates validated for secret leakage
- Environment detection prevents inappropriate configs
- Conditional logic isolates work/personal data

## Best Practices for Users

1. **Keep 1Password CLI updated**
2. **Use separate vaults** for work/personal secrets
3. **Regularly rotate SSH keys** in active-ssh-keys vault
4. **Review generated configs** before applying
5. **Use encrypted age keys** for additional secrets

## Responsible Disclosure

We appreciate responsible disclosure of security vulnerabilities. Contributors who report valid security issues will be credited in release notes unless they prefer to remain anonymous.
