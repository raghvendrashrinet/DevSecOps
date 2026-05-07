# OIDC (OpenID Connect): Secretless Authentication

## What is OIDC?
OpenID Connect (OIDC) allows your CI/CD pipelines (like GitHub Actions or Azure DevOps) to access resources in your Cloud Provider (Azure, AWS, GCP) without needing to store long-lived secrets or passwords in your repository settings.

## Why Use OIDC in DevSecOps?
* **No Secret Rotation:** Since there are no static passwords, you never have to rotate them.
* **Short-Lived Tokens:** Access is granted via a temporary token that expires automatically after the job finishes.
* **Workload Identity:** You grant access to a specific *repository* and *branch*, rather than a broad service account.
* **Minimized Risk:** If your CI/CD environment is compromised, there are no permanent credentials for an attacker to steal.

---

## How It Works
1. **Request:** The CI/CD runner requests an OIDC token from the provider (e.g., GitHub).
2. **Exchange:** The runner presents this token to the Cloud Provider (e.g., Azure).
3. **Validate:** Azure verifies that the token came from the correct repo/branch.
4. **Access:** Azure issues a temporary access token for the Terraform run.

---

## Azure & GitHub Actions Example

### 1. Azure Setup (Infrastructure Side)
You must create a **Federated Identity Credential** on your Azure Service Principal:
* **Issuer:** `https://token.actions.githubusercontent.com`
* **Subject Identifier:** `repo:<ORG_NAME>/<REPO_NAME>:ref:refs/heads/main`

### 2. GitHub Actions Configuration (Pipeline Side)
Add `permissions` to your workflow YAML to allow it to request the OIDC token:

```yaml
permissions:
  id-token: write # Required for requesting the JWT
  contents: read  # Required for actions/checkout
```
### 3. Terraform Provider Config
In your main.tf, you don't need a client secret anymore:
```yaml
provider "azurerm" {
  features {}
  use_oidc = true
}
```

### Best Practices
-- Narrow the Scope: Only allow the main branch to use OIDC for production environments.
-- Audit Logs: Regularly check Cloud provider logs to see which OIDC tokens were issued.
-- Remove Old Secrets: Once OIDC is verified, delete any old ARM_CLIENT_SECRET or AWS_SECRET_ACCESS_KEY from your repository secrets immediately.
