# Lab 5 — Static Analysis Findings

The issue locations below refer to the original `smart_home.py` in the assigned repository. Findings are ordered with the highest security severity first. The baseline tool output is preserved under [`reports/before/`](reports/before/), and verification after the fixes is under [`reports/after/`](reports/after/).

| Issue | Type | Line(s) | Description | Fix Approach |
|---|---|---:|---|---|
| Shell command built from input with `shell=True` | Security — High (Bandit B602) | 49–51 | Concatenating `sceneScript` into a shell command permits command injection if the value is controlled by an untrusted caller. | Removed the unused `runScene` helper and `subprocess` import. No scene files or call sites existed in the supplied project, so removing the unsafe, unused feature avoids shell execution entirely. |
| Hub bound to every network interface | Security — Medium (Bandit B104) | 4 | `0.0.0.0` exposes the hub endpoint on all interfaces instead of limiting it to the local machine. | Changed the default host to `127.0.0.1` (loopback). |
| Hard-coded password-like value | Security — Low (Bandit B105) | 7 | A credential embedded in source can be copied, leaked, or reused; the value was not used by the program. | Removed the unused `HUB_PASSWORD` constant. |
| Risky subprocess import | Security — Low (Bandit B404) | 2 | Importing `subprocess` for the shell-based scene helper enabled the unsafe execution path. | Removed the import with the unused helper. The final Bandit report has no findings. |
| Shared mutable default settings | Bug / maintainability (Pylint W0102) | 11–13 | `settings={}` is created once and reused, so devices added without explicit settings share state. The baseline demo incorrectly printed a thermostat temperature under the light. | Changed the default to `None`; create a fresh dictionary for each device and copy any caller-supplied mapping. |
| Missing-device handling and inconsistent return | Bug / quality (Pylint R1710; runtime robustness) | 16–29 | `toggleDevice` returned `None` for an unknown device, while `setTemperature` indexed the registry directly and could raise `KeyError`. | Both operations now look up devices safely and return `False` for missing or incompatible devices; successful operations return `True` or the new state. |
| Non-idiomatic names and missing documentation | Maintainability (Pylint C0103, C0114, C0116) | 1, 11–69 | The module and functions lacked docstrings, and function/parameter names used camelCase. | Added module and function docstrings and renamed functions/parameters to `snake_case`. |
| File opened without explicit encoding | Portability / quality (Pylint W1514) | 41–42 | Text-file encoding would depend on the host environment. | Opened the JSON output with explicit UTF-8 encoding. |
| Whitespace before dictionary colon | Style (Flake8 E203) | 6 | Extra whitespace violated the configured PEP 8 check. | Removed the extra whitespace. |
| Missing blank line before a top-level function | Style (Flake8 E302) | 40 | Top-level definitions did not have the required spacing. | Applied the required top-level separation during refactoring. |
| Inline comment spacing | Style (Flake8 E261) | 66 | The inline comment lacked two spaces before `#`. | Added the required spacing. |
| Line exceeded the configured maximum | Style (Flake8 E501) | 68 | A long list-comprehension print statement exceeded 79 characters. | Assigned the comprehension to a named variable and printed that variable on a separate line. |

## Verification summary

- **Pylint:** 10.00/10.
- **Bandit:** 0 findings (High, Medium, Low all 0).
- **Flake8:** no violations (empty report).
- Focused runtime assertions also passed for independent per-device settings, defensive copying, thermostat range clamping, missing-device handling, power totals, and UTF-8 JSON persistence.
