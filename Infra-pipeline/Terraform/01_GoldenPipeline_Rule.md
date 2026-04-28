## Golden Pipeline
### Industry best practices is to chain below tools to create a layered defense

1. TFLint: Catches logic errors and provider specific typos
2. tfsec/Trivy: Performs rapid, developer-friendly security checks.
3. Checkov: Enforces strict compliance and deep graph analysis, if check fails,the pipeline should automatically block the merge.
4. TruffleHog: Scans for leaked secrets in the Git history.

Note: Expert suggest using --soft-fail flag when first introducing Checkov to an existing project ,This allow you to see violations without breaking every build.
