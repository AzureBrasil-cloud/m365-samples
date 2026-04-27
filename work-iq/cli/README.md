# Work IQ CLI — installation and usage (read-only)

Install `@microsoft/workiq` to query your Microsoft 365 data using natural language directly from the terminal.

> 🔒 Everything here is **read-only** — `workiq ask` only queries; nothing is created, updated or deleted.

---

## Prerequisites

- **Node.js ≥ 18** ([nodejs.org](https://nodejs.org/))
- Tenant already configured by the admin → see [`../tenant-setup/`](../tenant-setup/)
- Active **Microsoft 365 Copilot add-on** license for the user

---

## Option 1 — Automated script (recommended)

### macOS / Linux

```bash
./cli/setup-workiq-cli.sh
```

### Windows

```powershell
.\cli\setup-workiq-cli.ps1
```

The script:
1. Verifies that Node.js is installed.
2. Installs `@microsoft/workiq` globally via NPM.
3. Runs `workiq accept-eula` (required on first run).
4. Shows the MCP snippet to be placed in `.vscode/mcp.json` (see [`../vscode-mcp/`](../vscode-mcp/)).
5. Detects GitHub Copilot CLI and prints the plugin command.

---

## Option 2 — Manual NPM global

```bash
npm install -g @microsoft/workiq
workiq accept-eula
workiq ask -q "What meetings do I have today?"
```

After `accept-eula`, you see a confirmation in the terminal:

![Terminal showing workiq accept-eula → EULA has been accepted](../images/cli-accept-eula.png)

---

## Option 3 — NPX with no install

```bash
npx -y @microsoft/workiq@latest ask -q "Summarize my unread emails"
```

> Unlike `npm` (which installs packages), `npx` (Node Package Execute) downloads the package into a temporary cache, runs it and discards it. Great for trying things out without polluting the system.

---

## Option 4 — GitHub Copilot CLI (as a plugin)

Use Work IQ as a plugin of the **GitHub Copilot CLI**:

```bash
copilot                                    # opens the Copilot CLI
/plugin marketplace add microsoft/work-iq  # one-time
/plugin install workiq@work-iq
/plugin install workiq-productivity@work-iq
workiq accept-eula
```

After that, just ask in natural language inside Copilot CLI:

```
You: What are my meetings today?
You: Summarize unread emails from the last hour
You: Who is my manager?
```

---

## Available commands

| Command | Description |
| --- | --- |
| `workiq accept-eula` | Accepts the EULA — required on first run. |
| `workiq ask` | Interactive mode — chat in the terminal. |
| `workiq ask -q "question"` | Direct question without entering interactive mode. |
| `workiq ask -t "tenant-id" -q "..."` | Specifies the tenant manually. |
| `workiq mcp` | Starts the MCP server (for clients — not for humans). |
| `workiq version` | Installed version. |

📖 Full options table and more examples in [`../docs/examples.md`](../docs/examples.md).

---

## Quick examples

```bash
workiq ask -q "What meetings do I have today?"
workiq ask -q "Summarize unread emails by priority"
workiq ask -q "Find documents I worked on in the last 3 days"
workiq ask -q "Who is my manager?"
workiq ask -q "Summarize today's messages in the Engineering channel"
```

Real example — asking which emails are irrelevant:

![CLI running workiq ask about irrelevant emails — start of the response](../images/cli-ask-emails-irrelevant.png)

![Continuation of the response: classification of emails that should not be deleted and quick summary](../images/cli-ask-emails-irrelevant-output.png)

Full catalog: [`../docs/examples.md`](../docs/examples.md).

---

## Files in this directory

| File | Platform |
| --- | --- |
| [setup-workiq-cli.sh](./setup-workiq-cli.sh) | macOS / Linux (bash) |
| [setup-workiq-cli.ps1](./setup-workiq-cli.ps1) | Windows (PowerShell 7+) |

---

## Next step

Want to use Work IQ directly in the editor? See [`../vscode-mcp/`](../vscode-mcp/).
