## Gitleaks: Secret Scanning & Pre-commit Hook

Gitleaks is a specialized SAST tool used to detect and prevent hardcoded secrets like passwords, API keys, and tokens in git repositories. By using it as a pre-commit hook, you ensure that sensitive data never leaves your local machine.

---

## 🛠 Local Setup (Pre-commit)

To implement filtering at the local level, follow these steps to set up the pre-commit framework.

### 1. Install Gitleaks & Pre-commit

```
# Install Gitleaks 
pip install gitleak

# Install Pre-commit framework
pip install pre-commit
```
### 2. Create Configuration File
```
Create a file named .pre-commit-config.yaml in the root of your sample_repo
repos:
  - repo: https://github.com/gitleaks/gitleaks
    rev: v8.18.2
    hooks:
      - id: gitleaks
```
### 3. Install the Hook
```
Run this command to activate the hook in your local .git directory:
pre-commit install
```
🔍 Usage Manual
- Automated Scanning:  
  Once installed, Gitleaks will run automatically every time you run git commit.
  If a secret is detected, the commit will be blocked.

-  Manual Local Scan:  
   To scan your staged changes manually before committing:
```   gitleaks detect --source . --verbose
```
Full History Scan: To check for leaks in the entire history of the repository:
```
gitleaks detect --source . --report-path leaks-report.json

🛡 Filtering & False Positives
   If Gitleaks flags a string that is not actually a secret, you can ignore it using a .gitleaksignore file.
   Copy the Fingerprint from the Gitleaks output.
   Add it to a .gitleaksignore file in your root directory:

# Ignore false positive in test file
  6dbda8c067d020d674996903f6560946:sample_repo/test.txt:1
```
### 🚀 DevSecOps Pipeline Integration
In addition to local hooks, Gitleaks should run in your devsecops.yaml to ensure a layered defense.
```
yaml
gitleaks_scan:
  stage: test
  image: 
    name: zricethezav/gitleaks:latest
    entrypoint: [""]
  script:
    - gitleaks detect --source . --verbose --redact
Note: The --redact flag ensures that if a secret is found, it is masked in the CI/CD logs to prevent further exposure.
```

📊 Comparison of Security Tools
| Tool     | Layer        | Focus                 |
|----------|--------------|-----------------------|
| Gitleaks | Local/Commit | Secrets & Credentials |
| tfsec    | Static Code  | Terraform Security    |
| Checkov  | Static Code  | General IaC Security  |


## 🔐 Gitleaks Workflow Diagram

```mermaid
flowchart TD
    A[Developer writes code] --> B[Pre-commit Hook: Gitleaks]
    B -->|Secret detected| C[Commit blocked]
    B -->|No secret| D[Commit proceeds]
    D --> E[Push to Remote Repo]
    E --> F[CI/CD Pipeline: Gitleaks Scan]
    F -->|Secret detected| G[Pipeline fails - secret masked (--redact)]
    F -->|No secret| H[Pipeline continues to deploy]
