# Phase 2 — Base Project Initialization

验收日期：2026-09-07。

**Phase 2 PASS**：模板已清理为 Markdown 研究知识库骨架；本地基础功能完成实际验收；Windows 普通/strict build、线上 Actions 构建与 Pages 部署通过。仅有占位内容和公开开发测试页，没有导入正式 PhD 材料。下面明确列出尚未执行的扩展测试。

## Repository and provenance

- Repository: <https://github.com/constantine617/phd-research-notes>（public）。
- Website: <https://constantine617.github.io/phd-research-notes/>。
- 通过 GitHub 模板生成机制创建，GitHub API 确认 template_repository 为 `lifeadventurer/mkdocs-material-template`；不是从零 mkdocs new。
- Phase 1 upstream baseline: `f1a77ee1696c7e574229ae8b9e538e448c82665c`。
- 新仓库初始提交：`7d15412460dd2ad175836f08309c4cd52dba9631`。
- 本次网站实现验收提交：`075089c116a6933025414575bc8a3e2f70cf3445`。本报告为其后的说明文档，不改变网站功能。
- 原 MIT LICENSE 和 `Copyright (c) 2025 Life Adventurer` 保留；LICENSE Git blob 前后均为 `100e944384e3472ca109c18667f7ac14e421e927`。README 保留来源链接及 baseline SHA；网站 UI 移除原作者 branding/social/repo 链接。

## 清理和保留

删除 demo reference/index 与 Reference 1–3、getting-started、contributing、changelog、blog/index、blog/.authors、重复的 docs/license 页面，以及原作者 CODEOWNERS。根 LICENSE 未删除。

移除 blog/glightbox plugins、glightbox 依赖、无用 pre-commit 开发依赖及其配置、旧 requirements 导出。uv.lock 随之移除 nodeenv、virtualenv、selectolax 等不再需要的依赖。没有新增 backend、Node 构建、数据库、CMS、citation、RAG、评论、analytics 或同步机制。

保留模板的 docs/、mkdocs.yml、pyproject.toml、uv.lock、原生 Material 设计、空 CSS 扩展文件、MathJax 配置文件和 gh-pages 发布路线；GitHub Actions 文件在原路径整理。没有 overrides/ 或自定义 HTML 模板。

## 目录结构

```text
phd-research-notes/
├── .github/workflows/gh-deploy.yml
├── .python-version
├── .gitignore
├── LICENSE
├── README.md
├── PHASE2_REPORT.md
├── pyproject.toml
├── uv.lock
├── mkdocs.yml
└── docs/
    ├── index.md
    ├── research-overview/index.md
    ├── foundations/index.md
    ├── hallucination/index.md
    ├── uncertainty/index.md
    ├── calibration/index.md
    ├── efficient-uq/index.md
    ├── representation-uq/index.md
    ├── multimodal/index.md
    ├── papers/index.md
    ├── research-log/index.md
    ├── glossary/index.md
    ├── feature-test.md
    ├── javascripts/mathjax.js
    ├── stylesheets/extra.css
    └── assets/{images,screenshots}/.gitkeep
```

11 个主题页面均只有标题和一句说明。首页为一句介绍和普通 Markdown 链接。feature-test 是例外的公开开发夹具，包含用户要求的 synthetic examples；不进主导航，但可通过直接 URL/搜索访问。`not_in_nav` 明确声明这个预期行为，不是隐私保护或排除发布。

## Dependency management

唯一来源：**pyproject.toml 定义 + uv.lock 锁定**。删除 requirements，CI 不再独立 pip 安装另一版本集。

- Python：`>=3.12,<3.13`；.python-version 和 CI 均为 3.12，本机实测 3.12.10。
- 直接依赖：MkDocs **1.6.1**、Material **9.7.7**，均精确锁定。
- uv **0.8.6** 用于本次及 CI；保留可兼容的上游锁定结果，没有执行全量 upgrade。
- 安装 29 个包；PyMdown 11.0.1、ghp-import 2.1.0 是已有传递依赖。无新增第三方 MkDocs plugin。
- `uv sync --locked --no-dev`、`uv lock --check`、`uv pip check` 均通过。
- 本机 uv 启动工具位于被忽略的 `.tools/`，站点环境在 `.venv/`；它们没有提交。不修改系统全局 Python 环境。

