# 会社環境向け Python チーム開発標準 再現ガイド（1ファイル完結版）

> ### 👤 使い方（VSCode：Kilo Code / Claude Code / Cline 対応）
> このファイル **1 枚だけ** を会社のプロジェクトフォルダに置き、VSCode の AI 拡張（Kilo Code / Claude Code / Cline）のチャット欄で次のように指示するだけで、最新の標準環境をまるごと構築・コンバートできます。
>
> **「`COMPANY_PYTHON_ENV_GUIDE.md` の内容に従って、このプロジェクトのフォルダ構成・ルール・ドキュメント・CI テスト環境を整備してください（既存の動作や固有ロジックは壊さないこと）。」**

---

## 1. 標準フォルダ構成（ルート直下を綺麗に統一）

```text
<ProjectRoot>/
├── AGENTS.md                        # 条件付き目次型ルーター（AIルールの正本）
├── CLAUDE.md                        # Claude Code 用橋渡し（AGENTS.md へ誘導）
├── .clinerules                      # Cline 用橋渡し（AGENTS.md へ誘導）
├── .kilocode/rules/guidelines.md    # Kilo Code 用橋渡し（AGENTS.md へ誘導）
├── .github/workflows/test.yml       # ヘッドレス自動テストCI（GitLabなら .gitlab-ci.yml）
├── .gitattributes                   # 改行コード固定（CRLF/LF事故防止）
├── .gitignore                       # 実行環境・キャッシュ・機密ファイル除外
├── pytest.ini                       # リポジトリ直下で pytest 一括実行
├── Settings/                        # 設定・依存一覧
│   ├── Core/                        # 共通基盤バージョン情報（library_info.json / requirements.lock）
│   ├── requirements.txt             # pip ライブラリ一覧（バージョン固定、pytest含む）
│   ├── app_setting.sample.json      # 設定ひな型（Git管理）
│   └── app_setting.json             # 実設定（Git除外）
├── Data/                            # マスター・保存・ログデータ（.gitkeep のみ管理）
│   ├── log/.gitkeep
│   ├── save/.gitkeep
│   └── temp/.gitkeep
├── docs/
│   ├── README.md                    # 全ドキュメント目次
│   ├── for_ai/                      # AI向け（英語・低トークン）
│   │   ├── README.md
│   │   ├── SETUP.md                 # 環境構築・リリース手順
│   │   ├── project/
│   │   │   ├── PROJECT_STATUS.md    # 必須8項目の開発資料
│   │   │   ├── concept/.gitkeep     # 構想資料
│   │   │   └── references/.gitkeep  # 参考文献
│   │   ├── rules/coding_rules.md    # 規約・メモリ効率・信頼性ルール
│   │   ├── core/                    # Scripts/Core/ の1対1仕様書
│   │   └── user/                    # Scripts/User/ の1対1仕様書
│   └── for_developers/              # 人間向け解説（日本語）
├── Scripts/
│   ├── main.py                      # エントリポイント
│   ├── Core/                        # 汎用共通基盤（どのツールでも使い回せる部品）
│   └── User/                        # ツール固有処理
│       ├── Common/                  # ツール内共通部品
│       └── <feature_or_web>/        # 機能別フォルダ（Webフロントは Scripts/User/web/）
└── Tests/
    ├── README.md                    # テスト一覧・smoke/fullコマンド・結合テスト基準
    ├── conftest.py                  # 共通フィクスチャ・互換シム
    ├── blackbox/                    # スクリプト単位（test_<script>.py）
    └── integration/                 # 機能単位（test_<feature>.py）
```

---

## 2. エージェントルール正本＆橋渡しファイル

