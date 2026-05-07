# Software Composition Analysis (SCA): Dependency Scanning

## What is Dependency Scanning?
Dependency scanning (or SCA) is the process of identifying security vulnerabilities in the third-party libraries and open-source components that your application depends on. Modern applications are often composed of up to 90% open-source code; securing the "Supply Chain" is critical.

## Why is it part of DevSecOps?
* **Shift Left:** It allows developers to find and fix vulnerabilities during the coding phase rather than discovering them in production.
* **License Compliance:** It helps ensure you aren't using libraries with restrictive licenses (like GPL) that could legally jeopardize your project.
* **Automated Governance:** Pipelines can be configured to "fail the build" if a high or critical vulnerability is detected.

---

## Key Concepts

### 1. The Dependency Tree
Most vulnerabilities don't come from the libraries you call directly (Direct Dependencies), but from the libraries *those* libraries use (**Transitive Dependencies**).

### 2. CVE & CVSS
* **CVE (Common Vulnerabilities and Exposures):** A list of publicly disclosed cybersecurity vulnerabilities.
* **CVSS (Common Vulnerability Scoring System):** A numerical score (0.0 to 10.0) reflecting the severity of a vulnerability.

---

## Tools & Implementation

### Language-Specific Tools
| Language | Tool | Command |
| :--- | :--- | :--- |
| **Node.js** | `npm audit` | `npm audit --audit-level=high` |
| **Python** | `pip-audit` | `pip-audit -r requirements.txt` |
| **Java** | `OWASP Dependency-Check` | `mvn org.owasp:dependency-check-maven:check` |

### Enterprise/SaaS Tools
* **Snyk:** Best-in-class for developer experience and remediation advice.
* **GitHub Dependabot:** Automatically creates Pull Requests to update vulnerable packages.
* **Sonatype Nexus Lifecycle:** Deep integration for artifact management.



---

## Best Practices
1. **Automate in CI:** Run scans on every Pull Request.
2. **Set Thresholds:** Fail builds only on `High` or `Critical` vulnerabilities to avoid "security fatigue."
3. **Update Regularly:** Even if you don't change your code, new vulnerabilities are discovered daily. Schedule weekly scans.
4. **Prune Dependencies:** Remove unused packages to reduce your attack surface.


---
## 🛠 Remediation Steps
Finding a vulnerability is only half the battle. Here is how to handle a detected vulnerability:

1. Upgrade the Package
The most common fix is simply moving to a patched version.
- Node.js: Run npm install [package-name]@latest or npm audit fix.
- Python: Update the version number in requirements.txt and run pip install -r requirements.txt.

2. Check for Breaking Changes
Before upgrading, check the library's changelog. A "Major" version bump (e.g., v1.0.0 to v2.0.0) might require code changes on your end.

3. Use Developer Tools
- Snyk Advisor: Use it to check if a package is healthy before even adding it to your project.
- GitHub Dependabot: If enabled, review the automated Pull Requests it creates. It often includes "Compatibility Scores" to tell you if the update is likely to break your build.

4. The "Vulnerability Patching" Priority
If you have 100+ vulnerabilities, prioritize them using this logic:
- Critical/High CVSS Score: Fix these immediately.
- Reachability: Is the vulnerable function actually being called by your code? (Tools like Snyk can tell you this).
- Exploit Maturity: Is there a "Proof of Concept" (PoC) exploit available in the wild?
