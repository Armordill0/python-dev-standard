# Kilo Code Guidelines — Entry Point

1. Treat `AGENTS.md` at the repository root as the single canonical rule file. Read it before starting any task.
2. Conserve tokens by reading `docs/for_ai/project/PROJECT_STATUS.md`, `docs/for_ai/` per-script specifications, and `tests/README.md` instead of scanning all `.py` source or test files.
3. Keep the repository root strictly aligned with the 4-folder skeleton (`config/`, `docs/`, `tests/`, `src/`), placing tool-specific web/extra files under `src/user/` or `config/`.
4. Keep all pip dependencies pinned in `config/requirements.txt`.
5. Run the smoke and full test commands documented in `tests/README.md` to verify changes.
