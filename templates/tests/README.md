# Test Catalog & Low-Cost Verification Guide

> **For AI Agents**: Do NOT read all test source files to check behavior. Use the commands and table below. Run **Smoke Tests** after every edit, and **Full Tests** before release.

## 1. Quick Execution Commands
| Tier | When to Run | Command | Expected Output |
| :--- | :--- | :--- | :--- |
| **Smoke** | After every code edit | `.embeddablepython\python.exe -m compileall -q src` | Exit code `0`, no output |
| **Full** | Before commit / release | `.embeddablepython\python.exe -m unittest discover -s tests -p "test_*.py" -v` | Exit code `0`, `OK` |

## 2. Black-Box Tests (Per Script — `tests/blackbox/`)
| Test File | Target Script | Verified Conditions & Cases | Expected Result |
| :--- | :--- | :--- | :--- |
| `tests/blackbox/test_<script>.py` | `src/user/<script>.py` | 1. Normal input<br>2. Boundary / missing value<br>3. Error handling | All cases `OK` |

## 3. Integration Tests (Per Feature — `tests/integration/`)
| Test File | Target Feature | Verified Scenario | Expected Result |
| :--- | :--- | :--- | :--- |
| `tests/integration/test_<feature>.py` | End-to-end feature flow | Multi-script interaction & output verification | All cases `OK` |
