# Work IQ MCP in GitHub Copilot CLI (read-only)

Use **GitHub Copilot CLI** as a terminal-based agentic assistant grounded in your Microsoft 365 data through the **Work IQ MCP Server**. Every answer about emails, calendar, Teams messages, SharePoint documents and organizational graph comes from your real tenant data, in read-only mode.

---

## Prerequisites

- **GitHub Copilot subscription** (Free, Pro, Pro+, Business or Enterprise).
- **Node.js 22+** (required for npm install).
- Tenant configured by the admin → [`../../tenant-setup/`](../../tenant-setup/).
- Active **Microsoft 365 Copilot add-on** license.
- (Optional) `workiq accept-eula` run once, see [`../../cli/`](../../cli/).

---

## Files in this directory

| File | Purpose |
| --- | --- |
| [mcp-config.json](./mcp-config.json) | Work IQ MCP snippet. Merge into `~/.copilot/mcp-config.json`. |
| [work-iq-assistant.agent.md](./work-iq-assistant.agent.md) | Optional custom agent profile. Copy to `~/.copilot/agents/` or `.github/agents/`. |

---

## Step 1: install GitHub Copilot CLI

Choose the method for your platform:

### macOS and Linux, Homebrew (recommended)

```bash
brew install copilot-cli
```

### All platforms, npm

```bash
npm install -g @github/copilot
```

> Requires Node.js 22 or later.

### macOS and Linux, install script

```bash
curl -fsSL https://gh.io/copilot-install | bash
```

### Windows, WinGet

```powershell
winget install GitHub.Copilot
```

Verify the installation:

```bash
copilot --version
```

---

## Step 2: authenticate

Start the CLI and log in with your GitHub account:

```bash
copilot
/login
```

Follow the on-screen instructions. Alternatively, export a fine-grained personal access token with the **Copilot Requests** permission:

```bash
export COPILOT_GITHUB_TOKEN=<your-token>
```

---

## Step 3: add the Work IQ MCP server

GitHub Copilot CLI stores MCP configuration in `~/.copilot/mcp-config.json`. You can add the Work IQ server interactively or by editing the file directly.

### Option A, interactive (recommended)

Inside an active `copilot` session:

```
/mcp add
```

Fill in the fields:

| Field | Value |
| --- | --- |
| Name | `workiq` |
| Command | `npx` |
| Arguments | `-y @microsoft/workiq@latest mcp` |
| Tools | `*` |

Press `Ctrl+S` to save.

### Option B: edit the file directly

Merge the contents of [`mcp-config.json`](./mcp-config.json) into `~/.copilot/mcp-config.json`:

```json
{
  "mcpServers": {
    "workiq": {
      "command": "npx",
      "args": ["-y", "@microsoft/workiq@latest", "mcp"],
      "tools": ["*"]
    }
  }
}
```

Restart the CLI after editing the file.

### Verify

Inside the `copilot` session:

```
/mcp
```

You should see `workiq` listed as a connected server.

---

## Step 4: (optional) install the custom agent

A **custom agent** is an `*.agent.md` file that gives Copilot CLI a focused persona with specific tool access and instructions.

The included [work-iq-assistant.agent.md](./work-iq-assistant.agent.md) creates a **Work IQ Assistant** agent restricted to `workiq/*` tools in read-only mode.

### Install at user level (all projects)

```bash
mkdir -p ~/.copilot/agents
cp work-iq/github-cli-mcp/work-iq-assistant.agent.md ~/.copilot/agents/
```

### Install at repository level (current project only)

```bash
mkdir -p .github/agents
cp work-iq/github-cli-mcp/work-iq-assistant.agent.md .github/agents/
```

### Activate the agent

Inside the `copilot` session, use the `/agent` slash command to pick it from the list:

```
/agent
```

Select **Work IQ Assistant**. Or call it directly in a prompt:

```
Use the Work IQ Assistant agent to list all messages from Carlos Erutan in Microsoft Teams (Work IQ).
```

---

## Step 5: use it

Once the MCP server is registered (and optionally the agent is active), ask in natural language. Copilot CLI will call the `workiq/*` MCP tools and ground every answer in your tenant data.

### Example

```
List all messages from Carlos Erutan in Microsoft Teams (Work IQ).
```

![Work IQ Assistant agent in GitHub Copilot CLI answering "List all messages from Carlos Erutan in Microsoft Teams" using the Work IQ MCP](../../images/github-cli-workiq-messages-from-user.png)

> 📷 Drop your screenshot at `work-iq/images/github-cli-workiq-messages-from-user.png` to render the image above.

More prompt ideas: [`../../docs/examples.md`](../../docs/examples.md).

---

## Tips

| Task | Command |
| --- | --- |
| See all configured MCP servers | `/mcp` |
| Check context and token usage | `/context` |
| Switch to a different AI model | `/model` |
| Enable plan mode before acting | `Shift+Tab` |
| Resume a previous session | `copilot --continue` |
| Enable all permissions (trusted env) | `copilot --allow-all` |
| Run a shell command directly | `!<command>` |

---

## Troubleshooting

| Symptom | Likely cause | Action |
| --- | --- | --- |
| `workiq` not listed under `/mcp` | `mcp-config.json` not saved or wrong path | Verify `~/.copilot/mcp-config.json`, restart the CLI. |
| `command not found: npx` | Node.js not installed or wrong version | Install Node.js 22+. |
| `Access Denied` on first Work IQ call | No Copilot license or M365 consent not granted | See [`../../tenant-setup/`](../../tenant-setup/). |
| `Work IQ Assistant` not in `/agent` list | Agent file not in `~/.copilot/agents/` or `.github/agents/` | Move the file and restart the session. |
| Agent answers from "general knowledge" | `workiq` MCP not registered or not connected | Run `/mcp` to confirm the server is listed. |
| Empty answers or "I don't have access" | Signed-in user lacks Graph permission for the resource | Expected. Work IQ inherits delegated permissions. |

---

## Official references

- [GitHub Copilot CLI, product page](https://github.com/features/copilot/cli)
- [Installing GitHub Copilot CLI](https://docs.github.com/en/copilot/how-tos/set-up/install-copilot-cli)
- [Using GitHub Copilot CLI](https://docs.github.com/en/copilot/how-tos/copilot-cli/use-copilot-cli-agents/overview)
- [Add an MCP server to Copilot CLI](https://docs.github.com/en/copilot/how-tos/copilot-cli/use-copilot-cli-agents/overview#add-an-mcp-server)
- [Custom agents in Copilot CLI](https://docs.github.com/en/copilot/how-tos/copilot-cli/use-copilot-cli-agents/overview#use-custom-agents)
