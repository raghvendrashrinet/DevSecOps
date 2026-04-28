## tfsec: Static Analysis for Terraform Security
tfsec is a static analysis security scanner for your Terraform code. It runs locally and in the pipeline to catch security misconfigurations before your infrastructure is even provisioned in Azure.

## 🛡️ Why tfsec? (The Manager's View)
tfsec is the industry standard for "Developer-First" security. We use it in our Golden Pipeline because:

- Speed: It is written in Go and scans hundreds of files in milliseconds.
- Clarity: Each finding includes a link to a "Remediation Page" explaining how to fix the code.
- Azure Specific: It has hundreds of built-in checks for Azure (e.g., detecting if a SQL Database has auditing disabled or if a Key Vault is publicly accessible).
- Ease of Use: It requires zero configuration to start; it knows what a "bad" Azure setup looks like out of the box.

## 🛠️ Setup & Installation
#### 1. Install tfsec
```
# MacOS
brew install tfsec

# Windows (Chocolatey)
choco install tfsec

# Linux (Binary)
curl -s https://raw.githubusercontent.com/aquasecurity/tfsec/master/scripts/install_linux.sh | bash
```
#### 2. Basic Usage
To scan your entire Azure Terraform directory:
```
tfsec .
```
### 🔍 Key Azure Security Checks
tfsec will automatically flag the following common Azure mistakes:
AZU001: Storage accounts allowing unencrypted (HTTP) traffic.  
AZU017: Azure Network Security Groups (NSG) allowing SSH (22) or RDP (3389) from the internet.
AZU023: Key Vaults without "Soft Delete" enabled.
AZU026: Managed Disks not using Customer Managed Keys (CMK) for encryption.

### 🚀 Golden Pipeline Integration
In our Golden Pipeline, tfsec acts as the "Fast Fail" gate. If tfsec finds a critical security hole, we stop the build before moving to the more intensive Checkov scans.

```
# GitHub Action Step
- name: Run tfsec
  uses: aquasecurity/tfsec-action@v1.0.0
  with:
    working_directory: ./Terraform
    soft_fail: false # Set to true only during initial setup
```
### 🚦 Handling Exceptions (Overrides)
Sometimes, a security risk is "accepted" for a specific reason (e.g., a public web server). You can ignore a specific check by adding a comment directly to the Terraform resource:
```
resource "azurerm_network_security_rule" "example" {
  # tfsec:ignore:azure-network-no-public-ingress
  name                        = "allow_ssh"
  priority                    = 100
  direction                   = "Inbound"
  access                      = "Allow"
  protocol                    = "Tcp"
  source_port_range           = "*"
  destination_port_range      = "22"
  source_address_prefix       = "*" # Public SSH allowed for demo purposes
}
```
### 📊 tfsec vs. Checkov
Feature,tfsec,Checkov
Primary Strength,Developer Speed & Ease,Compliance & Graph Analysis
Format,Binary (Go),Python-based
Custom Rules,Simple (Rego/YAML),Advanced (Python/YAML)

#### The "Fail Fast" Rule: Encourage developers to run tfsec locally. It is much faster to fix an open NSG rule on your own machine than to wait for the CI/CD pipeline to tell you your Pull Request is insecure.
Best Use Case,Immediate feedback in PRs,Deep audit of complex infra
