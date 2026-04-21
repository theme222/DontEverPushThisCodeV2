# DontEverPushThisCodeV2

> Intentionally vulnerable Next.js app — workshop asset for **Software Engineering (2110423) 2025/2**

**WARNING: Never deploy this to production. Ever.**

---

## What This Is

Deliberately broken codebase used to teach DevSecOps pipeline construction.  
Students build each security gate from scratch using `TODO#` markers in `.github/workflows/helpme.yaml`.

---

## Workshop Overview

| Session | Gate | Tool | Finds |
|---------|------|------|-------|
| 0 | Setup | GitHub Actions | Pipeline wiring, permissions |
| 1 | GATE#0 | TruffleHog + Gitleaks | Secrets in code/history |
| 2 | GATE#1a | SonarQube | SAST → Sonar dashboard |
| 3 | GATE#1b | CodeQL | SAST → GitHub Security tab |
| 4 | GATE#2 | OWASP Dependency-Check | CVEs in npm deps |
| 5 | GATE#3 | Trivy | CVEs in Docker image |

---

## Quick Start (Local)

```bash
docker build -t highly-exploitable-site .
docker run --rm -p 8080:3000 highly-exploitable-site
# visit http://localhost:8080
```
 
---

## Workshop Tasks — TODO Guide

All tasks live in `.github/workflows/helpme.yaml`.  
Fill each `TODO#` in order. Push to `main` to trigger the pipeline. Check results in:
- **Actions tab** → job logs
- **Security tab** → SARIF findings (CodeQL, Gitleaks, Depcheck, Trivy)
- **SonarCloud dashboard** → Sonar findings

---

### Permissions (TODO#0, 0a, 0b, 0c)

Fix the `permissions:` block so all scanners can upload results and comment on PRs.

**Q:** Why scope permissions per-workflow instead of repo-wide?

---

### GATE#0 — Secret Scanning

**TODO#1** — Why do secret scanners need full git history? Set the correct `fetch-depth`.

**TODO#2** — Configure TruffleHog to scan only the commit diff, not the whole repo every time.

**TODO#3** — Add a flag so TruffleHog reports only secrets it can verify as live credentials.

**TODO#4** — Add a complete Gitleaks step with SARIF upload and job summary enabled.  
**Q:** TruffleHog verifies live creds. Gitleaks matches regex patterns. Which has more false positives? Which misses more?

---

### GATE#1a — SonarQube SAST

**TODO#5** — Sonar uses git blame for new-code detection. What `fetch-depth` does it need?

**TODO#6** — Add the SonarQube scan step. Needs a repo secret. Set `continue-on-error` so later gates still run.  
**Q:** Findings appear in SonarCloud dashboard, NOT the GitHub Security tab. Why?

---

### GATE#1b — CodeQL SAST

**TODO#7** — Set the correct CodeQL language for a Next.js/React project.

**TODO#8** — Enable extended security + quality query suites.  
Docs: https://docs.github.com/en/code-security/code-scanning/creating-an-advanced-setup-for-code-scanning/codeql-code-scanning-for-compiled-languages#codeql-query-suites  
**Q:** Why use both suites? What's the difference between `security-extended` and `security-and-quality`?

**TODO#9** — Add the analyze step. Set `category` to avoid collisions when scanning multiple languages.  
**Q:** What breaks if two matrix languages share the same category?

---

### GATE#2 — Dependency Scanning

**TODO#10** — Set up Node 20 so the scanner can resolve the full dependency tree.

**TODO#11** — Install npm dependencies before running the scan.

**TODO#12** — Choose the output format that integrates with GitHub Security tab.

**TODO#13** — Set a CVSS fail threshold. What score separates MEDIUM from HIGH?  
CVSS calculator: https://www.first.org/cvss/calculator/3.1

**TODO#14** — Upload the SARIF report to GitHub Security tab.

**TODO#15** — Upload the raw report folder as a downloadable workflow artifact.

---

### GATE#3 — Container Scanning

**TODO#16** — Build the Docker image before scanning it.

**TODO#17** — Set the output format for Security tab integration.

**TODO#18** — Choose which severity levels to report.  
Trivy severity docs: https://aquasecurity.github.io/trivy/latest/docs/configuration/filtering/#by-severity  
**Q:** Should LOW/MEDIUM block a build? Why or why not?

**TODO#19** — Decide whether to report CVEs that have no available fix yet.

**TODO#20** — Upload the SARIF to the GitHub Security tab.

**TODO#21** — Upload the raw SARIF as a downloadable artifact.

---

## Where to See Results

| Result type | Location |
|-------------|----------|
| Secrets (Gitleaks) | Security tab → Code scanning |
| Secrets (TruffleHog) | Actions tab → job log |
| SAST (CodeQL) | Security tab → Code scanning |
| SAST (SonarQube) | https://sonarcloud.io → your project |
| SCA (Depcheck) | Security tab + Actions → Artifacts |
| Container (Trivy) | Security tab + Actions → Artifacts |

---

## Secrets Required

Add in repo → Settings → Secrets and variables → Actions:

| Secret | Where to get |
|--------|-------------|
| `SONAR_TOKEN` | https://sonarcloud.io → My Account → Security |
| `GITHUB_TOKEN` | Auto-provided by GitHub Actions — no setup needed |

---

## Security Notice

Intentionally vulnerable. Contains XSS, SQL injection, hardcoded secrets, and outdated dependencies.  
This is not a real website. One must not (ever) deploy this in the production server
