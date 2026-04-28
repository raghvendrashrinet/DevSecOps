TruffleHog
Problem
Committing sensitive information to version control is a major security risk. Even if you delete the file later, the secret remains in the Git history forever.

Leaked AWS Access Keys

Database Passwords

GitHub Personal Access Tokens (PATs)

What Is TruffleHog?
TruffleHog is a scanner that digs through your entire Git history, branches, and commits to find secrets using high-entropy strings and regular expression matching.

Step 1: Install TruffleHog
You can install it via Docker (recommended) or as a binary:

Using Docker:

Bash
docker pull trufflesecurity/trufflehog:latest
Using Homebrew (macOS/Linux):

Bash
brew install trufflehog
Step 2: Create a Test "Secret"
Warning: Never use real credentials for testing.

Create a dummy file with a fake secret:

Bash
echo "export AWS_SECRET_ACCESS_KEY=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY" > credentials.txt
git add credentials.txt
git commit -m "Add credentials file"
Step 3: Scan the Repository
Run the scan on your local directory:

Bash
trufflehog git file://. --only-verified
Note: --only-verified attempts to check if the found secret is actually active (for supported providers).

What You Should See
❌ Found unverified secret - AWS Secret Access Key
❌ Found in commit: e889bab...

TruffleHog will flag the exact line and the commit hash where the secret lives.

Step 4: Fix the Issue
Rotate the Secret: Even if you remove it from Git, assume it is compromised. Change the password/key immediately.

Remove from History: Use tools like bfg or git-filter-repo to scrub the secret from your entire Git history.

Add to .gitignore: Ensure credential files are never tracked.

Key Takeaway
Checkov prevents bad settings; TruffleHog prevents bad leaks. Always run TruffleHog as a pre-commit hook or in your CI pipeline to catch secrets before they hit the server.
