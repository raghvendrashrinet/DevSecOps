## TFLint: The Infrastructure Linter
TFLint is a Terraform linter focused on checking for errors that are specific to cloud providers.Code quality check
While terraform validate checks if your syntax is correct, TFLint checks if your logic is valid for the specific cloud (e.g., AWS, Azure, GCP)

## 🛡️ Why TFLint? (The Manager's View)
TFLint acts as the first line of defense in our Golden Pipeline by catching "expensive" mistakes before they reach the plan stage:
- Cloud Provider Validation: It catches invalid instance types (e.g., trying to use a t2.micro in a region where it doesn't exist).
- Enforcing Best Practices: It flags issues like missing mandatory tags or naming convention violations.-
- Reducing Pipeline Waste: It catches errors locally so developers don't have to wait 5 minutes for a CI/CD  pipeline to fail.

## 🛠️ Setup & Installation
1. Install TFLint
```
pip install tflint

# Windows (Chocolatey)
choco install tflint
```
2. Configure the Azure Plugin
To audit Azure resources, you must initialize the Azurerm ruleset. Create a .tflint.hcl file in your repository root:
```
config {
  module = true
  force  = false
}

plugin "azurerm" {
    enabled = true
    version = "0.27.0"
    source  = "github.com/terraform-linters/tflint-ruleset-azurerm"
}
```
Run the initialization:
```
tflint --init
```
###  🔍 Usage Manual
#### Local Scanning
Run this command before every commit to ensure your cloud resources are valid:
```
tflint --recursive
```
#### Integration with Pre-commit
Add this to your .pre-commit-config.yaml to automate the check:
```
- repo: https://github.com/terraform-linters/tflint
  rev: v0.50.0
  hooks:
    - id: tflint
      args: ["--config=.tflint.hcl"]
```
### 🚀 Golden Pipeline Integration
In our GoldenPipeline, TFLint runs immediately after terraform fmt. It serves as a Quality Gate.
``` YAML
# Example Step for Azure Pipeline
- script: |
    tflint --init
    tflint --recursive --check
  displayName: 'Linter: TFLint Azure Scan'
```
[!IMPORTANT]
If TFLint finds an error, the pipeline will stop. This ensures that only "deployable" code is passed to the security scanners (tfsec/Checkov).
Feature,terraform validate,TFLint (Azure Ruleset)
Syntax Check,✅ Yes,✅ Yes
Azure VM SKUs,❌ No,✅ Yes
Storage Naming Rules,❌ No,✅ Yes
Deprecated Attributes,⚠️ Partial,✅ Yes

By enforcing the Azurerm ruleset, we reduce the number of "Trial and Error" deployments.
