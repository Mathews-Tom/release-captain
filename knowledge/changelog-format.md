# Changelog Format Reference

Structure and generation rules for changelog entries from conventional commits.

## Changelog Structure

```markdown
# Changelog

## [1.3.0] - 2026-03-26

### Features
- **auth:** add JWT refresh token rotation (#42)
- **api:** support batch user creation endpoint (#38)

### Fixes
- **db:** prevent connection pool exhaustion under load (#41)
- **ui:** correct date picker timezone handling (#39)

### Performance
- **cache:** reduce Redis round trips for session lookup (#40)

### Breaking Changes
- **api:** require authentication on /users endpoint (#37)
  GET /users now returns 401 without Authorization header.

## [1.2.1] - 2026-03-20
...
```

## Section Ordering

| Section | Commit Types | Display Order |
|---------|-------------|---------------|
| Breaking Changes | Any with `BREAKING CHANGE` footer or `!` | 1 (always first) |
| Features | `feat` | 2 |
| Fixes | `fix` | 3 |
| Performance | `perf` | 4 |
| Refactoring | `refactor` | 5 (optional) |
| Documentation | `docs` | 6 (optional) |

### Excluded from Changelog
- `chore`, `ci`, `build`, `style`, `test` — infrastructure changes, not user-facing

## Entry Format

### Standard Entry
```
- **<scope>:** <subject> (#<PR-number>)
```

### Entry with Body (for breaking changes)
```
- **<scope>:** <subject> (#<PR-number>)
  <body explaining impact and migration>
```

## Generation Rules

### From Commit History
```bash
# Get commits between last tag and HEAD
git log v1.2.0..HEAD --format="%s|%b" --no-merges
```

### Parsing Logic
1. Extract type and scope from commit subject: `type(scope): subject`
2. Group by type
3. Sort groups by display order
4. Within each group, sort by scope alphabetically
5. Append PR/issue number if present in commit footer

### Commit-to-Entry Mapping

| Commit Message | Changelog Entry |
|---------------|-----------------|
| `feat(auth): add OAuth2 PKCE flow` | `- **auth:** add OAuth2 PKCE flow` |
| `fix(api): handle null query params` | `- **api:** handle null query params` |
| `feat(api)!: require auth on /users` | Listed under Breaking Changes |

## Version Header Format

```markdown
## [MAJOR.MINOR.PATCH] - YYYY-MM-DD
```

- Use ISO 8601 date format
- Link version to comparison URL when hosted:
  ```markdown
  [1.3.0]: https://github.com/org/repo/compare/v1.2.0...v1.3.0
  ```

## Multi-Commit Consolidation

### When Multiple Commits Share Scope
If 3+ commits modify the same scope, consider consolidating:
```markdown
### Fixes
- **auth:** resolve session expiry race conditions and token refresh timing (#41, #43, #44)
```

### When to Keep Separate
- Different logical changes (even if same scope)
- Different severity/impact
- Different PR numbers representing distinct reviews

## Changelog File Location

| Convention | File Path |
|-----------|-----------|
| Keep a Changelog | `CHANGELOG.md` (project root) |
| GitHub Releases | Release body via API |
| Both | Maintain CHANGELOG.md, copy to release body on tag |

## Automation Integration

### PR Body Template
```markdown
## Changelog

### [vX.Y.Z]

#### Features
- ...

#### Fixes
- ...

## Quality Gates
| Gate | Status |
|------|--------|
| Tests | PASS |
| Secrets | PASS |
| Dependencies | PASS |
```

### Validation Rules
- Every `feat` and `fix` commit must appear in changelog
- Breaking changes must have explicit migration notes
- Version number must match commit-derived bump
- Date must be the PR creation date
