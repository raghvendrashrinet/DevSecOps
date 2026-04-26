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
