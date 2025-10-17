[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![chezmoi](https://img.shields.io/badge/managed%20with-chezmoi-blue)](https://www.chezmoi.io/)
[![1Password](https://img.shields.io/badge/secrets-1Password-blue)](https://1password.com/)

## Table of Contents

- [What is Chezmoi?](#what-is-chezmoi)
  - [Key Features Used in This Setup](#key-features-used-in-this-setup)
- [Installation](#installation)
  - [Prerequisites](#prerequisites)
  - [Initial Setup](#initial-setup)
  - [Post-Installation](#post-installation)
- [Security Considerations](#security-considerations)
- [Usage](#usage)
  - [Common Chezmoi Commands](#common-chezmoi-commands)
  - [Discovering Managed and Unmanaged Files](#discovering-managed-and-unmanaged-files)
  - [Managing Secrets](#managing-secrets)
  - [Aliases](#aliases)
- [File Organization](#file-organization)
  - [Chezmoi Naming Conventions](#chezmoi-naming-conventions)
  - [Key Configuration Files](#key-configuration-files)
- [Environment Detection](#environment-detection)
  - [Machine Type Detection](#machine-type-detection)
  - [Conditional Configuration Examples](#conditional-configuration-examples)
- [1Password Integration](#1password-integration)
  - [1Password Vault Structure](#1password-vault-structure)
  - [SSH Key Management Strategy](#ssh-key-management-strategy)
- [Troubleshooting](#troubleshooting)
  - [1Password CLI Issues](#1password-cli-issues)
  - [Chezmoi Template Issues](#chezmoi-template-issues)
  - [Environment Detection Issues](#environment-detection-issues)

# Getting Started with Chezmoi Dotfiles Management

This repository contains a dotfiles setup using [chezmoi](https://www.chezmoi.io/) as the dotfile manager, integrated with 1Password for credential and configuration management.

## What is Chezmoi?

[Chezmoi](https://www.chezmoi.io/) is a command-line tool that helps you manage your dotfiles across multiple machines. It provides:

- **Synchronization** across multiple machines and operating systems
- **Secret management** by integrating with password managers like 1Password
- **Templating** to customize configurations based on the target machine
- **Version control** for dotfiles with Git while keeping secrets separate
- **Cross-platform support** with conditional configurations

### Key Features Used in This Setup

1. **Template Support**: Files ending in `.tmpl` are processed as Go templates, allowing dynamic configuration
2. **Secret Management**: Integration with 1Password CLI for secure secret retrieval
3. **Conditional Configuration**: Different configurations for work laptops vs. personal machines
4. **Container Environment Detection**: Special handling for development containers and codespaces

## Installation

### Prerequisites

1. **Install Homebrew** (macOS/Linux):

   [Homebrew](https://brew.sh/) is a package manager for macOS and Linux. This dotfiles setup uses Homebrew for package management.

   Install Homebrew by running:
   ```bash
   /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
   ```

   After installation, make sure Homebrew is in your PATH:
   ```bash
   # Add Homebrew to your PATH (the installer will show you the exact command)
   echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zprofile
   eval "$(/opt/homebrew/bin/brew shellenv)"

   # Verify installation
   brew --version
   ```

2. **Install required tools**:
   ```bash
   brew install chezmoi gh 1password-cli
   ```

3. **Set up 1Password**:
   - Install 1Password desktop application
   - Enable 1Password CLI integration
   - Configure SSH agent integration in 1Password settings

### Initial Setup

1. **Initialize chezmoi** with this repository:
   ```bash
   chezmoi init git@github.com:YOUR_USERNAME/dotfiles.git
   ```

   Replace `YOUR_USERNAME` with your actual GitHub username.

2. **Apply the dotfiles**:
   ```bash
   chezmoi apply
   ```

3. **Configure chezmoi**:

   Set up chezmoi with the configuration used in this repository. Create or edit `~/.config/chezmoi/chezmoi.toml`:

   ```bash
   mkdir -p ~/.config/chezmoi
   cat > ~/.config/chezmoi/chezmoi.toml << 'EOF'
   [add]
       secrets = "error"
   [git]
       autoCommit = true
       autoPush = false
   [edit]
       command = "mcedit"
   EOF
   ```

   This configuration enables:
   - **autoCommit**: Automatically commits changes when you edit files
   - **autoPush**: Automatically pushes commits to the remote repository
   - **editor**: Sets mcedit as the default editor (you can change this to your preferred editor like "code", "vim", etc.)

### Post-Installation

Install additional development tools:

```bash
# Java (using jabba)
jabba install temurin@25

# Set up Java version management (using jenv)
jenv add $(jabba which temurin@25)/Contents/Home/
jenv global 25

# Python (using pyenv)
pyenv install 3.13.3

# Node.js (using nvm)
nvm install --lts
```

## Security Considerations

- **Secrets are never stored in Git** - only 1Password references
- **SSH keys are managed through 1Password** and deployed to systems
- **Work and personal configurations are separated** using different vaults
- **Container environments are detected** to skip certain configurations

## Usage

### Common Chezmoi Commands

```bash
# Check what would change
chezmoi diff

# Apply changes
chezmoi apply

# Edit a file
chezmoi edit ~/.gitconfig

# Add a new file
chezmoi add ~/.newconfig

# Add a new template file
chezmoi add --template ~/.config/app/config.yaml

# Convert existing file to template
chezmoi chattr +template ~/.gitconfig

# Update from repository
chezmoi update

# Check status
chezmoi status
```

### Discovering Managed and Unmanaged Files

Understanding which files chezmoi manages helps you work more effectively with the system.

#### `chezmoi managed` - List All Managed Files

This command shows all files that chezmoi is currently managing:

```bash
# List all managed files
chezmoi managed

# Example output for this dotfiles setup:
# /Users/username/.ansible-vault
# /Users/username/.chezmoiignore
# /Users/username/.gitconfig
# /Users/username/.gitignore_global
# /Users/username/.p10k.zsh
# /Users/username/.zprofile
# /Users/username/.zshrc
# /Users/username/.zsh-aliases
# /Users/username/.config/gcloud/configurations/config_default
# /Users/username/.config/gh/config.yml
# /Users/username/.config/gh/hosts.yml
# /Users/username/.docker/config.json
# /Users/username/.httpie/config.json
# /Users/username/.httpie/plugins/httpie_zign.py
# /Users/username/.m2/settings.xml
# /Users/username/.m2/settings-security.xml
# /Users/username/.ssh/config
# /Users/username/.ssh/id_github
# /Users/username/.ssh/id_github.pub
# /Users/username/.ssh/id_rsa_calculon
# /Users/username/.ssh/id_rsa_calculon.pub
# /Users/username/development/.gitconfig
```

**Useful filtering examples:**
```bash
# Check if a specific file is managed
chezmoi managed | grep .gitconfig

# Count managed files
chezmoi managed | wc -l

# Show only SSH-related managed files
chezmoi managed | grep ssh

# Show managed files in a specific directory
chezmoi managed | grep .config
```

#### `chezmoi unmanaged` - Find Unmanaged Dotfiles

This command finds dotfiles in your home directory that chezmoi could potentially manage but currently doesn't:

```bash
# Find unmanaged dotfiles
chezmoi unmanaged

# Example output might include:
# /Users/username/.bash_history
# /Users/username/.zsh_history
# /Users/username/.viminfo
# /Users/username/.lesshst
# /Users/username/.DS_Store
# /Users/username/.cache
# /Users/username/.local/state
# /Users/username/.config/configstore
# /Users/username/.docker/buildx
# /Users/username/.vscode/extensions
```

**Practical examples:**
```bash
# Find unmanaged config files that might be worth managing
chezmoi unmanaged | grep -E "\.(conf|config|json|yaml|yml|toml)$"

# Find unmanaged dotfiles in home directory only (not subdirectories)
chezmoi unmanaged | grep "^$HOME/\.[^/]*$"

# Find unmanaged files you might want to add
chezmoi unmanaged | grep -v -E "(history|cache|state|DS_Store|lesshst|viminfo)"

# Check for unmanaged SSH files
chezmoi unmanaged | grep ssh
```

#### Workflow Examples

**Discovering files to manage:**
```bash
# Find configuration files that might benefit from chezmoi management
chezmoi unmanaged | grep -E "\.(json|yaml|yml|toml|conf|config)$" | head -10

# Example: You find ~/.vscode/settings.json is unmanaged
# Decide if you want to manage it:
cat ~/.vscode/settings.json

# If it contains no secrets, add it as-is:
chezmoi add ~/.vscode/settings.json

# If it contains secrets, add as template:
chezmoi add --template ~/.vscode/settings.json
```

**Verifying your setup:**
```bash
# Check what you're managing vs what you could manage
echo "Managed files: $(chezmoi managed | wc -l)"
echo "Unmanaged dotfiles: $(chezmoi unmanaged | wc -l)"

# Find the source file for any managed file
chezmoi source-path ~/.gitconfig
# Output: /Users/username/.local/share/chezmoi/dot_gitconfig.tmpl

# See what would happen if you applied chezmoi
chezmoi diff
```

### Managing Secrets

When you need to add new secrets:

1. Add the secret to appropriate 1Password vault
2. Use the secret in templates with `onepasswordRead "op://vault/item/field"`
3. Test with `chezmoi execute-template < template_file`

### Aliases

The setup includes these aliases:
- `cm` → `chezmoi` (shorthand)
- `brewski` → Update, upgrade, and clean Homebrew (macOS only)


## File Organization

### Chezmoi Naming Conventions

- `dot_filename` → `.filename` (hidden files)
- `private_dot_filename` → `.filename` (hidden files, not added to git)
- `empty_dot_filename` → `.filename` (creates empty file)
- `filename.tmpl` → `filename` (processed as template)

### Key Configuration Files

- **`.chezmoi.toml.tmpl`** - Main chezmoi configuration with environment detection
- **`dot_gitconfig.tmpl`** - Global Git configuration with 1Password integration
- **`development/dot_gitconfig.tmpl`** - Work-specific Git configuration
- **`dot_zshrc.tmpl`** - Zsh shell configuration
- **`Brewfile.tmpl`** - Homebrew package management with conditional packages
- **`private_dot_ssh/`** - SSH configuration and keys from 1Password
- **`dot_m2/`** - Maven configuration for Java development

## Environment Detection

The setup detects different environments and applies configurations accordingly:

### Machine Type Detection

The environment detection logic is implemented in `.chezmoi.toml.tmpl` and creates several boolean variables that templates can use for conditional configuration.

#### Detection Logic Implementation

**Step 1: Container Environment Detection**
```gotmpl
{{- $codespaces:= env "CODESPACES" | not | not -}}
{{- $vscode:= env "TERM_PROGRAM" | eq "vscode" -}}
{{- $remoteContainer:= env "REMOTE_CONTAINERS" | not | not -}}
```

**Step 2: Combined Container Detection**
```gotmpl
[data]
    isCodespaces = {{ $codespaces }}
    isVscode = {{ $vscode }}
    isContainerDev = {{ or $codespaces $vscode $remoteContainer }}
```

**Step 3: Work/Personal Laptop Detection** (only when not in container)
```gotmpl
{{- if not (or $codespaces $vscode $remoteContainer) }}
    {{ $hostname_prefix := onepasswordRead "op://work/dotfiles/hostname-prefix" }}
    isWorkLaptop = {{ contains $hostname_prefix .chezmoi.hostname }}
    isPrivateLaptop = {{ not (contains $hostname_prefix .chezmoi.hostname) }}
{{- else }}
    isPrivateLaptop = false
    isWorkLaptop = false
{{ end }}
```

#### Available Variables

The detection creates these boolean variables available in all templates:

- **`isCodespaces`**: `true` when running in GitHub Codespaces
- **`isVscode`**: `true` when running in VS Code terminal
- **`isContainerDev`**: `true` for any container development environment
- **`isWorkLaptop`**: `true` when hostname contains the company prefix (only on physical machines)
- **`isPrivateLaptop`**: `true` when hostname doesn't contain company prefix (only on physical machines)

#### Detection Methods

1. **Work Laptop Detection**:
   - Retrieves `hostname-prefix` from 1Password work vault
   - Compares against current machine's hostname using `contains` function
   - Only runs on physical machines (not in development containers)
   - Sets `isWorkLaptop` variable for conditional configurations

2. **Container Development Environment Detection**:
   - **GitHub Codespaces**: Checks `CODESPACES` environment variable
   - **VS Code Remote Containers**: Checks `REMOTE_CONTAINERS` environment variable
   - **VS Code Terminal**: Checks if `TERM_PROGRAM=vscode`
   - Sets `isContainerDev` variable to skip certain configurations (like SSH setup)

### Conditional Configuration Examples

These variables enable sophisticated conditional configurations across different environments:

#### Work vs Personal Laptop Configuration
```gotmpl
{{- if (and .isWorkLaptop (not .isContainerDev)) -}}
# Work-specific Git configuration
[user]
    name = {{ onepasswordRead "op://work/dotfiles/name" }}
    email = {{ onepasswordRead "op://work/dotfiles/email" }}

# Work-specific SSH config
Host work-server
    HostName {{ onepasswordRead "op://work/dotfiles/server-host" }}
    User {{ onepasswordRead "op://work/dotfiles/network-id" }}
{{- end }}

{{- if (and .isPrivateLaptop (not .isContainerDev)) -}}
# Personal Git configuration
[user]
    name = {{ onepasswordRead "op://private/dotfiles/Fullname" }}
    email = {{ onepasswordRead "op://private/dotfiles/email" }}

# Personal SSH keys and config
Host personal-server
    HostName {{ onepasswordRead "op://private/dotfiles/server-host" }}
{{- end }}
```

#### Container Environment Exclusions
```gotmpl
{{- if not .isContainerDev -}}
# SSH configuration - skip in containers since SSH agent forwarding is used
Include ~/.ssh/config.d/*

# 1Password SSH agent - not needed in containers
IdentityAgent "~/Library/Group Containers/2BUA8C4S2C.com.1password/t/agent.sock"
{{- end }}
```

#### Brewfile Package Conditions
```gotmpl
# Install Docker Desktop only on physical machines (not in containers)
{{ if not .isContainerDev }}brew "docker"{{ end }}

# Work-specific tools only on work laptops
{{ if .isWorkLaptop }}brew "{{ onepasswordRead "op://work/dotfiles/work-aws-cli" }}"{{ end }}

# Personal tools only on personal machines
{{ if .isPrivateLaptop }}brew "personal-tool"{{ end }}
```

#### Environment-Specific Shell Aliases
```bash
{{- if .isWorkLaptop }}
# Work-specific aliases
alias work-deploy='kubectl apply -f deployment.yaml'
alias work-logs='kubectl logs -f deployment/app'
{{- end }}

{{- if .isCodespaces }}
# GitHub Codespaces specific configuration
export CODESPACE_NAME="{{ env "CODESPACE_NAME" }}"
{{- end }}
```

## 1Password Integration

This dotfiles setup uses 1Password as the store for configuration data. The integration uses the 1Password CLI (`op`) to retrieve values during template processing.

### 1Password Vault Structure

The setup uses two main vaults in 1Password:

#### `private` Vault
Contains personal configuration and credentials:

| 1Password Path | Description | Used In Files |
|---|---|---|
| **Git Configuration** | | |
| `op://private/dotfiles/Fullname` | Your full name for Git configuration | `dot_gitconfig.tmpl` |
| `op://private/dotfiles/github-public-email` | Your GitHub public email | `dot_gitconfig.tmpl` |
| `op://private/dotfiles/email` | Your primary email address | `dot_gitconfig.tmpl` |
| **Google Cloud Configuration** | | |
| `op://private/dotfiles/email` | Your primary email address | `private_dot_config/gcloud/configurations/config_default.tmpl` |
| `op://private/dotfiles/gcloud-default-project` | Default Google Cloud project | `private_dot_config/gcloud/configurations/config_default.tmpl` |
| **GitHub Configuration** | | |
| `op://private/dotfiles/github-login` | Your GitHub username | `private_dot_config/gh/private_hosts.yml.tmpl` |
| **WireGuard VPN Configuration** | | |
| `op://private/dotfiles/wireguard_ip_list` | WireGuard VPN IP configuration | `private_dot_config/private_wireguard/private_empty_dot_wireguard.conf.tmpl` |
| `op://private/dotfiles/wireguard_private_key` | WireGuard private key | `private_dot_config/private_wireguard/private_empty_dot_wireguard.conf.tmpl` |
| `op://private/dotfiles/wireguard_server_public_key` | WireGuard server public key | `private_dot_config/private_wireguard/private_empty_dot_wireguard.conf.tmpl` |
| `op://private/dotfiles/wireguard_server_psk` | WireGuard pre-shared key | `private_dot_config/private_wireguard/private_empty_dot_wireguard.conf.tmpl` |
| `op://private/dotfiles/wireguard_server_endpoint` | WireGuard server endpoint | `private_dot_config/private_wireguard/private_empty_dot_wireguard.conf.tmpl` |
| **SSH Keys** | | |
| `op://private/id_github/id_github` | GitHub SSH private key | `private_dot_ssh/private_id_github.tmpl` |
| `op://private/id_github/id_github.pub` | GitHub SSH public key | `private_dot_ssh/id_github.pub.tmpl` |
| `op://private/id_rsa_calculon/private_key` | Private SSH key for servers | `private_dot_ssh/private_id_rsa_calculon.tmpl` |
| `op://private/id_rsa_calculon/public_key` | Public SSH key for servers | `private_dot_ssh/private_id_rsa_calculon.pub.tmpl` |
| **Ansible Configuration** | | |
| `op://private/ansible-vault/password` | Ansible Vault password | `dot_ansible-vault.tmpl` |

#### `work` Vault
Contains work-related configuration and credentials:

| 1Password Path | Description | Used In Files |
|---|---|---|
| **Environment Detection** | | |
| `op://work/dotfiles/hostname-prefix` | Company hostname identifier for work laptop detection | `.chezmoi.toml.tmpl` |
| **Git Configuration** | | |
| `op://work/dotfiles/email` | Work email address | `dot_gitconfig.tmpl`,<br>`development/dot_gitconfig.tmpl` |
| `op://work/dotfiles/name` | Work display name | `development/dot_gitconfig.tmpl` |
| `op://work/dotfiles/GHE-SSH-URL` | GitHub Enterprise SSH URL | `dot_gitconfig.tmpl` |
| `op://work/dotfiles/GHE-HTTPS-URL` | GitHub Enterprise HTTPS URL | `dot_gitconfig.tmpl` |
| **Homebrew Package Management** | | |
| `op://work/dotfiles/work-aws-cli` | Work-specific AWS CLI package name | `Brewfile.tmpl` |
| `op://work/dotfiles/base-infra-taps-url` | Company-specific Homebrew tap URL | `Brewfile.tmpl` |
| **Maven Configuration** | | |
| `op://work/dotfiles/maven-url` | Maven repository URL | `dot_m2/settings.xml.tmpl` |
| `op://work/dotfiles/maven-encoded-password` | Encoded Maven password | `dot_m2/settings.xml.tmpl` |
| `op://work/dotfiles/maven-url-releases` | Maven releases repository URL | `dot_m2/settings.xml.tmpl` |
| `op://work/dotfiles/maven-url-snapshots` | Maven snapshots repository URL | `dot_m2/settings.xml.tmpl` |
| `op://work/dotfiles/maven-plugin-groups` | Maven plugin groups | `dot_m2/settings.xml.tmpl` |
| `op://work/dotfiles/maven-settings-string` | Maven settings security string | `dot_m2/settings-security.xml.tmpl` |
| **Gradle Configuration** | | |
| `op://work/dotfiles/Gradle_Username_Key` | Gradle repository username property key | `dot_gradle/gradle.properties.tmpl`,<br>`dot_gradle/gradle.encrypted.properties.tmpl` |
| `op://work/dotfiles/Gradle_Password_Key` | Gradle repository password property key | `dot_gradle/gradle.properties.tmpl`,<br>`dot_gradle/gradle.encrypted.properties.tmpl` |
| `op://work/dotfiles/Gradle_Password_Value` | Gradle repository password value | `dot_gradle/gradle.encrypted.properties.tmpl` |
| **GitHub Enterprise Configuration** | | |
| `op://work/dotfiles/github-enterprise-host` | GitHub Enterprise hostname | `private_dot_config/gh/private_hosts.yml.tmpl` |
| `op://work/dotfiles/Github/cloud-account` | Work GitHub cloud account username | `private_dot_config/gh/private_hosts.yml.tmpl` |
| **Multi-File Shared Configuration** | | |
| `op://work/dotfiles/network-id` | Network/LDAP username | `dot_m2/settings.xml.tmpl`,<br>`private_dot_config/gh/private_hosts.yml.tmpl`,<br>`dot_gradle/gradle.properties.tmpl` |
| **Docker Configuration** | | |
| `op://work/dotfiles/docker-credHelpers` | Docker credential helpers configuration | `dot_docker/config.json.tmpl` |
| **AI/API Configuration** | | |
| `op://work/dotfiles/ANTHROPIC_BASE_URL` | Claude AI API base URL | `dot_claude/settings.json.tmpl` |

### SSH Key Management Strategy

To work around SSH server authentication limits (many servers fail authentication after too many key attempts), this setup uses a dedicated `active-ssh-keys` vault in 1Password.

**Key Strategy:**
- **Storage**: All SSH keys are stored in their respective vaults (`private`, `work`, etc.)
- **Active Keys**: Only currently needed SSH keys are copied to the `active-ssh-keys` vault
- **1Password SSH Agent**: Configured to only load keys from the `active-ssh-keys` vault
- **Rotation**: Inactive keys are removed from `active-ssh-keys` to reduce the number of loaded keys

This approach prevents SSH authentication failures that occur when too many keys are loaded in the SSH agent, while maintaining storage of all keys in their organizational vaults.

## Troubleshooting

### 1Password CLI Issues
```bash
# Check 1Password CLI status
op --version

# Sign in if needed
op signin

# Test secret retrieval
op read "op://private/dotfiles/email"
```

### Chezmoi Template Issues
```bash
# Test template processing
chezmoi execute-template < ~/.local/share/chezmoi/dot_gitconfig.tmpl

# Check chezmoi data
chezmoi data
```

### Environment Detection Issues
```bash
# Check detected environment
chezmoi data | grep -E "(isWorkLaptop|isContainerDev)"
```

This setup provides a dotfiles management system that adapts to different environments while storing sensitive data in 1Password.
