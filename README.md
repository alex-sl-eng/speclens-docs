# SpecLens

A local-first VS Code and Kiro extension that turns your **spec-driven development** artifacts into a live, read-only reactive dashboard. Designed for **agentic AI workflows** using [Kiro Spec](https://kiro.dev), [AI-DLC](https://github.com/awslabs/aidlc-workflows), and [OpenSpec](https://openspec.dev/) — SpecLens gives you real-time visibility into your requirements, task graph, and test traceability without leaving your editor.

No server, no telemetry, no sync — everything stays in your workspace.

![SpecLens Dashboard](https://raw.githubusercontent.com/alex-sl-eng/speclens-docs/main/images/preview.png)

---

## Features

SpecLens parses your spec artifacts on every save and reflects changes instantly — no manual refresh needed. Here's what's on the dashboard:

### Pipeline Bar
Tracks the lifecycle phases declared by your framework:
- **Kiro Spec** — Requirements → Design → Tasks
- **AI-DLC** — Inception → Construction → Operations
- **OpenSpec** — Proposal → Design → Specs → Tasks

The active phase is highlighted. Completed phases show their last-modified timestamp. Each phase box expands to show a sub-stage checklist (e.g. the individual AI-DLC stages within Inception or Construction), with a completion count summary toggle.

### Task Graph
A top-down **dependency graph** of tasks grouped into topological waves (dependency layers). Tasks are colour-coded by state — pending, running, or completed. Click any node to jump directly to that line in the source file. The graph viewport is resizable by dragging the bottom handle.

Selecting a requirement in the traceability sidebar filters the graph down to only the tasks linked to that requirement. A banner shows the active filter with a one-click clear button.

### Spec ↔ Code Traceability
A collapsible sidebar (drag to resize, or click to hide/show) with one row per requirement, showing:
- **EARS classification badge** — each requirement is automatically classified as Ubiquitous (UBQ), Event-driven (EVT), State-driven (STA), Optional (OPT), or Unwanted (UNW) based on [EARS syntax](https://ieeexplore.ieee.org/document/5636757)
- **Task count badge** — number of tasks linked to the requirement group
- **Verification status** — unverified, verified, or failing — based on a loaded JUnit or JSON test report
- Click a row to filter the task graph to that requirement's tasks; double-click (or Ctrl+Enter) to jump to the requirement in the editor

### Live AI Agent Log
A collapsible terminal-style panel pinned to the bottom of the left column. Shows the most recent file-system activity events scoped to the active spec, colour-coded by event type. Supports CLEAR and PAUSE controls. When collapsed, previews the latest log line in the header bar.

### Snapshot History
SpecLens automatically saves a snapshot every time something meaningful changes (task states, verification status). The History surface (opened via the `SpecLens: View History` command) lets you:
- Browse and filter snapshots by spec, repo, framework, and date range
- Track progress over time as your AI agent completes tasks
- Export a filtered snapshot set to a JSON file (with optional path redaction)
- Import a previously exported snapshot file
- Clear history with a confirmation prompt

---

## Why SpecLens?

If you use **Kiro**, **AI-DLC**, or **OpenSpec** to drive development with AI agents, SpecLens gives you situational awareness without disrupting your flow:

- **Requirements → tasks, always in sync** — see exactly what's done, what's running, and what's blocked
- **Test linkage** — connect your JUnit or Vitest results back to the requirements they verify
- **Zero config to get started** — auto-detects your framework from workspace structure
- **Fully offline** — no cloud, no accounts, no data leaves your machine

---

## Supported frameworks

SpecLens supports the three main **AI-driven spec workflows** used with Kiro and other agentic tools:

| Framework     | Detection                                                                                     |
| ---------------| -----------------------------------------------------------------------------------------------|
| **Kiro Spec** | Workspace contains `.kiro/specs/<feature>/` with `requirements.md`, `design.md`, `tasks.md`   |
| **AI-DLC**    | Workspace contains `aidlc-docs/aidlc-state.md`                                                |
| **OpenSpec**  | Workspace contains `openspec/config.yaml`; each `openspec/changes/<name>/` is a separate spec |

SpecLens activates automatically when it detects any of these structures. Multiple frameworks can coexist in the same workspace.

---

## Getting started

### 1. Install the extension

Install from the VS Code Marketplace, or from a `.vsix` file:

```
Extensions panel → ⋯ → Install from VSIX…
```

### 2. Open a workspace with a spec

SpecLens activates automatically when your workspace contains a recognised spec structure. No configuration is needed to get started.

### 3. Open the dashboard

| Method | Action |
|---|---|
| Command Palette | `Cmd+Shift+P` / `Ctrl+Shift+P` → **SpecLens: Open Dashboard** |
| Activity Bar | Click the **SpecLens eye icon** on the left sidebar |

---

## Commands

| Command | Description |
|---|---|
| **SpecLens: Open Dashboard** | Opens or reveals the live dashboard panel |
| **SpecLens: View History** | Opens the snapshot history surface in a separate panel |
| **SpecLens: Select Spec** | Quick-pick to switch the active spec (when the workspace has multiple) |

---

## Loading a test report

Point SpecLens at a test report to upgrade verification status from linkage-only to real pass/fail.

Add to `.vscode/settings.json`:

```jsonc
{
  "speclens.testReportPath": "reports/junit.xml"
}
```

The value is a path relative to the workspace folder (or an absolute path). Supported formats:

- **JUnit XML** — generated by Jest, Vitest, pytest, Go test, and most CI systems
- **JSON** — any `.json` file containing an array of `{ name, passed, isPropertyBased? }` entries:

```json
[
  { "name": "my test name", "passed": true },
  { "name": "another test", "passed": false, "isPropertyBased": true }
]
```

SpecLens **never runs tests** — it only reads an existing report file. If the file exists but cannot be parsed, a banner is shown in the dashboard.

---

## Settings

All settings are workspace-folder scoped.

| Setting | Type | Default | Description |
|---|---|---|---|
| `speclens.testReportPath` | `string` | — | Path to a JUnit XML or JSON test report file (relative to workspace folder, or absolute). Leave unset for linkage-only mode. |
| `speclens.running.recencyWindowSeconds` | `number` | `45` | Seconds within which a confirmed-linked file modification marks a task as *running*. Range: 1–3600. |
| `speclens.retention.maxDays` | `number` | `30` | Maximum days to retain snapshots. Range: 1–3650. |
| `speclens.retention.maxSizeMB` | `number` | `25` | Maximum snapshot store size in MB. Range: 1–1024. |
| `speclens.frameworks` | `object` | `{}` | Per-folder map to override framework detection. Keys are workspace folder URIs; values are `"auto"`, `"kiro-spec"`, `"aidlc"`, or `"openspec"`. Folders absent from the map default to `"auto"`. |

### Framework override example

```jsonc
{
  "speclens.frameworks": {
    "file:///Users/you/workspace/my-project": "kiro-spec"
  }
}
```

---

## Privacy

SpecLens is entirely local. It reads files from your workspace, writes snapshots to VS Code's extension global storage on your machine, and makes no network requests. No telemetry, no sync, no external services.

---

## License

MIT

---

## Feedback & issues

Found a bug or have a feature request? [Open an issue](https://github.com/alex-sl-eng/speclens-docs/issues) — all feedback is welcome.
