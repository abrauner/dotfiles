# Contributing to Chezmoi Dotfiles

Thank you for your interest in contributing to this dotfiles repository! This project provides a dotfiles management system using chezmoi and 1Password integration.

## 🎯 Project Goals

- **Security**: All secrets managed through 1Password, never stored in Git
- **Environment Adaptability**: Detection of work/personal/container environments
- **Team Usage**: Setup suitable for team adoption
- **Cross-Platform**: Works across macOS and Linux
- **Documentation**: Documentation for all features

## 🤝 How to Contribute

### Types of Contributions

1. **Bug Reports**: Issues that don't work as expected
2. **Feature Requests**: Ideas for new functionality or improvements
3. **Documentation**: Improvements to README, examples, or inline comments
4. **Code Contributions**: Bug fixes, new features, or optimizations
5. **Templates**: New application configurations or updated existing ones
6. **Testing**: Help test on different platforms and environments

### Getting Started

1. **Fork the repository**
2. **Clone your fork**:
   ```bash
   git clone git@github.com:YOUR_USERNAME/dotfiles.git
   cd dotfiles
   ```

3. **Set up the development environment**:
   ```bash
   # Install prerequisites
   brew install chezmoi gh 1password-cli

   # Test template processing (without applying)
   chezmoi execute-template < dot_gitconfig.tmpl
   ```

## 📋 Contribution Guidelines

### Before You Start

- **Check existing issues** to avoid duplicate work
- **Open an issue** for changes to discuss the approach
- **Review the documentation** to understand the project structure

### Code Standards

#### Template Files
- Use descriptive variable names in templates
- Include comments explaining complex conditionals
- Follow consistent indentation (2 spaces for YAML, 4 for others)
- Test templates with different environment variables

Example template structure:
```gotmpl
{{- if (and .isWorkLaptop (not .isContainerDev)) -}}
# Work-specific configuration
# This section only applies to physical work laptops
[section]
    setting = {{ onepasswordRead "op://work/item/field" }}
{{- end }}
```

#### 1Password Integration
- Use descriptive vault paths: `op://vault/item/field`
- Document new 1Password requirements in the README
- Never include actual secrets in code or commit messages
- Use consistent vault organization (`work` vs `private`)

#### Environment Detection
- Leverage existing variables (`isWorkLaptop`, `isContainerDev`, etc.)
- Add new detection only when necessary
- Document new environment variables in the README

### Commit Guidelines

#### Commit Message Format
```
type(scope): brief description

Detailed explanation of what changed and why.

- Bullet points for multiple changes
- Reference issues with #123
```

### Pull Request Process

1. **Create a feature branch**:
   ```bash
   git checkout -b feature/description-of-change
   ```

2. **Make your changes** following the guidelines above

3. **Test your changes**:
   ```bash
   # Test template execution
   chezmoi execute-template < your-template.tmpl

   # Check for syntax errors
   chezmoi apply --dry-run
   ```

4. **Update documentation** if needed:
   - Add new 1Password paths to README tables
   - Document new environment variables
   - Update installation instructions if new tools are required

4. **Submit pull request** with:
   - Description of changes
   - Reference to related issues
   - Testing information
   - Breaking changes (if any)

## 🧪 Testing Guidelines

### Template Testing
```bash
# Test template processing
chezmoi execute-template < template.tmpl

# Dry run to check file generation
chezmoi apply --dry-run

# Check environment detection
chezmoi data | grep -E "(isWorkLaptop|isContainerDev)"
```

### Security Testing
- Ensure no secrets are leaked in templates
- Verify 1Password paths are correct
- Test with missing 1Password items (should fail gracefully)

### Cross-Platform Testing
- Test on both macOS and Linux when possible
- Verify path handling across platforms
- Check Homebrew vs package manager differences

## 📝 Documentation Standards

### README Updates
- Keep 1Password vault tables current
- Document new environment variables
- Update installation steps for new dependencies
- Add examples for new features

### Code Comments
- Explain complex template logic
- Document environment-specific behavior
- Include usage examples for new functions

### Commit Documentation
- Reference related issues
- Explain the "why" not just the "what"
- Include testing information

## 🐛 Bug Reports

When reporting bugs, please include:

### Environment Information
```bash
# Include output of these commands
chezmoi --version
op --version
uname -a
echo $SHELL
```

### Template Context
```bash
# Include relevant chezmoi data
chezmoi data | grep -E "(isWorkLaptop|isContainerDev|hostname)"
```

### Steps to Reproduce
1. Step-by-step instructions
2. Expected vs actual behavior
3. Error messages (with sensitive info redacted)
4. Relevant configuration snippets

### Example Bug Report Template
```markdown
**Environment:**
- OS: macOS 14.0
- chezmoi: v2.40.0
- Shell: zsh

**Expected Behavior:**
SSH config should be generated with work-specific settings

**Actual Behavior:**
Template fails with "no such key" error

**Steps to Reproduce:**
1. Set isWorkLaptop=true
2. Run `chezmoi apply`
3. Error occurs in private_dot_ssh/config.tmpl

**Error Message:**
```
template: error executing template: no such key: network-id
```

**Additional Context:**
1Password item exists and op CLI works manually
```

## 🔒 Security Considerations

### What NOT to Include
- ❌ Actual passwords, tokens, or keys
- ❌ Real hostnames or IP addresses
- ❌ Company-specific information
- ❌ Personal email addresses or usernames

### What TO Include
- ✅ 1Password path references (`op://vault/item/field`)
- ✅ Generic placeholders (`YOUR_USERNAME`, `COMPANY_PREFIX`)
- ✅ Environment variable names
- ✅ Template logic and conditions

### Security Review
All contributions undergo security review to ensure:
- No secrets are exposed
- 1Password integration is secure
- Template logic doesn't leak sensitive data

### Getting Help

- **Issues**: Open a GitHub issue for bugs or feature requests
- **Documentation**: Check the README for setup instructions

## 🏆 Recognition

Contributors will be:
- Listed in the repository contributors
- Mentioned in release notes for contributions
- Invited to help maintain the project for ongoing contributors

## 📄 License

By contributing, you agree that your contributions will be licensed under the same license as the project (MIT License).

---

Thank you for contributing to this dotfiles setup!
