# Tenant Setup — M365 Admin

Steps performed by the **tenant administrator** (Global Admin, Cloud Application Admin or Application Admin) to enable Work IQ in Microsoft 365.

> 📖 Before starting, read [`docs/overview.md`](../docs/overview.md) to understand the prerequisites and the permission model.

---

## Flow at a glance

```
1. Verify Copilot add-on licenses
        │
        ▼
2. Enable the Frontier Program
        │
        ▼
3. Provision Service Principals + Admin Consent
   (PowerShell script)
        │
        ▼
4. Verify the configuration
        │
        ▼
5. Manage MCP Servers in the Admin Center
```

---

## 1. Verify licenses

Go to `admin.microsoft.com → Billing → Licenses` and confirm that users have the **Microsoft 365 Copilot add-on** license assigned.

> ⏱️ After assignment, allow up to **24h** for full propagation.

---

## 2. Enable the Frontier Program

Frontier requires three simultaneous conditions: Copilot license, organization opt-in, and admin enablement.

![Frontier prerequisites: Copilot license, Organization enrollment and Admin-managed access](../images/frontier-requirements.png)

1. Go to [admin.microsoft.com](https://admin.microsoft.com).
2. Navigate to **Settings → Org Settings → Services**.
3. Look for **"Microsoft 365 Insider"** / **"Frontier"**.
4. Enable it for the desired users or groups.

![M365 Admin Center — enabling Copilot Frontier under Copilot Settings](../images/admin-center-frontier-activation.png)

> 💡 Without Frontier, Work IQ MCP Servers **do not show up** in the Copilot Studio or Azure AI Foundry catalog.

More details: [Microsoft Frontier Program](https://www.microsoft.com/microsoft-365-copilot/frontier-program).

---

## 3. Provision Service Principals + Admin Consent

### Option A — PowerShell script (recommended)

```powershell
# Prerequisite: PowerShell 7+ and the Microsoft.Graph module
Install-Module Microsoft.Graph -Scope CurrentUser

cd tenant-setup
.\Enable-WorkIQToolsForTenant.ps1
```

![PowerShell terminal cloning work-iq and running Enable-WorkIQToolsForTenant.ps1](../images/enable-script-clone-and-run.png)

The script automatically provisions the Service Principals: **Work IQ Tools, Mail, Calendar, Teams, OneDrive, SharePoint, Word, Admin, Me and M365 Copilot**, and grants admin consent for the required permissions.

![Script output: provisioning of MCP server service principals and admin consent for permissions](../images/enable-script-provisioning-output.png)

### Option B — Quick-consent URL (1 click)

```
https://login.microsoftonline.com/YOUR_TENANT_ID/adminconsent?client_id=e1ef8955-6b2c-4f30-9e71-8ede31ae55ee
```

> ⚠️ If it returns `AADSTS650052`, the Service Principal has not been provisioned yet — use **Option A**.

---

## 4. Verify the configuration

Read-only script — safe to run in production:

```powershell
.\Verify-WorkIQSetup.ps1
```

Expected output: ✅ for every Work IQ Service Principal.

---

## 5. Manage MCP Servers in the Admin Center

1. Go to `admin.microsoft.com → Agents and Tools`.
2. View all active MCP Servers.
3. Use **Allow** / **Block** by organizational policy.

![M365 Admin Center — Agents → Tools → MCP Servers listing the 12 Work IQ servers available](../images/admin-center-mcp-servers.png)

To audit MCP calls in real time, use **Microsoft Defender → Advanced Hunting**.

---

## Files in this directory

| File | Description |
| --- | --- |
| [Enable-WorkIQToolsForTenant.ps1](./Enable-WorkIQToolsForTenant.ps1) | Provisions Service Principals and grants admin consent. |
| [Verify-WorkIQSetup.ps1](./Verify-WorkIQSetup.ps1) | Read-only tenant diagnostics. |

---

## Next steps

- Admin done → hand the configured tenant over to devs/users.
- User/Dev → [`../cli/`](../cli/) to install the Work IQ CLI.
- Editor → [`../vscode-mcp/`](../vscode-mcp/) to configure it in VS Code.
