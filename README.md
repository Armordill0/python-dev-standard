# Python チーム開発標準テンプレート（VSCode / AI エージェント対応版）

社内の既存 Python プロジェクトや新規プロジェクトへ、**「低トークン・高再現性・エージェント横断」** の開発環境標準を適用するための公開テンプレート＆導入キットです。

## 特徴
- **ルールの正本一本化（`AGENTS.md`）**：Claude Code / Cline / Kilo Code / Antigravity がすべて同じ `AGENTS.md` を参照する橋渡しファイルを用意。
- **ルート直下を 4 フォルダに厳守**：`config/`・`docs/`・`tests/`・`src/` のみをルート直下に置き、Webフロントエンドやデプロイ設定などは `src/user/web/` や `config/deploy/` に格納。
- **低トークン設計（AI向け資料とテスト一覧）**：
  - `docs/for_ai/`（英語）：スクリプトごとのインターフェース仕様書と `project/PROJECT_STATUS.md`（必須8項目）を集約。AIはソースを開く前に仕様書を読む。
  - `docs/for_developers/`（日本語）：人間向けの解説やマニュアルを配置。
  - `tests/README.md`：テストファイル名は `test_<対象>.py` とシンプルに保ち、テスト条件・`smoke`/`full` コマンド・期待結果を一覧表に集約。
- **依存関係の自動同期（`config/requirements.txt`）**：ライブラリ実体は Git に入れず、バージョン固定（`pkg==x.y.z`）で管理。

## 含まれるファイル
- **[PROMPT_FOR_COMPANY_AI.md](./PROMPT_FOR_COMPANY_AI.md)**：VSCode の AI 拡張（Kilo Code / Claude Code / Cline）のチャット欄にそのまま貼り付けて既存環境を自動整備するプロンプト。
- **[templates/](./templates/)**：そのままプロジェクトへコピーして使える各種ひな型ファイル一式。
  - `AGENTS.md`（Python 環境標準ルール）
  - `CLAUDE.md` / `.clinerules` / `.kilocode/rules/guidelines.md`（各AI拡張の橋渡し）
  - `config/requirements.txt` / `config/app_setting.sample.json`
  - `docs/README.md` / `docs/for_ai/README.md` / `docs/for_ai/project/PROJECT_STATUS.md`
  - `tests/README.md`
  - `.gitignore`

## クイックスタート
1. [PROMPT_FOR_COMPANY_AI.md](./PROMPT_FOR_COMPANY_AI.md) のプロンプトをコピーし、対象プロジェクトを開いた状態で AI チャット欄に貼り付けて実行する。
2. 新規プロジェクトの場合は `templates/` 配下のファイルをプロジェクトルートへコピーして使い始める。
