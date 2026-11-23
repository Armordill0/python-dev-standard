# 会社のAI（Kilo Code / Claude Code / Cline）に貼るプロンプト

既存の `AGENTS.md` やフォルダ構成があるプロジェクトで、チャット欄に以下をそのまま貼り付けると、不足しているファイルや設定だけを差分整備してくれます。

````markdown
既存のPython（およびNode/Web併用）開発環境を、チーム共通の「低トークン・高再現性・GitHub Actions自動テスト標準仕様」へアップデートしてください。
既存のコードや `AGENTS.md` の固有ルールは壊さず、以下の構成・ファイルを点検し、不足しているものを追加・統合してください。

### 1. エージェント指示書の正本化と3ツール橋渡し
- ルールの正本はリポジトリ直下の `AGENTS.md` に一本化する。
- VSCodeの各AI拡張が必ず `AGENTS.md` を読むよう、以下の3つの橋渡しファイルを作成（または追記）する。ルール本文はコピーせず「`AGENTS.md` を読むこと」と最小限の導線だけを書くこと：
  1. Claude Code用：`CLAUDE.md`
  2. Cline用：`.clinerules`
  3. Kilo Code用：`.kilocode/rules/guidelines.md`

### 2. 4大フォルダ構成の整備とルート直下の整理（`config/`・`Docs/`・`tests/`・`src/`）
- **ルート直下のルール**：リポジトリのルート直下には原則としてコア骨格（`config/`, `Docs/`, `tests/`, `src/`, `.github/`, `.gitignore`, `AGENTS.md`, `pytest.ini`, 起動バッチ等）のみを置く。ツール固有のWebフロントエンド（`vite.config.ts`, `package.json`, `templates/` 等）・デプロイ設定（`Dockerfile` 等）・補助スクリプト群がルート直下にある場合は、それぞれ `src/user/web/`, `src/user/scripts/`, `config/deploy/` 等へ振り分けてルートを綺麗に保つ。
- `config/`
  - 設定ファイル（JSON：`app_setting.sample.json` と Git除外の `app_setting.json`）
  - `config/requirements.txt`：必要なpipライブラリ（`pytest` を含む）をバージョン固定（`pkg==x.y.z`）で漏れなく記述する（ライブラリ実体はGitに入れない）。
- `Docs/`
  - `Docs/README.md`：全ドキュメントへの目次
  - `Docs/for_ai/project/PROJECT_STATUS.md`：必須の開発資料（英語で記載し、以下の8項目を見出しとして必ず含める）
    1. What We Want to Do / 2. Current State Analysis / 3. Problem / Objective Definition / 4. Targets & Rationale / 5. Approach & Rationale / 6. Schedule / 7. Current Progress / 8. Next Actions
  - `Docs/for_ai/project/concept/`（構想資料）、`Docs/for_ai/project/references/`（参考文献・外部仕様）
  - `Docs/for_ai/`（AI向け英語仕様書。スクリプトと1対1で `<script_name>.md` を作成し、「1. What It Does / 2. How to Use / 3. Dependencies & Config Keys / 4. Associated Tests / 5. Notes & Constraints」を記載。AIはソースを開く前に必ずこれを読む）
  - `Docs/for_developers/`（開発者向け日本語解説）
- `src/`
  - `src/main.py`（エントリポイント）、`src/core/`（共通部品）、`src/user/`（ツール固有処理。Webや補助スクリプトは `src/user/web/` 等に格納）
  - 依存の向きは `src/main.py` -> `src/user/` -> `src/core/` の単方向のみとする。

### 3. CI / 自動テストルール（GitHub Actions・ヘッドレス前提）
1. **テスト実行環境の前提（ヘッドレス・クラウド前提）**
   - テストコードは手元のPCだけでなく、**GitHub Actions（Linux/Ubuntu仮想マシン上、GUIなしのヘッドレス環境）** で自動実行されることを前提に設計すること。
   - ローカル固有の絶対パスや、手元のPCにしかない外部ファイル・認証情報・環境変数に直接依存しないコードにすること（必要な設定値は相対パス・環境変数・モックで代替可能にすること）。
2. **コア言語ごとのテスト・依存関係標準化**
   - **Python Core**：テストフレームワークは **`pytest`** で統一し、リポジトリ直下で `pytest` コマンドを実行するだけで全テストが一括実行できるようにすること（`pytest.ini` または `pyproject.toml` を設定）。外部ライブラリは必ず `config/requirements.txt`（または `requirements.txt` / `pyproject.toml`）に漏れなく記載すること。
   - **Node Core（TypeScript / JavaScript）**：テストフレームワークは **Vitest** または **Jest** に統一し、`npm test`（`CI=true npm test`）で対話プロンプトなしで全テストが完了・終了するようにすること。依存パッケージは `package.json` および `package-lock.json` に正確に記録すること。
3. **GitHub Actions ワークフロー（`.github/workflows/test.yml`）の標準装備**
   - 新規プロジェクト作成時や機能追加時は、`push` および `pull_request` をトリガーにテストを自動実行する `.github/workflows/test.yml` を必ず作成・維持すること。
   - ワークフロー内には、仮想マシン上での「依存パッケージのインストール（`pip install` / `npm ci`）」と「テストの一括実行（`pytest` / `CI=true npm test`）」のステップを必ず含めること。
4. **低コスト検査（`tests/README.md`）**
   - `tests/blackbox/test_<script_name>.py`（スクリプト単位）と `tests/integration/test_<feature_name>.py`（機能単位）を維持し、テスト条件・コマンド（`smoke` / `full`）・期待結果を `tests/README.md` の表に集約すること。

### 作業手順
1. 現在のフォルダ構成と `AGENTS.md` を確認し、上記との差分をリストアップする。
2. 不足しているフォルダ・ひな型ファイル（橋渡しファイル3種、`Docs/README.md`、`Docs/for_ai/project/PROJECT_STATUS.md`、`tests/README.md`、`config/requirements.txt`、`pytest.ini`、`.github/workflows/test.yml`）を作成し、`AGENTS.md` に上記ルールを統合する。
3. 完了後、作成・更新したファイル一覧を日本語で報告する。
````
