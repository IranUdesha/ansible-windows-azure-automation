# 🤝 Contributing to Ansible Windows Azure Automation

Thank you for your interest in contributing! This document provides guidelines and information for contributors.

## 📋 Table of Contents

- [Code of Conduct](#code-of-conduct)
- [Getting Started](#getting-started)
- [Development Setup](#development-setup)
- [Contributing Guidelines](#contributing-guidelines)
- [Testing](#testing)
- [Documentation](#documentation)
- [Pull Request Process](#pull-request-process)
- [Issue Reporting](#issue-reporting)

## 📜 Code of Conduct

This project adheres to a code of conduct that we expect all contributors to follow:

- **Be respectful** and inclusive in language and actions
- **Be collaborative** and help others learn and grow
- **Be constructive** in feedback and discussions
- **Focus on the project** and its goals
- **Report issues** if you see unacceptable behavior

## 🚀 Getting Started

### Prerequisites

- **Ansible** 2.15 or higher
- **Python** 3.8 or higher
- **Git** for version control
- **Development environment** with Windows Server access (recommended)

### Types of Contributions Welcome

- 🐛 **Bug fixes** - Fix issues and improve reliability
- ✨ **New features** - Add roles, tasks, or functionality
- 📚 **Documentation** - Improve guides, examples, and comments
- 🔧 **Optimization** - Performance improvements and refactoring
- 🛡️ **Security** - Security enhancements and fixes
- 🧪 **Testing** - Add tests and improve coverage

## 🛠️ Development Setup

### 1. Fork and Clone

```bash
# Fork the repository on GitHub, then clone your fork
git clone https://github.com/YOUR_USERNAME/ansible-windows-azure-automation.git
cd ansible-windows-azure-automation

# Add upstream remote
git remote add upstream https://github.com/IranUdesha/ansible-windows-azure-automation.git
```

### 2. Set Up Development Environment

```bash
# Install Python dependencies
pip install -r requirements.txt

# Install Ansible collections
ansible-galaxy collection install -r requirements.yml

# Install development tools
pip install ansible-lint yamllint
```

### 3. Create Development Configuration

```bash
# Copy vault template for testing
cp group_vars/win/vault.yml.template group_vars/win/vault.yml.dev

# Create test inventory
cp inventory/hosts.ini inventory/dev-hosts.ini
```

### 4. Verify Setup

```bash
# Check Ansible installation
ansible --version

# Syntax check
ansible-playbook --syntax-check Az-Win22-Win-playbook.yml

# Lint check
ansible-lint .
```

## 📝 Contributing Guidelines

### Code Style

#### Ansible Best Practices

- Follow [Ansible Best Practices](https://docs.ansible.com/ansible/latest/user_guide/playbooks_best_practices.html)
- Use **descriptive task names** that clearly explain what the task does
- Add **comments** for complex logic or business requirements
- Use **meaningful variable names** that are self-documenting

#### YAML Formatting

```yaml
# Good: Clear, descriptive task name
- name: Install IIS Web Server with management tools
  win_feature:
    name: Web-Server
    state: present
    include_management_tools: true

# Good: Well-structured with comments
- name: Configure IIS security settings
  win_shell: |
    # Enable HTTPS and disable weak protocols
    Import-Module WebAdministration
    Set-WebConfigurationProperty -Filter "system.webServer/security/access" -Name "sslFlags" -Value "Ssl,SslNegotiateCert"
  register: iis_security_result
```

#### Variable Naming

```yaml
# Good: Descriptive and consistent
azure_storage_account_name: "mystorageaccount"
windows_admin_username: "administrator"
ssl_certificate_thumbprint: "{{ vault_ssl_cert_thumbprint }}"

# Avoid: Unclear or inconsistent
acc_name: "mystorageaccount"
usr: "administrator"
cert: "{{ ssl_cert }}"
```

### File Organization

#### Role Structure

```
roles/
└── role_name/
    ├── tasks/
    │   ├── main.yml          # Main entry point
    │   └── component.yml     # Specific component tasks
    ├── handlers/
    │   └── main.yml          # Event handlers
    ├── vars/
    │   └── main.yml          # Role variables
    ├── defaults/
    │   └── main.yml          # Default variables
    └── README.md             # Role documentation
```

#### Task Files

- **Keep tasks focused** - one logical function per file
- **Use imports/includes** appropriately for reusability
- **Group related tasks** in logical files
- **Document dependencies** between tasks

### Security Guidelines

#### Sensitive Data

```yaml
# Good: Use vault variables
azure_client_secret: "{{ vault_azure_client_secret }}"
database_password: "{{ vault_db_password }}"

# Never: Hardcode secrets
azure_client_secret: "actual-secret-value"
database_password: "hardcoded-password"
```

#### Variable Protection

- **Always use Ansible Vault** for sensitive data
- **Use descriptive vault variable names** with `vault_` prefix
- **Test with fake data** in development
- **Document required vault variables** in README

## 🧪 Testing

### Testing Requirements

All contributions must include appropriate testing:

#### 1. Syntax Testing

```bash
# Required: All playbooks must pass syntax check
ansible-playbook --syntax-check playbook.yml
```

#### 2. Linting

```bash
# Required: All code must pass ansible-lint
ansible-lint .

# YAML formatting check
yamllint .
```

#### 3. Functional Testing

```bash
# Test in development environment
ansible-playbook -i inventory/dev-hosts.ini playbook.yml \
  -e "server_type=web" --check

# Test with different server types
for server_type in web job db; do
  ansible-playbook -i inventory/dev-hosts.ini playbook.yml \
    -e "server_type=$server_type" --check
done
```

#### 4. Integration Testing

- **Test Azure integration** with development Azure resources
- **Verify Windows compatibility** across different OS versions
- **Test role combinations** and dependencies
- **Validate error handling** and recovery scenarios

### Test Environment Setup

#### Development Azure Resources

Create dedicated Azure resources for testing:

```bash
# Example: Create test resource group
az group create --name "ansible-dev-rg" --location "East US"

# Create test storage account
az storage account create \
  --name "ansibledevstorage" \
  --resource-group "ansible-dev-rg" \
  --location "East US" \
  --sku "Standard_LRS"

# Create test Key Vault
az keyvault create \
  --name "ansible-dev-kv" \
  --resource-group "ansible-dev-rg" \
  --location "East US"
```

## 📚 Documentation

### Documentation Standards

#### README Updates

- **Update README.md** for significant feature changes
- **Add usage examples** for new functionality
- **Update requirements** if new dependencies are added
- **Maintain table of contents** and links

#### Code Documentation

```yaml
# Good: Well-documented task
- name: Download SSL certificate from Azure Key Vault
  azure.azcollection.azure_rm_keyvaultsecret:
    secret_name: "{{ ssl_certificate_name }}"
    keyvault_uri: "{{ key_vault_uri }}"
    client_id: "{{ azure_client_id }}"
    secret: "{{ azure_client_secret }}"
    tenant: "{{ azure_tenant_id }}"
  register: keyvault_secret
  delegate_to: localhost
  # This task retrieves the SSL certificate from Azure Key Vault
  # and registers it for later use in certificate installation
```

#### Role Documentation

Each role should include:

- **Purpose and scope** of the role
- **Required variables** and their descriptions
- **Optional variables** and default values
- **Dependencies** on other roles or external services
- **Usage examples** with different configurations

## 🔄 Pull Request Process

### Before Submitting

1. **Sync with upstream**
   ```bash
   git fetch upstream
   git checkout main
   git merge upstream/main
   ```

2. **Create feature branch**
   ```bash
   git checkout -b feature/your-feature-name
   ```

3. **Make changes and test**
   ```bash
   # Make your changes
   
   # Test locally
   ansible-lint .
   ansible-playbook --syntax-check playbook.yml
   ```

4. **Commit with clear messages**
   ```bash
   git add .
   git commit -m "Add: feature description
   
   - Detailed change 1
   - Detailed change 2
   - Fixes #issue-number"
   ```

### PR Checklist

- [ ] **Tests pass** - All automated tests pass
- [ ] **Documentation updated** - README and relevant docs updated
- [ ] **Security review** - No hardcoded secrets or vulnerabilities
- [ ] **Backwards compatibility** - Changes don't break existing functionality
- [ ] **Clear description** - PR includes clear description of changes

### PR Description Template

```markdown
## 📋 Description
Brief description of changes and motivation.

## 🔄 Type of Change
- [ ] Bug fix (non-breaking change)
- [ ] New feature (non-breaking change)
- [ ] Breaking change (fix or feature causing existing functionality to break)
- [ ] Documentation update

## 🧪 Testing
- [ ] Syntax check passed
- [ ] Ansible-lint passed
- [ ] Tested in development environment
- [ ] Tested with multiple server types

## 📝 Checklist
- [ ] Code follows project style guidelines
- [ ] Self-review completed
- [ ] Documentation updated
- [ ] No hardcoded secrets
- [ ] Tests added for new functionality
```

## 🐛 Issue Reporting

### Bug Reports

Use the following template for bug reports:

```markdown
## 🐛 Bug Description
Clear description of the bug.

## 🔄 Steps to Reproduce
1. Step one
2. Step two
3. Step three

## 💡 Expected Behavior
What should happen.

## 🚨 Actual Behavior
What actually happens.

## 🖥️ Environment
- Ansible version:
- Python version:
- Target OS:
- Azure region:

## 📋 Additional Context
Any additional information, logs, or screenshots.
```

### Feature Requests

```markdown
## ✨ Feature Description
Clear description of the proposed feature.

## 💡 Use Case
Why is this feature needed? What problem does it solve?

## 📝 Proposed Solution
How should this feature work?

## 🔄 Alternatives Considered
What alternatives have you considered?

## 📋 Additional Context
Any additional information or examples.
```

## 🙏 Recognition

Contributors will be recognized in:

- **README.md** acknowledgments section
- **CHANGELOG.md** for significant contributions
- **GitHub releases** notes
- **Project documentation** where appropriate

## 📞 Getting Help

- **💬 GitHub Discussions** - For general questions and community help
- **🐛 GitHub Issues** - For bug reports and feature requests
- **📧 Direct Contact** - Reach out to [@IranUdesha](https://github.com/IranUdesha) for urgent matters

Thank you for contributing to make Windows infrastructure automation better! 🚀