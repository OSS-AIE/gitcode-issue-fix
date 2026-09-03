---
name: gitcode-issue-fix
description: Use this skill for GitCode-hosted repositories when asked to analyze repository architecture and contribution rules, discover security/functionality/pre-existing bug issues, create GitCode issues with gitcode-cli, fix issues, submit pull requests, or repair PR CI failures while following project contribution guidelines.
---

# GitCode Issue Fix

## Build Engineering Composition

For build-script usability, build/test performance, dependency structure, reproducibility, SBOM, provenance, or build supply-chain security work, **REQUIRED SUB-SKILL:** Use `refactor-build-system` before selecting an issue or editing code. `refactor-build-system` owns the build analysis and issue contract; this skill remains the owner of GitCode issue triage, `gitcode`/`gc.exe` operations, branch/PR changes, Pipeline checks, review feedback, and final handoff. Do not duplicate the build-analysis procedure here.

## Overview

Use this skill for a full GitCode open-source contribution loop: understand the repository, generate a repository architecture and contribution-rules Markdown profile, identify candidate issues, file issues with `gitcode`, implement minimal fixes, open/update PRs, and drive local/remote CI to green.

Use the GitCode CLI as `gitcode`/`gitcode.exe` by default. On Windows PowerShell, do not use bare `gc` because it is a built-in alias for `Get-Content`; use `gitcode.exe`, `gc.exe`, or `python -m gc_cli` instead. Inspect `gitcode <subcommand> --help` and adapt commands if flags differ.

## Required First Step

Before proposing issues or editing code:

1. Preserve local work: run `git status --short`, `git branch --show-current`, and `git remote -v`.
2. Read repository rules: `AGENTS.md`, `CONTRIBUTING*`, `SECURITY*`, `.gitcode/PULL_REQUEST_TEMPLATE*`, `.gitcode/ISSUE_TEMPLATE*`, `.gitcode/workflows/`, `.pre-commit-config.yaml`, build/test configs, and language manifests.
3. Generate or refresh a repository profile Markdown:
   - `python "<skill>/scripts/generate_repo_profile.py" --repo "." --output "docs/architecture-and-contribution-rules.md"`
   - If the repo has another docs convention, choose the nearest existing docs path.
4. Read `references/gitcode-cli-workflow.md` before using `gitcode`.
5. Read `references/pr-quality-gate.md` before committing, pushing, or opening a PR.

For work that may sprawl across files, or when discovering new engineering issues, read
`references/scope-and-archetypes.md` for the minimal-change scope guard and common issue archetypes.

## Issue Discovery

Look for issues in three buckets:

- `security`: unsafe permissions, command injection, path traversal, secret leakage, insecure downloads, missing input validation, unsafe temporary files, or risky default behavior.
- `functionality`: incorrect behavior, edge cases, missing validation, bad error handling, broken docs/examples, platform incompatibility, or API contract mismatches.
- `pre-existing bug`: failures visible in tests, static analysis, TODO/FIXME markers with clear impact, stale CI scripts, broken build flags, or contradictions between docs and code.

For each candidate, collect evidence before filing:

- impacted files and exact behavior,
- reproduction or static proof,
- expected behavior,
- severity and user impact,
- proposed minimal fix,
- validation plan.

Do not file noisy issues. Skip duplicates, vague design ideas, private-environment problems, issues already owned by maintainers, or items with no reviewable validation path.

## Filing GitCode Issues

Use repository templates when present. For this common GitCode layout:

- Bug: title prefix like `[Bug-Report|缺陷反馈]: <short summary>`, label `bug-report`.
- Requirement: title prefix like `[Requirement|需求建议]: <short summary>`, label `requirement`.
- Documentation: follow the repo's documentation issue template and labels.

Prefer body files to avoid shell quoting mistakes:

```bash
gitcode issue create -R <owner>/<repo> --title "<template prefix>: <summary>" --body-file <issue-body.md> --label <label>
```

After creating an issue, comment `/assign @yourself` only if the project workflow asks contributors to self-assign and you intend to fix it.

## Fix Workflow

1. Fetch issue metadata with `gitcode issue view <number> -R <owner>/<repo> --comments` and search linked PRs with `gitcode issue prs <number> -R <owner>/<repo>`.
2. Classify:
   - `skip`: closed, duplicate, already fixed, active maintainer owner, or outside repo scope.
   - `needs-info`: missing reproduction, design decision needed, hardware/private data required with no local proxy.
   - `candidate`: narrow root cause and at least one local/static/test validation path.
3. Create one branch per independent root cause from the upstream default branch: `codex/issue-<number>-<slug>`.
4. Implement the smallest mergeable fix. Avoid broad refactors, unrelated formatting, and test weakening.
5. Add or update focused regression tests where practical. If hardware is required, add deterministic static/unit/docs validation that CI can still review.
6. Run the quality gate. Record exact commands and results.
7. Commit using project style and required trailers. Use sign-off only if the project requires it.
8. Push to a fork and create a PR with `gitcode pr create`, filling the project PR template and linking the issue.

## PR and CI Workflow

Use `gitcode pr test <number> -R <owner>/<repo>` when the project supports explicit PR test triggering. For projects that use bot comments such as `compile`, follow the contribution guide and comment only after local validation passes.

When CI fails:

1. Inspect PR details and comments: `gitcode pr view <number> -R <owner>/<repo> --comments`, `gitcode pr comments <number> -R <owner>/<repo>`.
2. Classify failures: lint/format, build, unit test, docs, permission gate, hardware gate, flaky/environmental, or reviewer feedback.
3. Fix only failures caused by the PR. Do not hide failures by deleting tests, weakening assertions, or bypassing checks unless the check is demonstrably wrong.
4. Re-run the closest local equivalent before pushing.
5. If CI is blocked by maintainer permission or unavailable hardware, leave a concise PR comment with local validation and the exact remaining gate.

## Output Requirements

End work with:

- repository profile path,
- issue numbers or skipped issue candidates with reasons,
- root cause and changed files,
- tests/validation commands and results,
- CI status or remaining maintainer/hardware gate,
- PR URL or a generated handoff path.

If automatic PR creation fails, create a local handoff:

```bash
python "<skill>/scripts/generate_pr_report.py" --input <handoff.json> --output <handoff.html>
```
