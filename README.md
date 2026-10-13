# `tfkit` — Unified DevUX, Fleet Governance & SRE ConOps CLI

`tfkit` is a high-performance, zero-dependency, single-binary Go CLI that consolidates fleet management, signed release invariant verification, SRE incident triage/janitor operations, and quality gates across Terraform modules and infrastructure repositories.

---

## Installation

### Via `go install` (Recommended)
```bash
go install github.com/hatan4ik/tfkit/cmd/tfkit@latest
```

### From Source
```bash
git clone https://github.com/hatan4ik/tfkit.git
cd tfkit
make check
make install
```

---

## Architectural Principles

1. **Zero External Dependencies**: Relies exclusively on Go 1.23+ standard library for instantaneous compilation, zero CVEs, and minimal binary size.
2. **Sub-Second Fleet Execution**: Parallelized worker pools with bounded concurrency across multi-repository workspaces.
3. **Deterministic 12-Factor Exit Codes**: Standardized exit codes (`0` success, `1` error, `2` usage, `3` policy/drift violation) across all subcommands.
4. **Structured Output**: Native support for human-readable tables and machine-parseable JSON (`--output json`).

---

## Commands

### 1. Module Quality Gates & Developer Experience
Replaces repetitive bash loops in module Makefiles with unified, high-performance Go subcommands:

```bash
# Discover module roots, submodules, examples, and test fixtures
tfkit module dirs --dir .

# Full quality gate pipeline (fmt -> validate -> lint -> test -> docs-check -> security)
tfkit module check --dir .

# Individual quality workflows
tfkit module fmt --fix
tfkit module validate
tfkit module lint
tfkit module test
tfkit module docs
tfkit module lock --platform linux_amd64,darwin_arm64
```

### 2. Fleet Git Audit & Synchronization
Concurrently audits or synchronizes multiple local module repositories:

```bash
# Audit local module repositories (table or JSON)
tfkit fleet status --dir ~
tfkit fleet status --dir ~ --output json

# Fetch remotes, switch clean feature checkouts to main, fast-forward main, and prune gone branches
tfkit fleet sync --dir ~
```

### 3. Fleet Release & Signed-Tag Invariant Verification
Concurrently verifies all 6 release invariants (default branch SHA parity, annotated tag, GitHub-verified signature, latest published release) across all module repositories:

```bash
tfkit fleet verify-releases --owner hatan4ik
```

### 4. SRE ConOps Diagnostics, Triage & Janitor
Executes structured AWS and GitHub Actions operational commands:

```bash
# Live AWS stack inspection (ECS tasks, stopped errors, VPC endpoints, routing, security groups)
tfkit ops diagnose --prefix bp-e2e-

# GitHub Actions incident triage (isolates failed steps and extracts root-cause logs)
tfkit ops triage --repo hatan4ik/aws.modules.blueprint-microservice-private --limit 5
tfkit ops triage --repo hatan4ik/aws.modules.blueprint-microservice-private --run-id 38297205266

# Safe dependency-ordered orphan resource janitor
tfkit ops janitor --prefix bp-e2e- --dry-run
tfkit ops janitor --prefix bp-e2e- --force
```

### 5. Static Workflow & Documentation Policy Gates
Enforces supply chain security and documentation link validity:

```bash
# Validate workflow top-level schema and 40-character commit SHA pins
tfkit policy workflows --dir .github/workflows

# Verify all repository-relative Markdown links resolve on disk
tfkit policy markdown --dir docs
```

---

## Exit Code Contract

| Exit Code | Meaning |
| :--- | :--- |
| `0` | Success / All checks and invariants passed |
| `1` | Unexpected runtime, I/O, or upstream API error |
| `2` | CLI usage or flag validation error |
| `3` | Policy, drift, or release invariant violation detected |

---

## License

Apache License 2.0. See [LICENSE](LICENSE) for details.
