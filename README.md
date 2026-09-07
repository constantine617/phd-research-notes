# PhD Research Notes

A Markdown-first knowledge base for trustworthy LLMs and LVLMs, hallucination, uncertainty quantification, confidence calibration, and multimodal reasoning.

This project is based on [lifeadventurer/mkdocs-material-template](https://github.com/lifeadventurer/mkdocs-material-template).
Upstream baseline: `f1a77ee1696c7e574229ae8b9e538e448c82665c`. The original MIT license and copyright are retained in [LICENSE](LICENSE).

## Local development

Install Python 3.12 and [uv](https://docs.astral.sh/uv/) (CI uses uv 0.8.6), then run:

```powershell
uv sync --locked --no-dev
uv run --locked --no-dev mkdocs serve
```

Open <http://127.0.0.1:8000/phd-research-notes/>. No environment activation, Node, database, or backend is needed.

```powershell
uv run --locked --no-dev mkdocs build
uv run --locked --no-dev mkdocs build --strict
```

`pyproject.toml` defines dependencies; `uv.lock` pins their resolution. These are the only dependency sources. The upstream requirements export and unused developer tooling have been removed. Python is restricted to 3.12; MkDocs 1.6.1 and Material 9.7.7 are pinned. Update deliberately on a branch with `uv lock`, then validate before merging. Do not implicitly upgrade to MkDocs 2.x.

## Content and features

Write public Markdown in `docs/` and update the handwritten `nav` in `mkdocs.yml`. Research Log is an ordinary directory. Initial topic pages are placeholders.

Math uses PyMdown arithmatex and MathJax 3.2.2 from jsDelivr. Mermaid uses Material's standard superfences integration. Tags and search are built-in plugins. Light/dark mode and responsive navigation use the native theme; custom CSS is empty. Instant navigation is disabled so each page loads and typesets normally.

The intentionally public [feature fixture](https://constantine617.github.io/phd-research-notes/feature-test/) is kept for regression checks but absent from the main navigation. It remains searchable. CDN availability is required for math and diagram rendering.

## Deployment

Main pushes install locked dependencies and run `mkdocs build --strict`, then publish the exact build artifact to `gh-pages`. The publisher `ghp-import` is already a MkDocs dependency. Pull requests only build, with read-only repository permissions. Pages uses **Deploy from a branch → gh-pages → / (root)**. No custom domain or repository secret is required.

Site: <https://constantine617.github.io/phd-research-notes/>.

## Public/private boundary

Everything in `docs/` may be published, including files omitted from navigation and non-Markdown assets. Only explicitly public content belongs here. Keep private notes, supervisor discussions, unpublished results, datasets, and confidential ideas outside this public repository. Do not synchronize research folders or include files outside `docs/`. Gitignore cannot erase committed secrets.