### `AGENTS.md`（条件付き目次型ルーター：正本）
```markdown
# AGENTS.md — Python & Node/Web Workspace Router

> **Progressive Disclosure Rule**: Do NOT read all documentation upfront. Read ONLY the specific file listed below for your current task.

## 1. Core Invariants
- **Language & Git**: Chat in natural Japanese; code, commits, `AGENTS.md`, `docs/for_ai/**`, and `Tests/README.md` in English; `docs/for_developers/**` in Japanese. Run `git pull` before editing. Use topic branches (`feat/*`, `fix/*`) + Pull Request instead of direct push to `main`. Never commit secrets.
- **Clean Root**: Keep root strictly aligned to `Settings/`, `Data/`, `docs/`, `Tests/`, and `Scripts/` (plus `.github/`, `.vscode/`, `.gitignore`, `.gitattributes`, `AGENTS.md`, `pytest.ini`, bridge files). Place web/extra files under `Scripts/User/web/` or `Settings/`.
- **Code & Dependency Boundary**: Place shared logic in `Scripts/Core/` and tool-specific logic ONLY in `Scripts/User/` (`Common/` + per-feature subfolders) and `Scripts/main.py`. Dependency direction: `Scripts/main.py` -> `Scripts/User/` -> `Scripts/Core/` only.
- **Runtime & Dependencies**: Declare all pip packages (including `pytest`) with pinned versions in `Settings/requirements.txt` (never at repo root). Always pass `--show_error_dialog false` in CLI/CI runs.

## 2. On-Demand Task Routing Table
| Current Task | Files to Read On-Demand |
| :--- | :--- |
| **1. Understand goals, architecture, or progress** | `docs/for_ai/project/PROJECT_STATUS.md` (and `docs/README.md` for index) |
| **2. Inspect or modify code (`Scripts/`)** | Target spec `docs/for_ai/{core,user}/<script>.md` FIRST, then `docs/for_ai/rules/coding_rules.md`, then target `.py`/`.ts` |
| **3. Run, add, or fix tests / CI** | `Tests/README.md` and `.github/workflows/test.yml` |
| **4. Setup environment, dependencies, or release** | `docs/for_ai/SETUP.md` |
```

### VSCode 各AI拡張の橋渡しファイル（ルール本文は重複させない）
* **Claude Code（`CLAUDE.md`）**：
```markdown
# CLAUDE.md — Claude Code Entry Point
Read and follow `@AGENTS.md` as the single canonical router. Do NOT read all docs upfront; follow its On-Demand Task Routing Table.
```
* **Cline（`.clinerules`）**：
```markdown
# .clinerules — Cline Entry Point
1. Read `AGENTS.md` at the repository root as the single canonical router.
2. Follow its On-Demand Task Routing Table instead of scanning all source/test files.
```
* **Kilo Code（`.kilocode/rules/guidelines.md`）**：
```markdown
# Kilo Code Guidelines — Entry Point
1. Treat `AGENTS.md` at the repository root as the single canonical router.
2. Read ONLY the sub-documents required by the On-Demand Task Routing Table.
```

---

## 3. 下層ルール・ドキュメントひな型

### `docs/for_ai/rules/coding_rules.md`（規約・メモリ効率・信頼性）
```markdown
# Coding, Reliability & Architecture Rules (Python)

## 1. Architecture & Formatting
| Rule | Specification |
| :--- | :--- |
| Dependency Direction | `Scripts/main.py` -> `Scripts/User/` -> `Scripts/Core/` only |
| Class Layout | 1 Feature = 1 File = 1 Main Class (`Scripts/User/`) |
| Per-Script Spec (`docs/for_ai/`) | 1-to-1 `.md` spec per script: 1. What It Does, 2. How to Use, 3. Dependencies & Config Keys, 4. Associated Tests, 5. Notes |
| Comments & Docstrings | English only; NO half-width parentheses `()` (use `（）` or `［］`) |
| Semver Rule | `x.y.z`: initial `1.0.0`, feature `y+1, z=0`, fix/comment `z+1` |

## 2. Memory Efficiency, Resource Safety & Reliability
| Area | Mandatory Rule |
| :--- | :--- |
| **Streaming & Memory** | Never load large datasets/logs/archives entirely into memory; use generators, iterators, or chunked reads (`chunksize`). |
| **Resource Cleanup** | Always wrap files, DB sessions, SSH/SFTP clients, HTTP sessions, and subprocesses in `with` context managers or `try...finally`. |
| **Timeouts & Retries** | Every external network/IPC call (HTTP, DB, SMTP, SSH, subprocess) MUST specify an explicit `timeout` (e.g., `timeout=10`). Use bounded retries for transient errors. |
| **Atomic File Writes** | Write state/config/save files to a temporary file in the same directory and commit via `os.replace()` to prevent corrupted/zero-byte files on crash. |
| **Cross-OS Compatibility** | Use `pathlib.Path` for all paths (no hardcoded `C:\` or `\`). Mock Windows-only APIs in tests or mark with `@pytest.mark.skipif(sys.platform != "win32", reason="Windows only")`. |
| **Project Docs & Conflict Prevention** | Maintain 8-section `docs/for_ai/project/PROJECT_STATUS.md` at milestone/PR boundaries to minimize merge conflicts. Enforce LF/CRLF via `.gitattributes`. |
```

### `docs/for_ai/project/PROJECT_STATUS.md`（必須8項目）
```markdown
# Project Status

## 1. What We Want to Do
## 2. Current State Analysis
## 3. Problem / Objective Definition
## 4. Targets & Rationale
## 5. Approach & Rationale
## 6. Schedule
## 7. Current Progress
## 8. Next Actions
```

### `Tests/README.md`（低トークンテスト一覧＋結合テスト基準）
```markdown
# Test Catalog & Low-Cost Verification Guide

> **For AI Agents**: Do NOT read all test source files to verify behavior. Run the commands below. All tests must pass on headless Linux CI without local paths or credentials.

## 1. Quick Execution Commands
| Tier | When to Run | Command | Expected Output |
| :--- | :--- | :--- | :--- |
| **Smoke (Syntax)** | After every `.py` edit | `python -m compileall -q Scripts Tests` | Exit code `0`, no output |
| **Full (Pytest / CI)** | Before commit / PR / release | `pytest` | Exit code `0`, all passed |
| **Node / Web (If present)** | Before commit / PR / release | `CI=true npm --prefix Scripts/User/web test` | Exit code `0`, all passed |

## 2. Black-Box Tests (`Tests/blackbox/test_<script>.py`)
| Test File | Target Script | Verified Conditions & Cases | Expected Result |
| :--- | :--- | :--- | :--- |
| `Tests/blackbox/test_<script>.py` | `Scripts/User/<script>.py` | Normal, boundary, and error cases | All passed |

## 3. Integration Tests (`Tests/integration/test_<feature>.py`)
| Test File | Target Feature | Verified Scenario | Expected Result |
| :--- | :--- | :--- | :--- |
| `Tests/integration/test_<feature>.py` | End-to-end feature flow | Multi-module pipeline | All passed |

## 4. Integration Test Design Criteria
1. **Boundary & Pipeline Scope**: Verify end-to-end data flow across `Scripts/main.py` -> `Scripts/User/*` -> `Scripts/Core/*`.
2. **Real I/O vs. Mock Boundary**: Use real local filesystem I/O inside `tmp_path` / `TemporaryDirectory` and SQLite/in-memory DB; mock only external network APIs (HTTP/SMTP/Cloud/DB servers) and external binaries.
3. **Failure Injection & Rollback**: Test missing config keys, malformed inputs, and simulated timeouts/errors to verify clean recovery and non-zero exit codes.
4. **Zero Resource Leak**: Assert all file handles, connections, and threads close cleanly with no leftover files outside the temporary directory.
```

---

## 4. CI・Pytest・Git 設定ひな型

### `pytest.ini`
```ini
[pytest]
testpaths = Tests
pythonpath = Scripts
addopts = -q
```

### `Settings/requirements.txt`
```text
# Pinned Python dependencies (installed via pip install -r Settings/requirements.txt)
pytest==8.3.4
```

### `.gitattributes`
```text
* text=auto eol=lf
*.bat text eol=crlf
*.cmd text eol=crlf
*.ps1 text eol=crlf
*.py text eol=lf
*.ts text eol=lf
*.tsx text eol=lf
*.js text eol=lf
*.json text eol=lf
*.yml text eol=lf
*.yaml text eol=lf
*.md text eol=lf
*.sh text eol=lf
```

### `.github/workflows/test.yml`（GitHub Actions 用／GitLab CI の場合は同等の `script:` へ移植）
```yaml
name: Automated Tests

on:
  push:
    branches: [ main, master ]
  pull_request:
    branches: [ main, master ]

jobs:
  test-python:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Read Python version
        id: pyver
        run: |
          if [ -f Settings/Core/library_info.json ]; then
            VER=$(python3 -c "import json; print(json.load(open('Settings/Core/library_info.json')).get('python_version', '3.13'))")
          else
            VER="3.13"
          fi
          echo "version=$VER" >> "$GITHUB_OUTPUT"
      - id: setup_requested
        continue-on-error: true
        uses: actions/setup-python@v5
        with:
          python-version: ${{ steps.pyver.outputs.version }}
          allow-prereleases: true
      - if: steps.setup_requested.outcome == 'failure'
        uses: actions/setup-python@v5
        with:
          python-version: "3.13"
      - if: steps.setup_requested.outcome == 'failure'
        run: echo "::warning::Requested Python ${{ steps.pyver.outputs.version }} unavailable; using $(python --version)"
      - name: Install dependencies
        run: |
          python -m pip install --upgrade pip
          python -m pip install pytest
          if [ -f Settings/Core/requirements.lock ]; then python -m pip install -r Settings/Core/requirements.lock; fi
          if [ -f Settings/requirements.txt ]; then python -m pip install -r Settings/requirements.txt; fi
      - name: Run pytest
        env:
          PYTHONPATH: Scripts
        run: pytest
```
