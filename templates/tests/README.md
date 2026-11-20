# Test Catalog & Low-Cost Verification Guide

> **For AI Agents**: Do NOT read all test source files to check behavior. Use the commands and table below. All tests must run cleanly in headless Linux on GitHub Actions (`.github/workflows/test.yml`) without local paths or credentials.

## 1. Quick Execution Commands
| Tier | When to Run | Command | Expected Output |
| :--- | :--- | :--- | :--- |
| **Smoke (Syntax)** | After every code edit | `python -m compileall -q src` (or `.embeddablepython\python.exe -m compileall -q src`) | Exit code `0`, no output |
| **Full (Pytest / CI)** | Before commit / PR / release | `pytest` (or `.embeddablepython\python.exe -m pytest`) | Exit code `0`, all passed |
| **Node / Web (If present)** | Before commit / PR / release | `CI=true npm test` | Exit code `0`, all passed |

## 2. Black-Box Tests (Per Script — `tests/blackbox/`)
| Test File | Target Script | Verified Conditions & Cases | Expected Result |
| :--- | :--- | :--- | :--- |
| `tests/blackbox/test_<script>.py` | `src/user/<script>.py` | 1. Normal input<br>2. Boundary / missing value<br>3. Error handling (mocked, headless-safe) | All cases `passed` |

## 3. Integration Tests (Per Feature — `tests/integration/`)
| Test File | Target Feature | Verified Scenario | Expected Result |
| :--- | :--- | :--- | :--- |
| `tests/integration/test_<feature>.py` | End-to-end feature flow | Multi-script interaction & output verification | All cases `passed` |
