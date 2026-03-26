# Quality Gates Reference

Gate definitions, pass/fail criteria, and execution order for release pipelines.

## Gate Execution Order

```
Pre-flight → Secret Scan → Pre-landing Review → PR Review → Dependency Audit → Ship
```

Each gate must PASS before the next executes. Any CRITICAL finding blocks the release.

## Gate Definitions

### Gate 1: Pre-flight Checks

| Check | Pass Criteria | Block On |
|-------|--------------|----------|
| Branch validation | Source != target branch | Shipping from main to main |
| Clean working tree | `git status` shows no changes | Uncommitted modifications |
| Remote sync | Branch up to date with remote target | Behind remote (needs rebase) |
| Test suite | All tests pass, zero failures | Any test failure |
| Commit existence | >= 1 commit between source and target | No new commits |

### Gate 2: Secret Scanning

| Check | Pass Criteria | Block On |
|-------|--------------|----------|
| Provider keys | Zero matches | Any CRITICAL or HIGH finding |
| Private keys | Zero PEM headers in source | Any private key detected |
| Connection strings | Zero embedded credentials | Any credential in connection string |
| High-entropy strings | Manual review for MEDIUM | CRITICAL or HIGH entropy findings |

Verdict: BLOCK if any CRITICAL or HIGH. PASS only on zero CRITICAL + zero HIGH.

### Gate 3: Pre-landing Review

| Check | Pass Criteria | Block On |
|-------|--------------|----------|
| Correctness | No logic errors identified | CRITICAL correctness issues |
| Error handling | Errors handled appropriately | Silent failures, bare except |
| Test coverage | Changed code has test coverage | Untested public API changes |
| Code quality | No HIGH severity quality issues | CRITICAL quality findings |

### Gate 4: PR Review

| Check | Pass Criteria | Block On |
|-------|--------------|----------|
| Commit conventions | All commits follow conventional format | Non-conventional commits |
| Breaking changes | BREAKING CHANGE footer present when needed | Breaking change without footer |
| Code quality | Overall quality assessment | CRITICAL findings |

### Gate 5: Dependency Audit

| Check | Pass Criteria | Block On |
|-------|--------------|----------|
| CVE scan | Zero CRITICAL CVEs | Any CRITICAL CVE |
| License compliance | No copyleft in proprietary projects | GPL in MIT/proprietary project |
| Maintenance health | No abandoned critical dependencies | Unmaintained with known vulns |

## Retry Policy

| Failure Type | Retry? | Max Retries |
|-------------|--------|-------------|
| Transient error (network, timeout) | Yes | 3 |
| Legitimate finding (vulnerability) | No | 0 |
| Test flake (intermittent failure) | Yes | 2 |
| Authentication error | No | 0 |

## Gate Result Recording

### Result Format
```markdown
| Gate | Status | Duration | Findings |
|------|--------|----------|----------|
| Pre-flight | PASS | 12s | 0 |
| Secret Scan | PASS | 8s | 0 |
| Pre-landing | PASS | 45s | 2 LOW |
| PR Review | PASS | 30s | 1 MEDIUM |
| Dependency | PASS | 15s | 0 CVEs |
```

### Blocking Issue Format
```markdown
### [RC-001] Title
- **Gate:** Secret Scan
- **Severity:** CRITICAL
- **File:** `src/config.ts:42`
- **Issue:** AWS access key hardcoded
- **Fix:** Move to environment variable, rotate key
```

## Escalation Protocol

| Situation | Action |
|-----------|--------|
| CRITICAL finding | Block release, report finding with fix recommendation |
| HIGH finding (secret scan) | Block release |
| HIGH finding (other gates) | Block, but allow override with justification |
| Gate timeout (3 retries) | Block, report infrastructure issue |
| All gates pass | Proceed to ship |
