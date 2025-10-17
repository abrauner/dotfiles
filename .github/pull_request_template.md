## Description

Brief description of the changes in this PR.

## Type of Change

- [ ] Bug fix (non-breaking change which fixes an issue)
- [ ] New feature (non-breaking change which adds functionality)
- [ ] Breaking change (fix or feature that would cause existing functionality to not work as expected)
- [ ] Documentation update
- [ ] Code refactoring
- [ ] Security improvement

## Changes Made

- [ ] New template files added
- [ ] Existing templates modified
- [ ] 1Password vault paths updated
- [ ] Environment detection logic changed
- [ ] Documentation updated
- [ ] Tests added/updated

## Testing

- [ ] Templates execute without errors (`chezmoi execute-template`)
- [ ] Dry run completes successfully (`chezmoi apply --dry-run`)
- [ ] Environment detection works correctly
- [ ] Cross-platform compatibility verified (if applicable)
- [ ] Security review completed (no secrets exposed)

### Test Environment
- OS:
- chezmoi version:
- 1Password CLI version:

## 1Password Changes

If this PR adds or modifies 1Password integration:

- [ ] New vault paths documented in README
- [ ] Vault structure table updated
- [ ] Path validation tested
- [ ] Fallback behavior defined

### New 1Password Paths Added
```
op://vault/item/field - Description
```

## Security Checklist

- [ ] No secrets or credentials included in code
- [ ] Template logic doesn't leak sensitive information
- [ ] 1Password paths are correct and secure
- [ ] File permissions are appropriate
- [ ] Changes don't expose personal information

## Documentation

- [ ] README updated (if needed)
- [ ] CONTRIBUTING.md updated (if needed)
- [ ] Code comments added/updated
- [ ] Examples provided (if needed)

## Breaking Changes

If this is a breaking change, describe:
- What breaks
- How users should migrate
- Why this change is necessary

## Related Issues

Fixes #(issue number)
Closes #(issue number)
Related to #(issue number)

## Additional Notes

Any additional information about this PR.