README 记录日常命令；以后按分支审查依赖升级，不升级到 MkDocs 2.x，也不自动升级 Material major。

## Configuration

- site_name 为 **PhD Research Notes**；site_url 和 repo_url 使用新建仓库的真实 GitHub Pages/仓库地址，没有虚构 domain。
- 手写层级 nav；Confidence Calibration、Efficient UQ、Representation-based UQ 放在 Uncertainty Quantification 下。所有指定主题均有入口。
- Material 原生 palette/toggle、TOC、搜索、导航、移动抽屉；不启用 blog、tabs 顶栏或大规模 CSS。
- font:false，避免默认 Google Fonts 请求；空 CSS 保留。
- Markdown extensions 为 tables、footnotes、admonition、details、highlight、inlinehilite、单一 superfences、arithmatex、tasklist、toc。
- 删除重复 superfences 和不用的 snippets、magiclink、emoji、md_in_html 等扩展，避免继承外部文件引用与作者配置。
- MathJax 3.2.2 固定 CDN URL；先载入本地配置，再载 renderer。未开启 instant navigation，每页普通加载和 typeset，不需要旧 document$ 回调。
- search/tags 使用内置 plugins；没有第三方 math/Mermaid/tags 插件。

## 实际功能验收

在本机浏览器访问 `http://127.0.0.1:8000/phd-research-notes/feature-test/`，不是仅检查 YAML。

| 项目 | 结果与证据 |
|---|---|
| Headings / TOC | 测试页显示多级标题及右侧 TOC，点击 Mermaid 锚点定位到对应部分 |
| Tables / highlighting | 表格结构和代码块出现在浏览器；生成 HTML 为 highlight/span 标记，代码复制按钮存在 |
| Admonition / details | note 显示；点击 Expandable detail 后出现折叠内容 |
| Footnotes | 注脚编号、文末注脚和返回链接出现在页面 |
| Inline math | `$H(Y)$` 排版为公式；浏览器 MathJax 容器存在 |
| Display math | 熵公式含求和上下标，截图实际显示；总计 2 个 mjx-container |
| Mermaid | 截图确认 LLM Output → Uncertainty Estimation → Hallucination Detection 三节点和箭头；不是原始代码文本 |
| Tags | 页首显示 UQ、hallucination 两个标签 |
| Search index | 18 条索引记录覆盖 13 个 Markdown 页面；检查到 learned representations 等正文内容 |
| Search interaction | 实际键盘输入 uncertainty，显示 **5 matching documents**；点击结果到 UQ 页面且命中词高亮 |
| Light / Dark | 点击主题控件，body scheme 从 default 到 slate，再回 default |
| Hierarchical navigation | UQ 下显示 Calibration/Efficient/Representation 子项，链接可跳转 |
| Mobile basic | 390×844 viewport，正文适配、抽屉打开、点击 Calibration 子项成功；document clientWidth/scrollWidth 均 390，无页面横向溢出；测试后恢复默认 viewport |
| Public-only content | 12 个主题/首页占位页 + 1 个 synthetic fixture；未扫描/导入其他研究目录 |

Mermaid 初载异步，首次 AX 状态短暂显示代码；等待实际页面渲染后截图确认流程图。一次自动化 radio check 未触发成功，改用可见主题按钮后确认双向切换；一次程序化 fill 未触发搜索，真实键盘输入后确认结果。这些不被记为虚假的功能成功或未解决网站错误。

## Local build / serve

```powershell
uv sync --locked --no-dev
uv run --locked --no-dev mkdocs build
uv run --locked --no-dev mkdocs build --strict
uv run --locked --no-dev mkdocs serve -a 127.0.0.1:8000
```

