## layered defense  

#### The best practice is a layered defense (Defense in Depth). You don't rely on just one tool or one location; you catch leaks as early as possible while having a safety net at the end.
Here is the industry-standard setup:
```
 1. Local: The "Inner Loop" (Gitleaks)
       What: Use Gitleaks as a pre-commit hook.
       Why: It is extremely fast. It stops the secret from ever leaving the developer's laptop.
       Best Practice: Don't use TruffleHog as a local hook unless you have a fast machine and stable Go environment, as it can be slow and prone to the Windows file-locking errors you saw earlier.
 
 3. CI/CD: The "Gatekeeper" (TruffleHog)
      What: Run TruffleHog in your GitHub Actions.
      Why: It can verify secrets. If a developer skips the local hook (--no-verify), TruffleHog catches it in the cloud.
      Best Practice: Always use fetch-depth: 0 in CI to scan the entire commit history, not just the latest "diff."
 
 4. Repository: The "Cleanup" (.gitignore)
      What: Maintain a strict .gitignore file.
      Best Practice: Never wait for a scanner to catch a .env file. Block files like .env, *.pem, and creds.json at the Git level so they are never even tracked.
 
 5. Organization: GitHub Secret Scanning
      What: Enable the built-in GitHub Secret Scanning (found in Settings > Code security).
      Why: GitHub partners with service providers (AWS, Stripe, etc.). If you push a key, GitHub automatically notifies the provider to instantly revoke it.

 6. Management: Use a Secret Manager
      What: Move secrets out of the code entirely.
      Best Practice: Instead of DB_PASSWORD="secret", use AWS Secrets Manager, HashiCorp Vault, or GitHub Actions Secrets. Your code should only contain "placeholders" (environment variables).
```
If a secret is ever leaked:
Simply deleting the file or the line is not enough. You must rotate the secret (change the password) immediately because it is now considered compromised in the Git history.
