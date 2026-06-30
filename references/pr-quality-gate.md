# PR Quality Gate

Run this gate before opening or updating a GitCode PR.

## Repository Standards

Inspect and follow:

- `AGENTS.md` and local agent instructions.
- `CONTRIBUTING*`, `SECURITY*`, `README*`, `docs/CONTRIBUTING*`.
- `.gitcode/PULL_REQUEST_TEMPLATE*`, `.gitcode/ISSUE_TEMPLATE*`, `.gitcode/workflows/`.
- `.pre-commit-config.yaml`, `.clang-format`, `CMakeLists.txt`, `build.sh`, `requirements.txt`, and language-specific build/test configs.

## Local Checks

Always start with:

```bash
git diff --check
git status --short
```

Then adapt to the stack:

- C/C++/CMake: `clang-format --dry-run --Werror <changed_files>`, `cmake -S . -B build`, `cmake --build build`, project `build.sh` targets.
- Python: `python -m py_compile <changed_python_files>`, `pytest -q <targeted_tests>`, `pre-commit run --files <changed_files>`.
- Shell: `bash -n <changed_shell_files>`, `shellcheck <changed_shell_files>` when available.
- Docs: markdown lint or docs build when configured.

For CANN operator repositories, prefer the narrowest feasible `build.sh` target first. If real Ascend hardware, CANN runtime, simulator, or large third-party downloads are required, record the blocker and run static/config checks that do not require that environment.

## Review-Risk Self-Check

Before PR creation:

- Does the change match the issue and avoid unrelated refactors?
- Is there a regression test, static check, or deterministic reproduction?
- Are public APIs, file formats, and docs kept compatible unless the issue requires a change?
- Are errors explicit and actionable?
- Are temporary files, paths, permissions, downloads, and command invocations safe?
- Are generated files, vendored files, and license headers handled according to project rules?
- Does the PR body include exact validation commands and results?

## PR Body Requirements

Use the repository template. Include:

- reason and root cause,
- solution summary,
- linked issue, preferably `Fixes #<number>` or the project-specific equivalent,
- tests run and results,
- documentation updates or `N/A`,
- type checkbox,
- hardware/CI-only limitations,
- AI assistance disclosure only if the project asks for it.

## CI Follow-Up

After opening a PR:

```bash
gitcode pr view <number> -R <owner>/<repo> --comments
gitcode pr test <number> -R <owner>/<repo>
gitcode pr comments <number> -R <owner>/<repo>
```

If checks are blocked by maintainer permission, missing hardware, or new-contributor gates, leave a concise comment with local validation and the exact gate. Do not repeatedly push without a code or metadata change.
