# Semantic Versioning Guide

Version numbering rules and conventional commit mapping for automated version bumps.

## Version Format

```
MAJOR.MINOR.PATCH[-PRERELEASE][+BUILD]
```

| Component | Increment When | Example |
|-----------|---------------|---------|
| MAJOR | Breaking changes to public API | 2.0.0 |
| MINOR | New features, backward compatible | 1.3.0 |
| PATCH | Bug fixes, backward compatible | 1.2.4 |
| PRERELEASE | Pre-release identifier | 1.3.0-beta.1 |
| BUILD | Build metadata (not for precedence) | 1.3.0+build.42 |

## Conventional Commit → Version Bump Mapping

| Commit Type | Footer | Version Bump |
|------------|--------|-------------|
| `feat!:` | — | MAJOR |
| Any type | `BREAKING CHANGE: ...` | MAJOR |
| `feat:` | — | MINOR |
| `fix:` | — | PATCH |
| `perf:` | — | PATCH |
| `refactor:` | — | PATCH |
| `docs:` | — | No bump (or PATCH) |
| `style:` | — | No bump |
| `test:` | — | No bump |
| `chore:` | — | No bump |
| `ci:` | — | No bump |
| `build:` | — | PATCH (if affects output) |

### Bump Priority
When multiple commits are present, the highest bump wins:
```
MAJOR > MINOR > PATCH > No bump
```

## Breaking Change Detection

### Signals Requiring MAJOR Bump
- Removing or renaming a public function/method/class
- Changing function signature (required parameters added/removed)
- Changing return type of public API
- Removing or renaming configuration keys
- Changing database schema in non-additive ways
- Removing or renaming CLI flags/arguments
- Changing default behavior that users depend on
- Removing API endpoints or changing their contract

### Commit Format for Breaking Changes
```
feat(api)!: require authentication on /users endpoint

BREAKING CHANGE: GET /users returns 401 without Authorization header
```

## Version Precedence Rules

Versions are compared left to right:
```
1.0.0 < 1.0.1 < 1.1.0 < 2.0.0
1.0.0-alpha < 1.0.0-alpha.1 < 1.0.0-beta < 1.0.0
```

### Pre-release Ordering
1. Alphanumeric identifiers compared lexically
2. Numeric identifiers compared as integers
3. Numeric < alphanumeric
4. Fewer fields < more fields (if all preceding are equal)

## Version Lifecycle

```
0.1.0 → 0.2.0 → ... → 0.x.0 → 1.0.0 → 1.0.1 → 1.1.0 → 2.0.0
  |                       |         |
  Initial dev         First stable  Breaking change
  (anything goes)     (API contract) (MAJOR bump)
```

### Pre-1.0.0 Rules
- Public API is not stable
- MINOR bumps may include breaking changes
- Use for initial development only
- Transition to 1.0.0 when API is production-ready

## Common Mistakes

| Mistake | Correct Approach |
|---------|-----------------|
| Bumping MAJOR for large features | MINOR — size does not determine bump type |
| Not bumping for security fixes | PATCH — fixes are patches |
| Skipping versions (1.0.0 → 1.0.5) | Sequential: each release increments by 1 |
| Reusing version numbers | Never reuse — even if release was yanked |
| Breaking change without MAJOR bump | MAJOR is mandatory for breaking changes |
| PATCH for new features | MINOR — new capability = minor bump |
