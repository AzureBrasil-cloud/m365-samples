# Work IQ MCP in VS Code (read-only)

Configure VS Code's **GitHub Copilot Chat** (or any other MCP client) to consume the **Work IQ MCP Server**. The AI gains read-only access to your emails, calendar, documents, Teams and organization, without ever changing anything.

---

## Prerequisites

- Up-to-date **VS Code** with the **GitHub Copilot Chat** extension.
- **Node.js ≥ 18** installed (for `npx`).
- Tenant configured by the admin → [`../tenant-setup/`](../tenant-setup/).
- Active **Microsoft 365 Copilot add-on** license.
- (Optional) `workiq accept-eula` run once, see [`../cli/`](../cli/).

> You **do not need** to install `@microsoft/workiq` globally. The snippet below uses `npx`, which fetches the package on demand.

---

## The snippet

The file [`mcp-config.json`](./mcp-config.json) is the **single, portable** snippet that works for VS Code as well as for any other MCP client (Claude Desktop, Azure AI Foundry, Copilot Studio in advanced config, custom agents):

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

---

## VS Code configuration

VS Code reads MCP configuration from an `mcp.json` file. There are two options:

### Option A: Workspace (recommended)

Create `.vscode/mcp.json` at the root of your project and paste the contents of [`mcp-config.json`](./mcp-config.json).

### Option B: User-wide

`Cmd/Ctrl + Shift + P` → **MCP: Open User Configuration** → paste the contents of [`mcp-config.json`](./mcp-config.json).

### Activate it in Copilot Chat

1. Restart VS Code.
2. In the Copilot Chat panel → 🔧 **Tools** icon → toggle **`workiq`** on.
3. The first call will request a Microsoft 365 sign-in consent.

---

## Other MCP clients

The same [`mcp-config.json`](./mcp-config.json) works in any client that reads the standard MCP format. Examples:

| Client | Where to paste |
| --- | --- |
| **Claude Desktop** | `claude_desktop_config.json` |
| **Azure AI Foundry** | Agent configuration → Tools → MCP |
| **Copilot Studio** | Tools → Add MCP server (advanced config) |
| **Custom agent** | Whatever file your MCP client expects |

---

## How to use

With `workiq` enabled, just ask in natural language in Copilot Chat:

```
What meetings do I have today?
Summarize unread emails by priority.
Which SharePoint documents are related to customer Contoso?
Who is my manager?
List decisions made in recent meetings with the Engineering team.
```

### Typical scenario: context for code

```
Before I implement the checkout feature:
1. Search Work IQ for all meetings, emails and specs about "checkout v2".
2. Summarize the requirements that were discussed.
3. List who should be added as a reviewer on the PR.
```

More examples: [`../docs/examples.md`](../docs/examples.md).

---

## Troubleshooting

| Symptom | Likely cause | Action |
| --- | --- | --- |
| `workiq` does not show under Tools | `mcp.json` not saved / VS Code not restarted | Save the file and restart VS Code. |
| `Access Denied` on the first call | No Copilot license or consent not granted | See [`../tenant-setup/`](../tenant-setup/). |
| `command not found: npx` | Node.js not installed | Install Node.js ≥ 18. |
| Empty answers or "I don't have access" | The signed-in user really lacks Graph permission for the resource | Expected, Work IQ inherits delegated permissions. |

---

## Files in this directory

| File | Description |
| --- | --- |
| [mcp-config.json](./mcp-config.json) | Single MCP snippet, works in VS Code (`.vscode/mcp.json`) and in any other MCP client. |
