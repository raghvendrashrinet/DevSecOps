While Checkov and tfsec focus on security, TFLint focuses on code quality and cloud-specific error

TFLintProblemTerraform's built-in terraform validate only checks for basic syntax. It won't catch:Invalid EC2 instance types (e.g., t2.microo).Naming convention violations.Cloud-specific errors before you run terraform plan.Unused declarations (variables or locals).What Is TFLint?TFLint is a Terraform linter that finds errors that the official compiler misses. It is pluggable, meaning it has specific rulesets for AWS, Azure, and GCP.Step 1: Install TFLintUsing Homebrew (macOS/Linux):Bashbrew install tflint
Using Chocolatey (Windows):Bashchoco install tflint
Using Docker:Bashdocker pull ghcr.io/terraform-linters/tflint
Step 2: Initialize TFLintTFLint requires a configuration file named .tflint.hcl in your root directory to enable cloud-specific rules.Create .tflint.hcl:Terraformconfig {
  module = true
  force = false
}

plugin "aws" {
    enabled = true
    version = "0.28.0"
    source  = "github.com/terraform-linters/tflint-ruleset-aws"
}
Then, run the init command to install the plugin:Bashtflint --init
Step 3: Run a ScanRun the linter on your current directory:Bashtflint
Step 4: Example Error DetectionIf you have a resource with an invalid instance type:Terraformresource "aws_instance" "web" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.microo" # Typo: extra 'o'
}
TFLint Output:PlaintextError: "t2.microo" is an invalid value as instance_type (aws_instance_invalid_type)
Best PracticesUse with Plugins: Always enable the plugin for your specific cloud provider (AWS/Azure/GCP).Pre-commit Hook: Integrate TFLint so your code is "clean" before it ever reaches a human reviewer.Deep Checking: Use TFLint to enforce team standards, such as mandatory tags on all resources.The Terraform Security Stack SummaryToolWhat it catchesCheckovCompliance & Security PoliciestfsecVulnerabilities & Best PracticesTFLintCloud-specific errors & Code QualityTruffleHogSecrets & Credentials in code
