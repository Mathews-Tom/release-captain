# RULES.md — Release Captain

Hard constraints that govern all agent behavior. Non-negotiable.

## ALWAYS

- Run the test suite and verify all tests pass before any quality gate
- Run secret scanning on every release — the secret-scanner gate is mandatory
- Block on any CRITICAL finding from any gate — no exceptions, no overrides
- Retry failed gates maximum 3 times for transient errors only — legitimate findings are not retried
- Create bisectable commits — every commit must build and pass tests independently
- Auto-detect version bump from commit types unless user explicitly specifies (feat! or BREAKING CHANGE = major, feat = minor, otherwise patch)
- Include the changelog entry in the PR body for reviewer context
- Verify PR creation succeeded and CI triggered before reporting success
- Report blocking issues with file:line references and specific fix recommendations
- Verify current branch is not the target branch before shipping
- Confirm no uncommitted changes exist before starting the pipeline
- Present the changelog entry to user for confirmation before shipping

## NEVER

- Ship with failing tests
- Skip secret scanning for any reason
- Force-push or rewrite history on shared branches
- Override a BLOCK verdict from any quality gate
- Declare success before verifying PR creation and CI trigger
- Ship from main to main
- Retry gates that returned legitimate findings (only retry transient failures)

## SHOULD

- Fetch and check if branch is up to date with remote target before starting
- Determine version bump automatically from commit history when not specified
- Run quality gates in the defined sequence: pre-flight, secret scan, pre-landing review, PR review, dependency audit
- Include quality gate pass/fail status in the PR body
- Reference related issues from commit messages in the PR
- Provide a machine-parseable summary line: `**Release:** vX.Y.Z — PR #N opened against <target>`
- Report the complete release summary: version, changelog highlights, gate results, PR URL
