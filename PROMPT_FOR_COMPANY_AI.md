# 会社のAI（Kilo Code / Claude Code / Cline）に貼るプロンプト

既存の `AGENTS.md` やフォルダ構成があるプロジェクトで、チャット欄に以下をそのまま貼り付けると、不足しているファイルや設定だけを差分整備してくれます。

````markdown
既存のPython開発環境を、チーム共通の「低トークン・高再現性標準仕様」へアップデートしてください。
既存のコードや `AGENTS.md` の固有ルールは壊さず、以下の構成・ファイルを点検し、不足しているものを追加・統合してください。

### 1. エージェント指示書の正本化と3ツール橋渡し
- ルールの正本はリポジトリ直下の `AGENTS.md` に一本化する。
- VSCodeの各AI拡張が必ず `AGENTS.md` を読むよう、以下の3つの橋渡しファイルを作成（または追記）する。ルール本文はコピーせず「`AGENTS.md` を読むこと」と最小限の導線だけを書くこと：
  1. Claude Code用：`CLAUDE.md`
  2. Cline用：`.clinerules`
  3. Kilo Code用：`.kilocode/rules/guidelines.md`

### 2. 4大フォルダ構成の整備とルート直下の整理（`config/`・`docs/`・`tests/`・`src/`）
- **ルート直下のルール**：リポジトリのルート直下には原則としてコア骨格（`config/`, `docs/`, `tests/`, `src/`, `.gitignore`, `AGENTS.md`, 起動バッチ等）のみを置く。ツール固有のWebフロントエンド（`vite.config.ts`, `package.json`, `templates/` 等）・デプロイ設定（`Dockerfile` 等）・補助スクリプト群がルート直下にある場合は、それぞれ `src/user/web/`, `src/user/scripts/`, `config/deploy/` 等へ振り分けてルートを綺麗に保つ。
- `config/`
  - 設定ファイル（JSON：`app_setting.sample.json` と Git除外の `app_setting.json`）
  - `config/requirements.txt`：必要なpipライブラリをバージョン固定（`pkg==x.y.z`）で記述する。起動スクリプト（`setup_script.py` 等）でSHA-256ハッシュを比較し、変更時のみ自動 `pip install -r config/requirements.txt` する仕組みにする（ライブラリ実体はGitに入れない）。
- `docs/`
  - `docs/README.md`：全ドキュメントへの目次
  - `docs/for_ai/project/PROJECT_STATUS.md`：必須の開発資料（英語で記載し、以下の8項目を見出しとして必ず含める）
    1. What We Want to Do（やりたいこと・動機）
    2. Current State Analysis（現状分析・課題）
    3. Problem / Objective Definition（解決する目的）
    4. Targets & Rationale（目標と、他案ではなくその目標にした経緯）
    5. Approach & Rationale（進め方と、不採用案を含む選定経緯）
    6. Schedule（マイルストーンと期限）
    7. Current Progress（完了・進行中・未着手）
    8. Next Actions（今後の予定）
  - `docs/for_ai/project/concept/`（構想資料）、`docs/for_ai/project/references/`（参考文献・外部仕様）
  - `docs/for_ai/`（AI向け英語仕様書。スクリプトと1対1で `<script_name>.md` を作成し、「1. What It Does / 2. How to Use（引数・戻り値・最小コード例） / 3. Dependencies & Config Keys / 4. Associated Tests / 5. Notes & Constraints」を記載。AIはソースを開く前に必ずこれを読む）
  - `docs/for_developers/`（開発者向け日本語解説）
- `tests/`
  - `tests/blackbox/test_<script_name>.py`（スクリプト単位のブラックボックステスト：必須）
  - `tests/integration/test_<feature_name>.py`（機能単位の結合テスト：必須）
  - `tests/README.md`：トークン節約のため、テストファイル名はシンプル（`test_<対象>.py`）に保ち、テストの条件・使い方（数秒で終わる `smoke` コマンドと全件 `full` コマンド）・期待する結果をすべてこの `tests/README.md` の表にまとめる。AIはテストコード全文を読まず、`tests/README.md` と実行結果だけで検査する。
- `src/`
  - `src/main.py`（エントリポイント）
  - `src/core/`（共通部品）
  - `src/user/`（ツール固有処理。WebやCLI補助がある場合は `src/user/web/` 等に格納）
  - 依存の向きは `src/main.py` -> `src/user/` -> `src/core/` の単方向のみとする。

### 作業手順
1. 現在のフォルダ構成と `AGENTS.md` を確認し、上記との差分をリストアップする。
2. 不足しているフォルダ・ひな型ファイル（橋渡しファイル3種、`docs/README.md`、`docs/for_ai/project/PROJECT_STATUS.md`、`tests/README.md`、`config/requirements.txt`）を作成し、ルート直下のツール固有ファイルを `src/user/` や `config/` へ整理したうえで `AGENTS.md` に上記ルールを統合する。
3. 完了後、作成・更新したファイル一覧を日本語で報告する。
````
