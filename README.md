# DevSecOps Knowledge Base

This repository is a **documentation hub** for DevSecOps practices.  
It contains **knowledge docs** and **sample repositories** to demonstrate secure CI/CD concepts.  
No production-ready pipeline code is included — samples are illustrative only.

## 📂 Repository Structure

- **infra-pipeline/**
  - Documentation for infrastructure automation pipelines.
  - `terraform/` → Notes on Infrastructure-as-Code (IaC) security, scanning, and compliance.
  - Includes a `sample-repo/` to demonstrate secure Terraform practices.

- **app-pipeline/**
  - Documentation for application delivery pipelines.
  - Organized by language/runtime:
    - `npm/` → Node.js/JavaScript practices + sample repo
    - `python/` → Python practices + sample repo
    - `shared/` → Common DevSecOps principles (SAST, DAST, dependency scanning, secrets management)


## 🎯 Purpose

- Provide language-specific DevSecOps knowledge for application pipelines.
- Document best practices for infrastructure pipelines.
- Demonstrate concepts with **sample repos** for hands-on learning.
- Serve as a **reference KB** for teams adopting DevSecOps.

## 🚀 Usage

- Browse the folders to find language-specific or infrastructure-related notes.
- Explore `sample-repo/` directories for example implementations.
- Use the `shared/` folder for principles that apply across all pipelines.

##  Navigation
```
DevSecOps/
│
├── Infra-pipeline/
│   └── Terraform/
│       ├── 01.GoldenPipeline_Rule.md  
│       ├── Checkov.md                 
│       ├── tfsec.md                   
│       ├── OIDC_Secretless_Auth.md    
│       └── devsecops.yaml             
│
├── App-pipeline/
│   ├── npm/
│   ├── python/
│   └── shared/                        <-- New Modular Structure
│       ├── DAST/                      (Dynamic Analysis - ZAP/Burp)
│       ├── SAST/                      (Static Analysis - Sonar/CodeQL)
│       └── SCA/                       (Dependency Scanning & Remediation)
│
├── 01.Git.md                          
├── gitleaks-trufflehog.md             
├── layered defense.md                 
└── README.md
```
