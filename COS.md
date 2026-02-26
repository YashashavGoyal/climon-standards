# Climon Output Standard (COS)

**Version:** 1.1  
**Status:** Unified Specification  

The Climon Output Standard (COS) defines a unified behavior and output style for all CLI tools within the Climon ecosystem. Regardless of the language used (TypeScript, Python, Go), every tool must follow these rules to ensure a consistent, professional developer experience.

---

## 1. Core Philosophy

Climon tools must always aim to:
- **Reduce Friction:** Minimal steps to achieve the desired outcome.
- **Automate Predictably:** Consistent behavior across local, CI/CD, and production environments.
- **Communicate Clearly:** Human-readable status updates paired with machine-parseable results.
- **Fail Gracefully:** Informative error messages that suggest immediate solutions.

---

## 2. Command Structure

All commands must follow the standard pattern:

```bash
tool <action> <target> [flags]
```

**Example:**
- `devmate deploy pycurl`
- `netscan scan 192.168.1.0/24 --verbose`

**Rules:**
- **Action:** Must always be a verb (e.g., `get`, `set`, `scan`, `deploy`).
- **Target:** Must be explicit when possible.
- **Flags:** Must always follow targets. Use double-hyphern for full names (`--json`) and single-hyphen for aliases (`-j`).

---

## 3. User Interface (Human Mode)

By default, tools should output content optimized for human readability.

### 3.1 Styling & Colors
Maintain a consistent color scheme for status signaling.

| Type | Color | Meaning |
| :--- | :--- | :--- |
| **DEBUG** | Gray | Detailed technical logs for troubleshooting |
| **INFO** | Blue | Informational progress updates |
| **SUCCESS** | Green | Confirmed operation completion |
| **WARN** | Yellow | Non-critical issues or potential risks |
| **ERROR** | Red | Failure that stops the execution |

> [!IMPORTANT]
> Colors must be disabled automatically when the output is piped or when the `--no-color` flag is used.

### 3.2 Symbols & Emojis
Use professional symbols to enhance scannability:
- `✔` or `[SUCCESS]` for completed tasks.
- `ℹ` or `[INFO]` for informational notes.
- `⚠` or `[WARN]` for warnings.
- `✖` or `[ERROR]` for failures.

---

## 4. Machine Interface (JSON Mode)

When the `--json` flag is provided, tools must switch to machine-only mode.

**Requirements:**
- **Purity:** Only valid JSON must be printed to `stdout`.
- **Logs:** All informational/error logs must be redirected to `stderr` or suppressed entirely.
- **Structure:** Use flat keys where possible; nested objects only for complex data sets.

**Example:**
```json
{
  "status": "success",
  "result": {
    "service": "api-server",
    "port": 8080,
    "latency": "45ms"
  }
}
```

---

## 5. Global Flags

Every Climon tool must support the following global flags:

| Flag | Alias | Description |
| :--- | :--- | :--- |
| `--help` | `-h` | Show command-specific help |
| `--version` | `-v` | Show CLI version |
| `--verbose` | | Show detailed DEBUG logs |
| `--quiet` | `-q` | Minimize output (errors only) |
| `--json` | `-j` | Output result in pure JSON format |
| `--no-color` | | Disable colored output |
| `--config` | | Path to a custom configuration file |

---

## 6. Interactive Elements

For tools requiring user input or showing long-running tasks:

- **Spinners:** Use for operations taking >1 second.
- **Progress Bars:** Use for tasks with known durations (e.g., downloads, bulk scans).
- **Confirmations:** Default to "No" for destructive actions.
- **Non-Interactive Fallback:** Tools must detect if they are running in a CI/CD environment (Terminal/TTY check) and skip interactive prompts, using defaults instead.

---

## 7. Environment Variables

Standard variables to control CLI behavior globally:

| Variable | Effect |
| :--- | :--- |
| `CLIMON_JSON` | If set to `true`, forces JSON mode. |
| `CLIMON_QUIET` | If set to `true`, forces quiet mode. |
| `CLIMON_NO_COLOR` | If set to `true`, disables all styling. |
| `CLIMON_CONFIG` | Path to a custom configuration file. |

---

## 8. Configuration Files

Tools must search for configuration in this order:
1. **Explicit Flag:** `--config <path>`
2. **Project Config:** `.climon.yaml` (in current directory)
3. **Tool-Specific Global:** `~/.<tool_name>/config.json` or `.yaml` (e.g., `~/.pycurl/config.json`)
4. **Ecosystem Global:** `~/.climon/config.json` or `yaml`

**Priority:** Flag > Project Config > Tool Global > Ecosystem Global > Defaults.

---

## 9. Exit Codes

Predictable exit codes are critical for shell scripting.

| Code | Meaning |
| :--- | :--- |
| **0** | Success |
| **1** | Generic/Logic error |
| **2** | Invalid input/Arguments |
| **3** | Network/Connection failure |
| **4** | Missing dependency/Permissions |

---

## 10. Error Management

Errors must be actionable. A good error message follows the **P-S-A** pattern:
1. **P**roblem: What went wrong?
2. **S**ource: Where/Why? (if possible)
3. **A**ction: What should the user do next?

**Bad:** `Error occurred while connecting.` \
**Good:** `[ERROR] Failed to connect to port 8080. The service may be offline. Source: Error Source. Suggestion: Check if the server is running and port 8080 is open.`

---

## 11. Versioning

Tools must follow [Semantic Versioning 2.0.0](https://semver.org/):
- **MAJOR:** Breaking changes (e.g., CLI flag removals).
- **MINOR:** New features, backward-compatible.
- **PATCH:** Bug fixes, backward-compatible.

---

## 12. Documentation

Every Climon tool must provide accessible, high-quality documentation.

- **Command Reference:** Access via `tool docs`. This command should render the project's Markdown documentation directly in the terminal with pager support.
- **Workflow Guides:** Access via `tool workflow` or `tool docs workflow`. These guides should provide step-by-step instructions for specific end-to-end tasks.

```bash
tool docs [name]
```
If `name` is omitted, the tool should list, all available documentation.
This command should render the project's Markdown documentation directly in the terminal with pager support.

---

## 13. Workflow Documentation

Workflow documentation is designed to help users achieve specific outcomes by chaining multiple commands and configurations.

**Requirements:**
- **Outcome-Oriented:** Each workflow must solve a specific problem (e.g., "Getting Started", "Deploying a Microservice").
- **Step-by-Step:** Use numbered lists with clear, executable commands.
- **Contextual Tips:** Include brief explanations for *why* a step is necessary.
- **Visual Aids:** Use ASCII diagrams or clear code blocks to demonstrate the flow.

**Standard Command:**
```bash
tool workflow
```

---

*Climon Ecosystem 2026*
