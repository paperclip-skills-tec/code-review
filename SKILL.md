---
name: code-review
description: Structured code review workflow for Deltek Developer Dashboard PRs and changes. Checks code quality, security, conventions, and test coverage.
---

# Code Review

Review code changes for quality, security, conventions, and correctness before merging.

## Purpose

Provide structured, thorough code reviews for the Deltek Developer Dashboard project. Ensures all changes meet project standards before integration.

## Inputs

- **PR or branch diff** — the set of changed files to review
- **Paperclip issue ID** — the associated task (e.g., `TEC-xxx`) for traceability

## Workflow

1. **Fetch context** — Read the associated Paperclip issue to understand the intent behind the change.
2. **Review diff** — Examine every changed file for:
   - Correctness: Does the code do what the issue requires?
   - Security: No secrets, no injection vectors, no OWASP top-10 issues.
   - Conventions: React 18 + Bootstrap 5 patterns on frontend, Express patterns on backend, Knex migration conventions for DB.
   - Tests: Are new/changed paths covered by Jest unit tests or Playwright E2E tests?
   - **Assertion adequacy**: Do the tests actually verify what they claim? A test that exists but only asserts `toBeTruthy()` or `toBeDefined()` provides false confidence. For each test block, check: (a) assertions are specific to the expected shape/value, not just presence; (b) negative assertions cover removed or forbidden fields (e.g., `expect(result).not.toHaveProperty('deletedField')`); (c) `toBeInstanceOf` or type-specific matchers are used when type matters, not generic truthiness; (d) each distinct code path (success, not-found, validation error, permission denied) has its own targeted assertions, not shared generic ones.
   - Performance: No unnecessary re-renders, N+1 queries, or missing indexes.
3. **Check Paperclip references** — Confirm commit messages and PR description reference the Paperclip issue ID in the correct format (e.g., `TEC-74`).
4. **Summarise findings** — Produce a structured review with severity levels: `blocker`, `warning`, `suggestion`, `praise`. Weak assertions (tests that exist but don't verify meaningful outcomes) should be raised as `warning` at minimum; missing negative assertions on security-relevant removals are `blocker`.
5. **Post feedback** — Comment on the PR or Paperclip issue with the review summary.

## Outputs

A markdown review summary containing:
- Overall verdict: `approve`, `request-changes`, or `comment`
- List of findings grouped by severity
- Reference to the Paperclip issue (e.g., [TEC-74](/TEC/issues/TEC-74))

## Example Invocation

```
/code-review PR #42 for TEC-74
```
