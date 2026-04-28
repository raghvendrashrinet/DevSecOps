## TruffleHog: Enterprise Secret Verification
TruffleHog is the "intelligent" layer of our secret scanning strategy. While Gitleaks stops secrets at the developer's laptop, TruffleHog acts as our automated security gate in the CI/CD pipeline.

### 🛡 Why TruffleHog? 
TruffleHog is superior for pipeline enforcement because of Verification:
- Verified Findings: It doesn't just guess; it checks with the provider (AWS, Slack, GitHub) to see if the secret is active.
- Deep Scanning: It digs through the entire Git history, including deleted branches and commit metadata.
- Low Noise: By focusing on "Verified" secrets, we prevent the pipeline from failing on fake or test keys.

### 🛠 Setup & Installation
#### 1. Local Installation (For Security Audits)
While we use Gitleaks for pre-commit, you should use TruffleHog locally to audit your full history before pushing.
```

pip install trufflehog

# Docker (Recommended for consistency)
docker pull trufflesecurity/trufflehog:latest
```
#### 2. Scanning Local History
To scan your local repository and verify findings against live APIs:
```
trufflehog git file://. --only-verified
```
### 🚀 GitHub Actions Integration (Industry Standard)
This is how we enforce security at the Pull Request level. Add this to your .github/workflows/security.yml:
``` YAML
name: Secret Verification
on: [pull_request]

jobs:
  trufflehog:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
        with:
          fetch-depth: 0 # Scans the full history of the PR branch

      - name: TruffleHog OSS
        uses: trufflesecurity/trufflehog@main
        with:
          path: ./
          base: ${{ github.event.pull_request.base.sha }}
          head: ${{ github.event.pull_request.head.sha }}
          extra_args: --only-verified
```
### 🚨 Incident Response: Remediation 
If TruffleHog finds a Verified secret, the build will fail. Simply deleting the secret from the code is not enough.  
Step 1: Invalidate/Rotate  
  Immediately deactivate the key in the provider's console (e.g., AWS IAM, GitHub Settings). Assume the key is stolen.

Step 2: Clean Git History  
  Use git-filter-repo or BFG Repo-Cleaner to scrub the secret from every commit in the history.
  ```
# Example using BFG to remove a file containing secrets
bfg --delete-files credentials.txt
```

Step 3: Verify Fix
Run a fresh scan to ensure no traces remain:
```
trufflehog git file://. --only-verified
```

| Feature   | Gitleaks (Local)   | TruffleHog (Pipeline) |
|-----------|--------------------|-----------------------|
| Placement | Pre-commit Hook    | PR / CI/CD Gate       |
| Logic     | Fast Regex Matching| API-based Verification|
| Goal      | Developer feedback | Zero-trust enforcement|
