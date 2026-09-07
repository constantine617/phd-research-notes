# PhD Research Notes — Infrastructure Baseline v1

## Baseline

- Freeze date: 2026-09-07 (Asia/Shanghai).
- Repository: <https://github.com/constantine617/phd-research-notes>.
- Website: <https://constantine617.github.io/phd-research-notes/>.
- Verified cleanup commit SHA: `76ae50baf89a431566fc1ddd488c3db2089d0385`.
- Final freeze revision: annotated tag [`infra-v1`](https://github.com/constantine617/phd-research-notes/tree/infra-v1). It includes this baseline record after the cleanup commit; resolve its exact commit with `git rev-parse 'infra-v1^{commit}'`. The final external freeze report records that complete SHA without a self-referencing commit.
- Python: 3.12 (local verification: 3.12.10).
- MkDocs: 1.6.1.
- Material for MkDocs: 9.7.7.
- uv: 0.8.6.
- MathJax: 3.2.2.
- Dependency definitions and full resolution: `pyproject.toml` + `uv.lock`.

## Verified Features

Markdown navigation, built-in search, MathJax, Mermaid, built-in tags, Light/Dark toggle and responsive Material layout were verified during Phase 2. Their implementations remain unchanged at freeze; the public feature fixture has been removed.

Final cleanup validation: locked sync, lock check, normal build and strict build passed; local homepage HTTP 200. Generated search contains 12 placeholder pages and no test content. Checked 217 generated internal links with no missing targets or anchors. GitHub Actions build/deploy and GitHub Pages passed; public homepage HTTP 200, removed fixture HTTP 404, public search clean.

Cleanup evidence: [Actions](https://github.com/constantine617/phd-research-notes/actions/runs/34104789673), [Pages](https://github.com/constantine617/phd-research-notes/actions/runs/34104832640). Historical feature evidence is in `PHASE2_REPORT.md`; its description of a retained fixture is historical and superseded by this record.

## Architecture Rules

1. Markdown-first.
2. `docs/` is public content, including assets and pages absent from navigation.
3. No backend.
4. No database.
5. No Node frontend architecture.
6. Minimal custom CSS/JS; retain native Material layout.
7. `pyproject.toml + uv.lock` are the dependency source of truth.
8. Infrastructure versions stay frozen unless there is a concrete reason to upgrade.
9. Content development must not trigger infrastructure refactoring.
10. Private research files must never be automatically imported into `docs/`.

## Future Infrastructure Changes

Reopen infrastructure work only for a confirmed bug, security issue, required dependency compatibility fix, necessary research-content feature, or a deliberate future phase approved by the user.

A newer release, attractive alternative plugin, visual redesign idea, or refactoring preference is not a reason to change this baseline. No implicit MkDocs 2.x upgrade. No release package is created for this tag.

## Non-blocking Limits

- Real pull-request event has not been exercised; verify build-only/no-deploy with the first meaningful PR, not a synthetic test PR.
- MathJax/Mermaid rendering depends on external assets; offline fallback and comprehensive browser/device testing are not verified.
- Material's MkDocs 2.0 advisory remains; current 1.6.1 strict build passes without project WARNING/ERROR diagnostics.
- GitHub emits Node 20 action-runtime deprecation notices while running these actions on Node 24; builds/deployments pass. This is runner tooling, not a Node frontend. Do not proactively migrate the workflow during freeze.
- A 404 removes the current page; search-engine caches and Git history are not erased.
