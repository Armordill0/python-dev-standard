# AGENTS.md — Canonical AI Instructions (Python Environment)

> Start by reading `docs/README.md`, `docs/for_ai/project/PROJECT_STATUS.md`, `docs/for_ai/README.md`, and `tests/README.md` before modifying code.

## 1. Language & Output Rules
- **Chat Output to User**: Natural Japanese ONLY.
- **Code & AI Docs (`*.py`, `AGENTS.md`, `docs/for_ai/**`, `tests/README.md`, commits)**: English ONLY.
- **Developer Docs (`docs/for_developers/**`)**: Japanese ONLY.
- **Proper Nouns**: Never translate Japanese proper nouns (file, folder, or sheet names).

## 2. Runtime & Library Management
- **Runtime**: Use `.embeddablepython/python.exe` only (if present). Never create `.venv` when using the embeddable runtime.
- **Dependencies**: Declare required pip libraries with pinned versions (`package==x.y.z`) in `config/requirements.txt`. `setup_script.py` checks the SHA-256 digest on startup and installs updates automatically.
- **Dialog Suppression**: Always pass `--show_error_dialog false` when running the tool from an AI terminal.

## 3. Directory Layout & Dependency Direction
- **4 Core Folders (Keep Root Clean)**:
  - `config/`: JSON settings (`app_setting.sample.json`, `app_setting.json`), `requirements.txt`, and deployment configs (`config/deploy/`)
  - `docs/`: `README.md`, `for_ai/` (EN specs, `project/PROJECT_STATUS.md` with 8 mandatory sections, `project/concept/`, `project/references/`), `for_developers/` (JA guides)
  - `tests/`: `README.md` (commands & expected results), `blackbox/` (per-script), `integration/` (per-feature)
  - `src/`: `main.py` (entry point), `core/` (shared modules), `user/` (tool-specific modules; place web/extra assets under `src/user/web/` or `src/user/scripts/`)
- **Dependency Direction**: `src/main.py` -> `src/user/` -> `src/core/` only.
- **Code Placement**: Place tool business logic ONLY in `src/user/` and between `# ここからメイン処理` and `# ここまでメイン処理` in `src/main.py`.

## 4. Token Conservation (Docs & Testing)
- **Read Specs First**: Read `docs/for_ai/<folder>/<script>.md` (1. What It Does, 2. How to Use, 3. Dependencies & Config Keys, 4. Associated Tests, 5. Notes) before reading full `.py` source files.
- **Low-Cost Test Verification**:
  - Keep test filenames simple (`tests/blackbox/test_<script>.py`, `tests/integration/test_<feature>.py`).
  - Document all test conditions, commands (`smoke` and `full`), and expected outputs in `tests/README.md`.
  - After every code change, run the `smoke` command from `tests/README.md` and check the summary output instead of re-reading test code.
