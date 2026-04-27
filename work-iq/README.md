# 🧠 Work IQ — Consolidated reference

> Community guide to put **Microsoft Work IQ** to work in a Microsoft 365 tenant — **read-only**.
>
> Content consolidated from the [CarlosErutan/Work-IQ](https://github.com/CarlosErutan/Work-IQ) repository and the official [microsoft/work-iq](https://github.com/microsoft/work-iq) documentation.

**Microsoft Work IQ** is the intelligence layer that connects AI assistants to Microsoft 365 Copilot data — emails, meetings, documents, Teams messages and organizational insights — through the **Model Context Protocol (MCP)**.

> 🔒 **Scope of this directory:** read-only usage of Work IQ (CLI + MCP) only. Write operations are not part of the native product and are **out of scope**.

> ⚠️ **Public Preview.** Features and APIs may change. Requires the **Microsoft 365 Copilot add-on** license and the **Frontier Program** enabled.

---

## Directory layout

```
work-iq/
├── README.md                           ← you are here
│
├── docs/                               ← conceptual explanation + examples
│   ├── README.md
│   ├── overview.md                     ← what it is, architecture, security
│   └── examples.md                     ← read-only prompt catalog
│
├── tenant-setup/                       ← M365 admin prepares the tenant
│   ├── README.md                       ← admin consent step-by-step
│   ├── Enable-WorkIQToolsForTenant.ps1 ← provisions Service Principals
│   └── Verify-WorkIQSetup.ps1          ← read-only diagnostics
│
├── cli/                                ← Work IQ CLI (terminal)
│   ├── README.md                       ← installation + CLI usage
│   ├── setup-workiq-cli.sh             ← bootstrap macOS/Linux
│   └── setup-workiq-cli.ps1            ← bootstrap Windows
│
└── vscode-mcp/                         ← Work IQ MCP in VS Code (and other MCP clients)
    ├── README.md                       ← config + usage in Copilot Chat
    └── mcp-config.json                 ← single MCP snippet (VS Code + others)

images/                                 ← screenshots used across the docs
```

---

## Where to start

| I am… | Start with |
| --- | --- |
| I want to understand the product | [docs/overview.md](./docs/overview.md) |
| M365 admin preparing the tenant | [tenant-setup/](./tenant-setup/) |
| Dev/User installing locally | [cli/](./cli/) |
| I want to use it in the editor | [vscode-mcp/](./vscode-mcp/) |
| Looking for prompt examples | [docs/examples.md](./docs/examples.md) |

---

## End-to-end flow

```
1. M365 Admin                           tenant-setup/
   └─ Enables Frontier
   └─ Runs Enable-WorkIQToolsForTenant.ps1 (admin consent)
   └─ Verifies with Verify-WorkIQSetup.ps1
                │
                ▼
2. User/Dev                             cli/
   └─ Installs @microsoft/workiq (script or npm/npx)
   └─ workiq accept-eula
   └─ workiq ask -q "What meetings do I have today?"
                │
                ▼
3. Editor (optional)                    vscode-mcp/
   └─ Creates .vscode/mcp.json (or User MCP config) with the snippet
   └─ Toggles "workiq" under Tools in Copilot Chat
   └─ Asks in natural language inside VS Code
```

---

## Prerequisites at a glance

- **Microsoft 365 Copilot add-on** license (without it: `Access Denied`).
- **Node.js ≥ 18**.
- **PowerShell 7+** for the tenant scripts.
- **Frontier Program** enabled on the tenant.
- **Admin** role (Global / Cloud Application / Application) for the initial consent.

Full details in [docs/overview.md](./docs/overview.md#prerequisites).

---

## Official references

- [Microsoft Work IQ CLI (preview)](https://learn.microsoft.com/en-us/microsoft-365/copilot/extensibility/workiq-overview)
- [Work IQ MCP Overview — Microsoft Agent 365](https://learn.microsoft.com/en-us/microsoft-agent-365/tooling-servers-overview)
- [Work IQ in Copilot Studio](https://learn.microsoft.com/en-us/microsoft-copilot-studio/use-work-iq)
- [microsoft/work-iq (official repo)](https://github.com/microsoft/work-iq)
- [Model Context Protocol — Official spec](https://modelcontextprotocol.io/)

---

> **Disclaimer:** community reference, **not officially affiliated with Microsoft**. Work IQ is a Microsoft product in Public Preview.
