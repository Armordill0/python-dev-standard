# Test Catalog & Low-Cost Verification Guide

> **For AI Agents**: Do NOT read all test source files to check behavior. Use the commands and table below. All tests must run cleanly in headless Linux on GitHub Actions (`.github/workflows/test.yml`) without local paths or credentials.

## 1. Quick Execution Commands
| Tier | When to Run | Command | Expected Output |
| :--- | :--- | :--- | :--- |
| **Smoke (Syntax)** | After every code edit | `python -m compileall -q src` (or `.embeddablepython\python.exe -m compileall -q src`) | Exit code `0`, no output |
| **Full (Pytest / CI)** | Before commit / PR / release | `pytest` (with `PYTHONPATH=src` or `pytest.ini`) | Exit code `0`, all passed |
| **Node / Web (If present)** | Before commit / PR / release | `CI=true npm --prefix src/user/web test` | Exit code `0`, all passed |

## 2. Black-Box Tests (Per Script — `tests/blackbox/`)
| Test File | Target Script | Verified Conditions & Cases | Expected Result |
| :--- | :--- | :--- | :--- |
| `tests/blackbox/test_<script>.py` | `src/user/<script>.py` | 1. Normal input<br>2. Boundary / missing value<br>3. Error handling (mocked, headless-safe) | All cases `passed` |

## 3. Integration Tests (Per Feature — `tests/integration/`)
| Test File | Target Feature | Verified Scenario | Expected Result |
| :--- | :--- | :--- | :--- |
| `tests/integration/test_<feature>.py` | End-to-end feature flow | Multi-script interaction & output verification | All cases `passed` |

## 4. Integration Test Design Criteria (`tests/integration/test_<feature>.py`)
1. **Boundary & Pipeline Scope**: Verify end-to-end data flow across `src/main.py` -> `src/user/*` -> `src/core/*` (config loading -> domain transformation -> file/DB output -> structured log emission).
2. **Real I/O vs. Mock Boundary**:
   - **Use Real Implementations**: Local filesystem I/O (inside pytest `tmp_path`), JSON/CSV parsing, and SQLite/in-memory databases.
   - **Mock Only External Boundaries**: External network APIs (GCP, GAS, SMTP, HTTP) and external binaries (e.g., SteamCMD) via `unittest.mock` / `monkeypatch`.
3. **Failure Injection & Rollback**: Test missing config keys, malformed inputs, and simulated network timeouts/500 errors to verify graceful rollback, clean error logging, and non-zero exit codes without data corruption.
4. **Zero Resource Leak Post-Condition**: Assert that all file handles, DB connections, and worker threads are closed before teardown (no locked files on Windows or leftover files outside `tmp_path`).
