# AGENTS.md — Canonical AI Instructions (Python & Node/Web Environment)

> Start by reading `docs/README.md`, `docs/for_ai/project/PROJECT_STATUS.md`, `docs/for_ai/README.md`, and `tests/README.md` before modifying code.

## 1. Language & Output Rules
- **Chat Output to User**: Natural Japanese ONLY.
- **Code & AI Docs (`*.py`, `*.ts`, `AGENTS.md`, `docs/for_ai/**`, `tests/README.md`, commits)**: English ONLY.
- **Developer Docs (`docs/for_developers/**`)**: Japanese ONLY.
- **Proper Nouns**: Never translate Japanese proper nouns (file, folder, or sheet names).

## 2. Runtime & Library Management
- **Local Python Runtime**: Use `.embeddablepython/python.exe` only (if present). Never create `.venv` when using the embeddable runtime.
- **CI Runtime**: GitHub Actions executes on headless Linux (`ubuntu-latest`) using standard Python / Node runners.
- **Python Dependencies**: Declare all required pip libraries (including `pytest`) with pinned versions (`package==x.y.z`) in `config/requirements.txt` (or `requirements.txt` / `pyproject.toml`). `setup_script.py` checks the SHA-256 digest on startup and installs updates automatically.
- **Node / Web Dependencies**: Record all packages accurately in `package.json` and `package-lock.json` for reproducible `npm ci` installs.
- **Dialog Suppression**: Always pass `--show_error_dialog false` when running the tool from an AI terminal or CI.

## 3. Directory Layout & Dependency Direction
- **4 Core Folders (Keep Root Clean)**:
  - `config/`: JSON settings (`app_setting.sample.json`, `app_setting.json`), `requirements.txt`, and deployment configs (`config/deploy/`)
  - `docs/`: `README.md`, `for_ai/` (EN specs, `project/PROJECT_STATUS.md` with 8 mandatory sections, `project/concept/`, `project/references/`), `for_developers/` (JA guides)
  - `tests/`: `README.md` (commands & expected results), `blackbox/` (per-script), `integration/` (per-feature)
  - `src/`: `main.py` (entry point), `core/` (shared modules), `user/` (tool-specific modules; place web/extra assets under `src/user/web/` or `src/user/scripts/`)
- **Dependency Direction**: `src/main.py` -> `src/user/` -> `src/core/` only.
- **Code Placement**: Place tool business logic ONLY in `src/user/` and between `# ここからメイン処理` and `# ここまでメイン処理` in `src/main.py`.

## 4. CI / Automated Testing & Token Conservation (GitHub Actions First)
1. **Headless & Cloud Execution Prerequisite**:
   - Design all tests to run automatically in **GitHub Actions (Linux / Ubuntu VM, headless environment without GUI)** as well as on local PCs.
   - NEVER depend on local-only absolute paths (e.g., `C:\...`, `D:\...`), machine-specific external files, personal credentials, or unmocked host environment variables. Use relative paths, environment variables, or mocks/fixtures.
2. **Standardized Test Runners by Stack**:
   - **Python Core**: Standardize on **`pytest`**. Running `pytest` at the repository root must execute the entire Python test suite in one shot. List all dependencies in `config/requirements.txt` (or `requirements.txt` / `pyproject.toml`).
   - **Node Core (TypeScript / JavaScript)**: Standardize on **Vitest** or **Jest**. Running `CI=true npm test` (or `npm test`) must complete and exit non-interactively without watch prompts.
3. **Mandatory GitHub Actions Workflow (`.github/workflows/test.yml`)**:
   - On project creation or feature addition, always create and maintain `.github/workflows/test.yml` triggered on `push` and `pull_request`, containing dependency installation (`pip install` / `npm ci`) and batch test execution (`pytest` / `CI=true npm test`).
4. **Low-Token Reading & Verification**:
   - Read `docs/for_ai/<folder>/<script>.md` before opening full source files.
   - Keep test filenames simple (`tests/blackbox/test_<script>.py`, `tests/integration/test_<feature>.py`) and document all test conditions, `smoke`/`full` commands, and expected outputs in `tests/README.md`.
