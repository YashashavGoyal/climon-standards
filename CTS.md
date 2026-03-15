# Climon Tool Standard (CTS)

**Version:** 1.0  
**Status:** Draft / Not in use (Reserved for `climon-core` development)

The Climon Tool Standard (CTS) defines how independent CLI tools integrate with the central `climon` ecosystem hub.

---

## 1. Climon Core Overview

`climon-core` is the central CLI tool that serves as a **Package Manager** for the Climon ecosystem. It allows users to discover, install, upgrade, and remove compliant tools.

### 1.1 Core Objectives
- **Centralized Discovery:** List available tools in the registry.
- **Unified Lifecycle:** Single command to `climon install <tool>`.
- **Proxy Invocation:** Allow calling tools via the `climon` binary.

---

## 2. Tool Identity

Each tool must be identifiable via a manifest file located in its root directory.

### 2.1 Manifest: `climon-tool.yaml`
Every tool must include a `climon-tool.yaml` file defining its metadata:

```yaml
name: pycurl
version: 1.2.0
description: "Advanced curl implementation with COS/CCS compliance"
binary: pycurl
language: python
category: networking
```

---

## 3. Installation Layout

When managed by `climon-core`, tools follow a standardized cross-platform layout:

- **Binaries:** `~/.climon/bin/` (Added to system PATH)
- **Manifests:** `~/.climon/tools/<tool_name>/climon-tool.yaml`
- **Data/Cache:** `~/.climon/data/<tool_name>/`

---

## 4. Invocation Mechanics

Tools in the Climon ecosystem can be invoked in two ways.

### 4.1 Direct Invocation
If the tool's binary is in the user's PATH (managed by `climon-core`), it works like any standalone command:
```bash
pycurl get https://example.com
```

### 4.2 Proxy Invocation (Soft Link)
Users can call any installed tool via the `climon` master binary. `climon` acts as a proxy, passing arguments to the underlying tool:
```bash
climon pycurl get https://example.com
```
*Note: This ultimately resolves to `pycurl get https://example.com` after verifying the tool is installed.*

---

## 5. Integration Rules

To be eligible for registry listing and `climon-core` management, tools must:

- **Follow COS:** Strictly adhere to the [Climon Output Standard (COS)](COS.md).
- **Follow CCS:** Use search priorities and variables defined in [Climon Config Standard (CCS)](CCS.md).
- **Global Flags:** Support all mandatory flags (e.g., `--json`, `--verbose`).
- **Versioning:** Use Semantic Versioning 2.0.0.

---

*Climon Ecosystem 2026*

