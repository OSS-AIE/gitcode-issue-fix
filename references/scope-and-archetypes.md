# Scope Guard and Engineering Issue Archetypes

Use this reference when a GitCode issue, PR review fix, CI repair, or new issue discovery task may sprawl across files or mix unrelated concerns.

## Scope Guard

Before editing, write the issue contract in one sentence: the exact GitCode issue, review comment, CI failure, or contributor pain being fixed.

Keep the change narrow:

- List the files that must change and why before touching them.
- Do not modify unrelated code, docs, generated metadata, formatting, or workflows.
- Keep formatter churn limited to files already needed for the issue unless formatting is the issue.
- Split unrelated cleanup into follow-up GitCode issues and PRs. Do not bundle build-script cleanup, workflow cleanup, docs cleanup, and code refactors unless the issue explicitly asks for that combination.
- Prefer adding a step to an existing script or workflow job over adding a new job, file, or abstraction.

Before committing or pushing:

- Inspect `git diff --stat` and `git diff`.
- Revert or split drive-by changes.
- Confirm every changed file maps directly to the issue contract.
- Run the narrowest validation that proves the change, then broader checks only when the blast radius requires them.

When reviewer feedback asks for a smaller approach, reduce the patch first. Keep only the code needed to satisfy the review thread and preserve existing project patterns.

## Engineering Issue Archetypes

Use these archetypes to discover reviewable issues and keep PRs focused.

### Build Script Usability

Look for build scripts that are hard to run locally, have unclear defaults, assume one platform, or fail with poor errors. Good fixes improve messages, document required inputs, make defaults explicit, or reuse project-native helpers.

Evidence:

- failing command and exact error,
- affected shell, OS, or container context,
- expected contributor workflow,
- smallest script or docs change that improves the workflow.

Avoid rewriting the build system unless the issue is specifically about build-system design.

### Build Performance And Dependency Efficiency

Look for repeated dependency installs, missing caches, overly broad rebuild triggers, unnecessary network fetches, or serial steps that can be safely narrowed.

Evidence:

- repeated or expensive step,
- why the dependency or trigger is broader than needed,
- local timing, CI log excerpt, or static workflow proof,
- validation that outputs remain unchanged.

Keep performance PRs measurable and isolated from behavior changes.

### Unit-Test Efficiency And Reliability

Look for slow, flaky, over-broad, or environment-coupled unit tests. Good fixes reduce setup cost, narrow parametrization, remove sleeps, add deterministic fixtures, or separate hardware/integration tests from UT.

Evidence:

- specific test path and command,
- failure mode or time cost,
- root cause in fixture, parametrization, dependency, or environment,
- before/after command result when possible.

Do not weaken assertions or skip tests unless the test is demonstrably in the wrong layer and an equivalent check remains.

### Software Architecture And Build Dependency Analysis

Look for dependency cycles, hidden import-time side effects, optional dependencies imported unconditionally, or build-time/runtime dependency confusion.

Evidence:

- dependency graph, import path, or build manifest proof,
- user-visible effect such as slow startup, broken minimal install, or CI failure,
- minimal boundary change that follows existing architecture.

Prefer moving dependency edges to existing extension points over adding broad abstractions.

### Duplicate Include, Import, Or Header Cleanup

Look for duplicated headers, Python imports, stale includes, or repeated declarations that increase build time or confuse ownership.

Evidence:

- exact duplicate include/import locations,
- proof the symbol is unused or provided elsewhere,
- static check, compiler check, or targeted test result.

Keep cleanup mechanical and scoped to the duplicated lines. Do not re-sort unrelated import blocks unless the project formatter does it.

### CI And Workflow Correctness

Look for workflow inputs that drift across jobs, duplicated validation snippets, hardcoded versions, inconsistent matrix variables, or checks that do not match local scripts.

Evidence:

- exact `.gitcode/workflows/` file and job,
- duplicated or inconsistent variable flow,
- expected source of truth,
- validation with workflow syntax checks, local script checks, or CI dry-run tooling where available.

Prefer one shared step or existing job output over a new workflow job when the data is only used in that job.

### Documentation Contributor Experience

Look for contributor docs that omit clone/setup steps, mix full development setup with lint-only setup, use platform-specific commands without alternatives, or drift from CI.

Evidence:

- affected doc section,
- contributor task being blocked,
- command sequence that works locally or matches CI,
- minimal docs patch.

Keep docs patches focused on the documented workflow; split unrelated wording cleanup into a separate PR.
