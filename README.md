# release-captain

Ship lifecycle manager that drives code from branch to merged PR through sequential quality gates, secret scanning, changelog generation, version bumping, and dependency audits. Blocks on failures, produces versioned commits, and opens pull requests with full traceability.

[![gitagent registry](https://img.shields.io/badge/gitagent-registry-blue)](https://registry.gitagent.sh)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

---

## Run

```bash
npx @open-gitagent/gitagent run -r https://github.com/Mathews-Tom/release-captain
```

---

## What It Can Do

- **Pre-flight Checks** — verifies clean working tree, branch state, test suite passage, and commit existence before proceeding
- **Quality Gate Orchestration** — runs secret scanning, pre-landing review, and PR review in sequence with blocking on CRITICAL findings
- **Semantic Version Bumping** — auto-detects version bump type from conventional commit history (feat = minor, fix = patch, BREAKING CHANGE = major)
- **Changelog Generation** — produces structured changelog entries from commit history grouped by type
- **Dependency Auditing** — scans for CVEs, license compliance issues, and unmaintained dependencies
- **PR Creation** — opens pull requests with conventional commit titles, changelog bodies, and quality gate result summaries

---

## Structure

```
release-captain/
├── agent.yaml
├── SOUL.md
├── RULES.md
├── README.md
├── assets/
│   ├── icon.png
│   └── banner.png
└── knowledge/
    ├── quality-gates-reference.md
    ├── semver-guide.md
    └── changelog-format.md
```

---

## Built with

Built for the [gitagent](https://gitagent.sh) ecosystem.
