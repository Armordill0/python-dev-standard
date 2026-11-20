# AGENTS.md — Canonical AI Instructions (Python & Node/Web Tool Environment)

> Start by reading `docs/README.md`, `docs/for_ai/project/PROJECT_STATUS.md`, `docs/for_ai/README.md`, `docs/for_ai/SETUP.md`, `docs/for_ai/AI_WORKING_NOTES.md`, `docs/for_ai/rules/coding_rules.md`, and `tests/README.md` before modifying code.

## 1. Core Directives
1. **Chat Output**: Reply to the user in natural Japanese.
2. **Code & AI Docs**: Write code, comments, `docs/for_ai/project/PROJECT_STATUS.md`, `tests/README.md`, and `docs/for_ai/**` in English only. Human docs in `docs/for_developers/` are written in Japanese.
3. **Runtime & Libraries**:
   - Local Windows execution uses `.embeddablepython/python.exe` (never create `.venv` locally).
   - GitHub Actions CI executes on headless Linux (`ubuntu-latest`) using standard Python / Node runners.
   - Keep `python_version` and `core_version` in `config/core/library_info.json`.
   - Declare all tool-specific Python pip dependencies exclusivamente in `config/requirements.txt` (and core dependencies in `config/core/requirements.lock`); never place `requirements.txt` or `pyproject.toml` at the repository root.
4. **Error Dialog Suppression**: Always pass `--show_error_dialog false` when running a tool from AI or CI.
5. **Code Placement & Clean Root**:
   - Keep the repository root strictly aligned with the 4-folder skeleton (`config/`, `docs/`, `tests/`, `src/`).
   - Place tool business logic ONLY inside `src/user/` (put web frontends in `src/user/web/`) and between `# ここからメイン処理` and `# ここまでメイン処理` in `src/main.py`.
6. **Dependency Direction**: `src/main.py` -> `src/user/` -> `src/core/` only. Read `docs/for_ai/` per-script specs before opening full `.py` or `.ts` source files.
7. **Mandatory Project Documentation (`docs/for_ai/project/PROJECT_STATUS.md`)**:
   - Maintain 8 required sections: (1) What We Want to Do, (2) Current State Analysis, (3) Problem / Objective Definition, (4) Targets & Rationale, (5) Approach & Rationale, (6) Schedule, (7) Current Progress, (8) Next Actions.
   - Store concept notes in `docs/for_ai/project/concept/` and references in `docs/for_ai/project/references/` (link heavy files from Google Drive).
   - When asked about project status/goals/progress in Japanese, read `PROJECT_STATUS.md` and answer concisely in Japanese.

## 2. CI / Automated Testing Rules (GitHub Actions First)
1. **Headless & Cloud Execution Prerequisite (Cross-OS Compatibility)**:
   - Design all tests to run automatically on **GitHub Actions (Linux / Ubuntu VM, headless environment without GUI)** as well as local Windows machines.
   - NEVER depend on local-only absolute paths (e.g., `C:\...`, `D:\...`), machine-specific external files, personal credentials, or unmocked host environment variables.
   - Always use `pathlib.Path` for file path operations to prevent Windows/Linux separator bugs.
   - For Windows-only features (Win32 APIs, native GUI dialogs, etc.), mock the behavior in tests or guard with `@pytest.mark.skipif(sys.platform != "win32", reason="Windows only")`.
2. **Test Framework & Dependency Standardization by Stack**:
   - **Python Core**:
     - Standardize on **`pytest`**. Running `pytest` at the repository root must execute the entire Python test suite in one shot.
     - Always configure `PYTHONPATH=src` (or `pythonpath = src` in `pytest.ini`) so imports under `src/` resolve without error.
     - Declare all required external pip libraries (including `pytest`) with pinned versions (`package==x.y.z`) in `config/requirements.txt` (and `config/core/requirements.lock` for core).
   - **Node Core (TypeScript / JavaScript / `src/user/web/`)**:
     - Standardize on **Vitest** or **Jest**. Running `CI=true npm test` (or `npm test`) must execute and terminate all tests non-interactively without watch prompts.
     - Record all dependencies accurately in `package.json` and `package-lock.json` for reproducible `npm ci` installs.
3. **Mandatory GitHub Actions Workflow (`.github/workflows/test.yml`)**:
   - On project initialization or feature addition, always create and maintain `.github/workflows/test.yml` triggered on `push` and `pull_request`.
   - The workflow must dynamically read `python_version` from `config/core/library_info.json` to match the CI Python version, install dependencies (`pip install -r config/requirements.txt` / `config/core/requirements.lock` and `npm ci`), and run `PYTHONPATH=src pytest` (plus `CI=true npm test` if Node is used).
4. **Test Structure & Low-Cost Catalog (`tests/README.md`)**:
   - Maintain per-script black-box tests (`tests/blackbox/test_<script_name>.py`) and per-feature integration tests (`tests/integration/test_<feature_name>.py`).
   - Record all test cases, conditions, run commands (`smoke` and `full`), and expected outputs in `tests/README.md`.

## 3. Release, Distribution & Central Registry Flow
- Run `src/core/dev_manager/release_tool.py` when requested to package or release a tool:
  1. It validates syntax/secrets, bumps semantic version (`x.y.z`), and builds a minimal end-user distribution ZIP locally (excluding development-only files such as `tests/`, `docs/for_ai/`, `.git/`, `.github/`, `.vscode/`).
  2. It outputs the metadata payload required to update the organization's central `.github` repository registry (`profile/README.md` and `registry.json`).
  3. End-user distribution ZIPs are uploaded to Google Drive manually by a human operator (do NOT use automated Google Drive upload APIs).
