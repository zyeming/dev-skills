---
name: code-review
description: Reviews Node.js/TypeScript and Python code for bugs, security issues, regressions, missing tests, architecture drift, risky diffs, and pre-commit problems. Use when the user asks to review code, inspect a git diff, audit files, check a pull request, or perform a codebase audit.
allowed-tools: Read Grep Glob Bash
---

# Code Review Skill

Review code across five axes: correctness, readability, architecture, security, and performance. Approve changes that clearly improve overall code health and follow project conventions. Do not block on personal style preferences or theoretical improvements.

Review code only. Do not edit files during review. If the user later asks for fixes, handle that as a separate coding task.

In Claude Code direct invocation, `$ARGUMENTS` may contain the requested scope. Otherwise, infer scope from the user's request and repository state.

## Step 1: Detect Scope

Determine review mode from the user's request, direct invocation arguments if available, and repository state:

- **No args**: `git diff HEAD` (staged + unstaged changes)
- **`--staged`**: `git diff --staged` only (pre-commit)
- **file/dir path**: specific files or directory
- **`--audit`**: full codebase scan of all `.ts`/`.js`/`.py` files
- **branch/PR**: `git diff main...HEAD`

```bash
git diff --staged --name-only
git diff --name-only
```

If both commands return empty output and no path/branch was specified, stop and tell the user: "No changes detected. Pass a file path, `--staged`, `--audit`, or a branch name to review."

## Step 2: Check Project Conventions

Read the nearest applicable project instructions before reviewing, such as `AGENTS.md`, `CLAUDE.md`, or repository review guidelines, if present and accessible. Check for:

- File size limits, function length limits
- Immutability requirements
- Error handling patterns
- Naming conventions
- Database policies (RLS, migrations)
- Protected-file and review policies

Apply project-specific rules before generic checklist items.

## Step 3: Understand Context

Before judging code, identify what the change is trying to accomplish:

- What behavior, bug, or refactor does this change target?
- What should be different after the change?
- Is the intent clear from the diff, tests, issue, or user request?

If intent is unclear, list it under Open Questions instead of inventing assumptions.

## Step 4: Review Tests First

Tests reveal intended behavior and coverage:

- Do tests exist for new behavior or bug fixes?
- Do tests verify behavior rather than implementation details?
- Are edge cases and error paths covered?
- Would the tests catch a regression if the implementation changed?
- Are security-sensitive or data-sensitive changes covered?
- Do mocks or stubs bypass code that enforces important preconditions or side effects?

Do not require tests for every trivial change. Require tests when behavior, bug fixes, auth, payments, data mutation, or external I/O changes.

## Step 5: Expand Context When Needed

Default to the smallest context that can prove or disprove the change. Expand beyond changed files when the review involves architecture, non-trivial behavior, cross-module effects, public APIs, persistence, async flows, external I/O, or security boundaries.

Inspect as needed:

- Direct callers and callees
- Public interfaces and data shapes
- Persistence/state ownership
- Similar existing implementations
- Relevant project or architecture docs

Stop expanding when you can explain the behavior, the involved contracts, and whether the implementation follows existing patterns.

## Step 6: Trace Behavioral Contracts

For non-trivial behavior, trace the contract across the relevant boundary:

- What must be true before this code runs?
- Where is that enforced?
- What does this code promise after it runs?
- Which downstream code relies on that promise?
- What happens when the precondition is partially true or stale?
- Are labels such as `ready`, `valid`, `resolved`, `completed`, or `safe` stronger than the checks actually performed?

## Step 7: Review Implementation

Review the changed code and expanded context with the five axes below:

- Read full changed files and relevant surrounding code.
- Check imports, dependencies, call sites, and module boundaries.
- Compare with existing patterns before calling something inconsistent.
- Detect language from targeted files and load language references only when useful.

Apply the five-axis review below. Use confidence-based filtering: only report findings you are more than 80% confident are real.

