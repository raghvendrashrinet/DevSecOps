Many security teams use both in a layered approach: 
1. Gitleaks locally: Use it as your pre-commit hook for instant, sub-second blocking of obvious patterns.
2. TruffleHog in CI: Use it as a GitHub Action or CI step to do a deeper dive and verify if any leaked credentials are active

How to add TruffleHog to your config
If you want to try it out, you can add it to your existing .pre-commit-config.yaml alongside Gitleaks:

.pre-commit-config.yaml 
```
repos:
  - repo: https://github.com/gitleaks/gitleaks
    rev: v8.24.2
    hooks:
      - id: gitleaks
  - repo: https://github.com/trufflesecurity/trufflehog
    rev: v3.88.12
    hooks:
      - id: trufflehog
        name: TruffleHog
        description: Detect secrets in your data with TruffleHog.
        entry: trufflehog git file://. --since-commit HEAD --only-verified --fail
        language: golang
```
### For gitleak CI Go to  -- > [01.Git.md ](01.Git.md)
### Secret Scan in CI Workflow with Trufflehog
[TruffleHog](https://docs.trufflesecurity.com/pre-commit-hooks)   

 --> .github/workflows/secret-scan.yml
```  
name: Secret Scanning
on:
  push:
    branches: [main]
  pull_request:

jobs:
  trufflehog:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
        with:
          fetch-depth: 0 # Essential: scans full history, not just the latest commit

      - name: TruffleHog OSS
        uses: trufflesecurity/trufflehog@main
        with:
          path: ./
          base: ${{ github.event.repository.default_branch }}
          head: HEAD
          extra_args: --only-verified --fail
```

Why this configuration is effective:  
 - fetch-depth: 0: By default, GitHub Actions only downloads the latest commit. Setting this to 0 ensures TruffleHog scans the entire Git history.
 - --only-verified: This tells TruffleHog to only fail the build if it finds a secret it can verify as active (e.g., a real AWS key), reducing annoying false alarms.
 - --fail: This ensures the CI pipeline stops and turns red if a secret is found, preventing the code from being merged or deployed

### Commit and Push
Once you save the file, commit it and push to GitHub:
```
 git add .github/workflows/secret-scan.yml
 git commit -m "Add TruffleHog CI scanning"
 git push origin main

