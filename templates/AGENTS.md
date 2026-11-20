# AGENTS.md — Python & Node/Web Workspace Router

> **Progressive Disclosure Rule**: Do NOT read all documentation upfront. Read ONLY the specific file listed below for your current task.

## 1. Core Invariants
- **Clean 4-Folder Root**: Keep the repository root strictly aligned to `config/`, `docs/`, `tests/`, and `src/` (plus `.github/`, `.vscode/`, `.gitignore`, `.gitattributes`, `AGENTS.md`, `pytest.ini`, `実行.bat`). Place web/extra files under `src/user/web/` or `config/`.
- **Code & Dependency Boundary**: Place tool logic ONLY in `src/user/` and between `# ここからメイン処理` and `# ここまでメイン処理` in `src/main.py`. Dependency direction: `src/main.py` -> `src/user/` -> `src/core/` only.
- **Runtime & CLI Safety**: Use `.embeddablepython/python.exe` locally (never create `.venv`) and always pass `--show_error_dialog false`. Declare tool pip packages exclusively in `config/requirements.txt`.

## 2. On-Demand Task Routing Table
| Current Task | Files to Read On-Demand |
| :--- | :--- |
| **1. Understand goals, architecture, or progress** | `docs/for_ai/project/PROJECT_STATUS.md` (and `docs/README.md` for index) |
| **2. Inspect or modify code (`src/`)** | Target spec `docs/for_ai/{core,user}/<script>.md` FIRST, then `docs/for_ai/rules/coding_rules.md`, then target `.py`/`.ts` |
| **3. Run, add, or fix tests / CI** | `tests/README.md` and `.github/workflows/test.yml` |
| **4. Setup environment, dependencies, or release** | `docs/for_ai/SETUP.md` (and `src/core/dev_manager/release_tool.py` when releasing) |