## Five-Axis Review

### Correctness

- Does the implementation match the requested behavior?
- Are caller assumptions enforced before deeper code relies on them?
- Are callee requirements satisfied by all reachable callers?
- Are intermediate states, outputs, or side effects complete before downstream code consumes them?
- Does failure leave state consistent and observable?
- Does a bug fix include a regression test that would fail before the fix?

### Readability & Simplicity

- Are names clear and consistent with project conventions?
- Is control flow straightforward?
- Are functions or files large because they mix responsibilities?
- Is the abstraction justified by actual reuse or complexity reduction?
- Is there dead, unreachable, duplicated, or compatibility-shim code left behind?
- Are comments explaining non-obvious intent instead of restating code?

Only report style, size, or naming issues when they materially affect understanding, maintenance, or risk.

### Architecture

- Does the change follow existing project patterns?
- Are module boundaries preserved?
- Are layer/module contracts explicit: validation, ownership, output shape, side effects, and error semantics?
- Is business logic kept out of HTTP/controller layers?
- Are dependencies injectable or otherwise testable?
- Are circular imports, global mutable state, or cross-module shortcuts introduced?
- Is refactoring mixed with behavior change in a way that makes review risky?
- Does the implementation remain coherent across the full affected flow, not only the changed file?

For structural changes or `--audit` mode, also read `references/architecture.md`.

### Security

Always check:

- Hardcoded credentials, tokens, connection strings, or secrets
- SQL, command, template, or deserialization injection
- SSRF from user-controlled URLs
- Path traversal from user-controlled file paths
- Unsafe deserialization or execution (`pickle`, unsafe YAML load, `eval`, `exec`)
- Secrets logged or exposed in errors
- Missing auth or authorization checks on protected routes
- Untrusted external data used without validation at system boundaries

For Node.js/TypeScript security details, read `references/nodejs.md`.
For Python security details, read `references/python.md`.

### Performance

- N+1 database or API calls
- Unbounded queries, loops, or list endpoints
- Missing pagination on user-facing collection APIs
- Missing timeout on external I/O
- Blocking I/O in async or request paths
- Large allocations or expensive work in hot paths

Report performance issues only when the changed code plausibly affects real request paths, jobs, or user-visible latency.

## Additional Review Signals

Apply these when relevant:

- **AI-generated code**: check for plausible but wrong APIs, missing edge cases, over-engineering, and trust-boundary drift.
- **Protected or sensitive changes**: migrations, infra, CI, env files, keys, auth, payments, crypto, user data, and raw SQL.
- **New dependencies**: prefer existing stack or standard library; require justification for sensitive packages.
- **Change sizing**: large diffs or mixed refactor plus behavior change should be split or explicitly justified.
- **Dead code**: list unreachable or replaced code, but do not delete it during review.

For team-specific AI coding standards, read `references/ai-coding-standards.md`.

## Severity

Use severity labels consistently:

- `CRITICAL`: Blocks merge. Security vulnerability, data loss, auth bypass, broken core functionality, or dangerous production impact.
- `HIGH`: Should fix before merge. Real bug, missing regression test for bug fix, serious maintainability risk, or architecture risk.
- `MEDIUM`: Should fix or explicitly defer. Localized risk, unclear behavior, avoidable complexity, or moderate performance issue.
- `LOW`: Optional. Style, naming, documentation, or cleanup that does not materially affect correctness or risk.

Put uncertain issues in Open Questions, not findings.

## Output

Start with findings ordered by severity. Keep summaries brief.

```markdown
## Code Review

Mode: [git diff | staged | file | branch | audit]
Files reviewed: [list]

[HIGH] Finding title
File: path/to/file.ts:42
Issue: What is wrong.
Fix: What should change.

## Open Questions

- Question that affects review confidence.

## Summary

Verdict: [Block | Warning | Approve]
```

If there are no findings, say that clearly and mention any remaining uncertainty.
