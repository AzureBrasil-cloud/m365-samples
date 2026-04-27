# What is Work IQ

> Conceptual overview based on the [official Microsoft documentation](https://learn.microsoft.com/en-us/microsoft-365/copilot/extensibility/workiq-overview) and the [microsoft/work-iq](https://github.com/microsoft/work-iq) repository.

**Microsoft Work IQ** is the intelligence layer that connects AI assistants to Microsoft 365 Copilot data. It exposes **emails, meetings, documents, Teams messages and organizational insights** through the **Model Context Protocol (MCP)**, the same open protocol used by Claude, VS Code, GitHub Copilot and other AI clients.

> ⚠️ **Public Preview.** Features and APIs may change. Requires the **Microsoft 365 Copilot add-on** license and the **Frontier Program** enabled on the tenant.

> 🔒 **Read-only.** This directory covers exclusively the **read-only** usage of Work IQ (queries via CLI and MCP). Write operations are not part of the native product and are out of scope here.

---

## Three-layer architecture

| Layer | What it does |
| --- | --- |
| **Data** | Semantic indexing of emails, documents, chats and files (SharePoint, OneDrive, Outlook, Teams). |
| **Memory** | Persistent context, priorities, collaboration patterns, work history. |
| **Inference** | Language models + MCP tools + agents, reasoning over the data. |

Everything is **fetched on demand**: Work IQ does not store M365 data.

---

## How Work IQ fits in

```
You (VS Code, Copilot CLI, Copilot Studio, Claude, Foundry…)
      │
      ▼
MCP Client  ──────►  Work IQ MCP Server  ──────►  Microsoft Graph
                      (authenticates as you)        (emails, calendar,
                                                     docs, Teams, people)
```

Work IQ operates under a **Delegated Permissions** model: it impersonates the connected user (OAuth/JWT token). If you do not have permission to read the CEO's mailbox in Outlook, the AI will not have it either. **Conditional Access** policies (MFA, device compliance) configured in Microsoft Entra ID are enforced end-to-end during token issuance.

---

## Official repository

| | `microsoft/work-iq` |
| --- | --- |
| **What it is** | CLI + MCP server + ready-made plugins |
| **Audience** | Users and devs consuming M365 data via AI |
| **Language** | TypeScript / Node.js (NPM package `@microsoft/workiq`) |
| **Authentication** | Automatic via M365 credentials |
| **Exposed MCP tool** | `ask_work_iq` (natural language) |

### Official repo layout

```
microsoft/work-iq/
├── scripts/
│   ├── Enable-WorkIQToolsForTenant.ps1   ← enables MCPs + admin consent
│   └── Verify-WorkIQSetup.ps1            ← read-only diagnostics
├── plugins/
│   ├── workiq/                           ← main plugin
│   │   ├── .mcp.json                     ← MCP server config
│   │   └── skills/workiq/SKILL.md        ← when/how to use ask_work_iq
│   ├── workiq-productivity/              ← productivity insights
│   │   ├── email-analytics
│   │   ├── meeting-cost-analyzer
│   │   ├── org-chart
│   │   └── channel-audit
│   └── microsoft-365-agents-toolkit/     ← declarative agent scaffolding
├── server.json                           ← MCP server definition (npm package)
├── README.md
├── ADMIN-INSTRUCTIONS.md
└── PLUGINS.md
```

---

## Execution modes

### `workiq ask`: humans in the terminal

```bash
workiq ask
# Interactive chat in the terminal, type questions in natural language.
# Read-only: it does not create, update or delete anything.
```

### `workiq mcp`: AI agents

When you run `workiq mcp`, the cursor "blinks" because the process turned into a **background server**. It is not waiting for human input: it is waiting for an MCP client (VS Code, Claude, Copilot Studio, Azure AI Foundry) to send JSON-RPC commands.

```
You (agent UI)
    ↓ "What meetings do I have tomorrow?"
MCP Client (VS Code Copilot, Claude, Foundry…)
    ↓ spawns: workiq mcp (in background)
    ↓ sends JSON-RPC: tools/call → ask_work_iq
Work IQ MCP Server
    ↓ authenticates with Microsoft Entra
    ↓ queries Microsoft Graph
    ↓ returns JSON
MCP Client
    ↓ formats the response
You ← human-readable answer
```

> 💡 `workiq mcp` is **not a command for you to type answers into**: it is an endpoint for MCP clients. It goes inside files like `.mcp.json`, `mcp-config.json` or VS Code's `settings.json`.

---

## Security and governance

- **No storage:** no M365 data is persisted, everything is fetched on demand.
- **Delegated Permissions:** the server inherits exactly the permissions of the authenticated user.
- **Admin visibility:** admins control usage via `admin.microsoft.com → Agents and Tools` (Allow / Block by policy).
- **Audit:** MCP call logs are available in **Microsoft Defender → Advanced Hunting**.
- **JWT tokens:** expire in ~1 hour; never store them in code or repositories.

---

## Prerequisites

### Required

- **Microsoft 365 Copilot add-on license**, without it, every call returns `Access Denied`.
- **Node.js ≥ 18** ([nodejs.org](https://nodejs.org/)), Work IQ uses `npx` to run the MCP server.
- **Frontier Program enabled** on the tenant, Work IQ MCP Servers are part of the Frontier preview.
- **Admin permission** on the tenant for the initial admin consent (Global Admin, Cloud Application Admin or Application Admin).

### Optional

- **GitHub Copilot CLI**, to use Work IQ as a terminal plugin.
- **VS Code + GitHub Copilot Chat**, to use Work IQ as an MCP server in the editor.
- **Copilot Studio**, to build low-code agents.
- **Azure AI Foundry**, for pro-code agent development.
- **PowerShell 7+**, for the tenant enablement scripts.

---

## Next steps

1. **Tenant admin:** [../tenant-setup/](../tenant-setup/), enable Frontier and run the admin consent.
2. **User/Dev:** [../cli/](../cli/), install the Work IQ CLI.
3. **Editor:** [../vscode-mcp/](../vscode-mcp/), configure MCP in VS Code.
4. **Use cases:** [./examples.md](./examples.md), ready-made (read-only) prompts.

---

## Official references

- [Microsoft Work IQ CLI (preview)](https://learn.microsoft.com/en-us/microsoft-365/copilot/extensibility/workiq-overview)
- [Work IQ MCP Overview, Microsoft Agent 365](https://learn.microsoft.com/en-us/microsoft-agent-365/tooling-servers-overview)
- [Work IQ in Copilot Studio](https://learn.microsoft.com/en-us/microsoft-copilot-studio/use-work-iq)
- [microsoft/work-iq (official repo)](https://github.com/microsoft/work-iq)
- [ADMIN-INSTRUCTIONS.md (official)](https://github.com/microsoft/work-iq/blob/main/ADMIN-INSTRUCTIONS.md)
- [User and Admin Consent Overview, Microsoft Entra](https://learn.microsoft.com/en-us/entra/identity/enterprise-apps/user-admin-consent-overview)
- [Model Context Protocol, Official spec](https://modelcontextprotocol.io/)