- sync：exit 0；普通 build：exit 0；strict build：exit 0。
- serve：正常监听，首页/fixture HTTP 200。
- pip check：29 packages compatible；uv lock --check 通过。
- LICENSE 按 Git blob 验证不变；初次按原始磁盘 bytes 对比受 Windows CRLF checkout 影响，改用 Git 内容哈希后确认相同。
- 本地验证记录位于忽略的 `.tools/sync.log`、build.log、strict.log、local-results.json；不会发布到网站。

## GitHub Actions / Pages

工作流：main push → Python 3.12/uv 0.8.6 → locked sync → strict build → artifact → 仅 main 的 deploy job 发布 gh-pages。发布使用 MkDocs 已有依赖 ghp-import，不重新构建未经检查的站点。

全局 contents:read；只有 deploy job 获 contents:write。PR 触发 build，但 deploy 与产物上传明确排除 pull_request；未配置 pull_request_target，不将 PR 代码带入有写权限的部署 job。

- [实现提交的 Actions run](https://github.com/constantine617/phd-research-notes/actions/runs/34103185657)：**success**；build、locked sync、strict build、artifact upload/download 和 deploy 均成功。
- [对应 Pages build and deployment](https://github.com/constantine617/phd-research-notes/actions/runs/34103224921)：**success**。
- GitHub Pages API：status=built，source.branch=gh-pages，source.path=/，build_type=legacy。
- 线上 fixture HTTP **200**，响应含 Synthetic example、MathJax 3.2.2 URL 和标签；确认不是上游 demo。
- 新建仓库原本未启用 Pages，因此进行了必要的首次 source 设置；没有创建/修改 secrets、custom domain 或其他不必要设置。
- **真实 pull_request 事件未创建执行：Not Verified**。PR 不部署已按 job if/permissions 静态核查；不能将 main 成功说成 PR 实跑成功。

## Warnings and limitations

- Material 9.7.7 仍输出关于 MkDocs 2.0 的上游提示框；当前使用 1.6.1，普通/strict build 成功。没有未解释的 MkDocs WARNING/ERROR 项目诊断。上游提示不等于本报告独立认可其所有论断。
- uv 初次 lock 跳过 registry 中不支持的旧 egg 分发；首次 sync 的跨盘 hardlink 回退为 copy。安装成功，后续 locked sync 正常；不影响网站功能。
- Git 首次传输遇 TLS handshake 问题，使用单次 OpenSSL/HTTP1.1 重试成功；未关闭 TLS 验证、未更改全局 Git 配置。
- 本地日志脚本初次遇 GBK 输出编码异常，设置 PYTHONIOENCODING=utf-8 后重跑完成；不是 MkDocs 构建失败。
- MathJax/Mermaid 仍依赖 CDN，离线渲染和 CDN 故障回退 **Not Verified**。
- 浏览器打开公网测试页的一次导航超时；公网 HTTP/响应内容已验，但**公网浏览器逐项交互重测 Not Verified**。数学/Mermaid/搜索/主题/mobile 的交互证据来自同一实现的本地站。
- 多浏览器、真实手机、完整无障碍、1000+ 页面性能、复杂公式与图表、长期依赖升级不在本次基础验收范围，**Not Verified**。

## 用户可确认的配置

当前没有阻止使用的待填字段。仓库名称 `phd-research-notes`、site_name `PhD Research Notes` 为合理默认；可在下一阶段确认是否改名。site_url/repo_name/repo_url 是真实值。未设置个人头像、专属 logo/favicon、真实姓名或自定义域名，保留 Material 默认资源即可使用。

## 下一阶段与停止条件

仅在用户审核后，讨论公开笔记的写作规范、少量 paper-note 模板及是否需要 BibTeX；不自动创建论文库或导入 PhD 内容。本次不进入 Phase 3。

**Phase 2 PASS。** 上述未验证项是明确的测试边界，不是隐藏的构建/部署失败。  
