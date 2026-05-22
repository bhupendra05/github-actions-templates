# github-actions-templates

> Copy-paste GitHub Actions workflows — Python CI, Node CI, Docker build+push (multi-arch), deploy to Fly.io, semantic release, CodeQL security scan.

![GitHub Actions](https://img.shields.io/badge/github_actions-2088FF?logo=github-actions&logoColor=white) ![License](https://img.shields.io/badge/license-MIT-green)

## Workflows

| File | What it does |
|------|-------------|
| `python-ci.yml` | Test matrix (3.11, 3.12), ruff lint, mypy, pytest+coverage, Bandit |
| `node-ci.yml` | Test matrix (Node 20, 22), lint, typecheck, build, npm audit |
| `docker-build-push.yml` | Multi-arch build (amd64+arm64), push to GHCR, Trivy scan, SBOM |
| `deploy-fly.yml` | Deploy to Fly.io, health check, Slack alert on failure |
| `semantic-release.yml` | Auto-version from commit messages, GitHub release, PyPI publish |
| `security-scan.yml` | CodeQL, dependency review, TruffleHog secret scanning |

## Quick Start

Copy any workflow file into your repo's `.github/workflows/` directory.

```bash
git clone https://github.com/bhupendra05/github-actions-templates.git

# Copy a workflow to your project
cp github-actions-templates/.github/workflows/python-ci.yml \
   your-project/.github/workflows/

git add .github/workflows/python-ci.yml
git commit -m "ci: add Python CI workflow"
git push
```

## Workflow Details

### Python CI

```yaml
# Triggers on push/PR to main
# Matrix: Python 3.11, 3.12
# Steps: pip cache → ruff lint → mypy → pytest+coverage → codecov → bandit
```

**Required:** `requirements.txt` with your deps. Add `pytest`, `ruff`, `mypy` as dev deps.

### Docker Build & Push (Multi-arch)

```yaml
# Builds linux/amd64 + linux/arm64 (Apple Silicon compatible)
# Pushes to ghcr.io on main push or tag
# Cache via GitHub Actions cache (fast rebuilds)
# Trivy CVE scan + SARIF upload to Security tab
```

**Required:** `Dockerfile` at repo root. No secrets needed — uses `GITHUB_TOKEN`.

### Deploy to Fly.io

```yaml
# Deploys on push to main
# Waits for /health endpoint to return 200
# Sends Slack alert on failure
```

**Required secrets:** `FLY_API_TOKEN`, optionally `SLACK_BOT_TOKEN`.

### Semantic Release

Automatically versions and releases based on [Conventional Commits](https://www.conventionalcommits.org/):

| Commit prefix | Release type |
|--------------|-------------|
| `feat: ...` | Minor (1.x.0) |
| `fix: ...` | Patch (1.0.x) |
| `feat!: ...` or `BREAKING CHANGE` | Major (x.0.0) |

**Required:** `release.config.js` or `.releaserc`. **Required secrets:** `RELEASE_TOKEN` (PAT with write access), `NPM_TOKEN` (if publishing to npm).

### Security Scan

- **CodeQL** — SAST for Python and JavaScript (runs on push + weekly)
- **Dependency Review** — blocks PRs that add high-severity vulnerabilities
- **TruffleHog** — scans git history for accidentally committed secrets

**No configuration required** — works out of the box for any repo.

## Required Secrets

| Secret | Used by |
|--------|---------|
| `FLY_API_TOKEN` | `deploy-fly.yml` |
| `SLACK_BOT_TOKEN` | `deploy-fly.yml` (optional) |
| `RELEASE_TOKEN` | `semantic-release.yml` |
| `NPM_TOKEN` | `semantic-release.yml` (optional) |
| `PYPI_API_TOKEN` | `semantic-release.yml` (optional) |

> `GITHUB_TOKEN` is automatic — no setup needed.

## License

MIT © bhupendra05
