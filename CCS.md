# Climon Config Standard (CCS)

**Version:** 1.1  
**Status:** Unified Specification

The Climon Config Standard (CCS) defines how tools within the Climon ecosystem discover, parse, and merge configuration settings.

---

## 1. Configuration Management

This section defines how Climon tools discover and prioritize configuration settings.

### 1.1 Configuration Files

Climon tools recognize the following configuration files:

- **Project Config:** `.climon.yaml` or `.climon.json` located in the current working directory. Used for project-specific overrides.
- **Tool-Specific Global:** `~/.<tool_name>/config.yaml` or `config.json` (e.g., `~/.pycurl/config.yaml`). Used for user-specific settings for a single tool.
- **Ecosystem Global:** `~/.climon/config.yaml` or `config.json`. used for settings shared across all Climon tools.

---

### 1.2 Search Priority

Tools must search for configuration in the following order of precedence (highest to lowest):

1.  **Explicit Flag:** `--config <path>`
2.  **Project Config:** `.climon.yaml` or `.climon.json`
3.  **Tool-Specific Global:** `~/.<tool_name>/config.yaml`
4.  **Ecosystem Global:** `~/.climon/config.yaml`
5.  **Defaults:** Built-in hardcoded defaults.

> [!IMPORTANT] \
> **Priority Overview:** Flag > Project Config > Tool Global > Ecosystem Global > Defaults.

> [!NOTE] \
> If a configuration file exists but is invalid (syntax error), the tool must fail with Exit Code 2 (Invalid input) and a clear PSA error message.

---

## 2. Merging Rules

When multiple configuration levels exist, tools should merge them recursively:

- **Scalars:** Higher priority overrides lower priority.
- **Arrays:** Higher priority replaces lower priority (no automatic appending, unless specified by a flag).
- **Objects:** Deep merge; keys in higher priority files overwrite those in lower priority files.

### 2.1 Project Config Precedence
In monorepos or nested structures, tools should only look for the *closest* `.climon.yaml` to the current working directory. They should **not** automatically traverse upwards and merge multiple project-level configs unless explicitly designed for hierarchical inheritance.

---

## 3. Environment Variables

Standard variables to control CLI behavior globally. Tools must respect these even if a config file suggests otherwise.

| Variable | Effect |
| :--- | :--- |
| `CLIMON_CONFIG` | Overrides the default config search (equivalent to `--config`). |
| `CLIMON_JSON` | If set to `true`, forces JSON mode. |
| `CLIMON_QUIET` | If set to `true`, forces quiet mode (errors only). |
| `CLIMON_NO_COLOR` | If set to `true`, disables all ANSI styling. |
| `CLIMON_TELEMETRY` | If set to `false`, disables all usage data collection. |
| `CLIMON_VERBOSE` | If set to `true`, enables DEBUG level logging. |

---

## 4. Configuration Schema

While specific tools have unique keys, the following top-level keys are reserved for ecosystem-wide management.
Standard Climon configurations should validate against the following schema:


### 4.1 YAML Configuration Example

```yaml
# Example .climon.yaml
core:
  auto_update: true
  telemetry: true
  theme: dark

registry: "https://registry.climon.dev"

# Tool-specific settings
pycurl:
  timeout: 30
  verbose: false
```

### 4.2 JSON Configuration Example

```json
{
  "core": {
    "auto_update": true,
    "telemetry": true,
    "theme": "dark"
  },
  "registry": "https://registry.climon.dev",
  "pycurl": {
    "timeout": 30,
    "verbose": false
  }
}
```

---

*Climon Ecosystem 2026*
