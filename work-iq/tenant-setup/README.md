# Tenant Setup: M365 Admin

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

![M365 Admin Center, enabling Copilot Frontier under Copilot Settings](../images/admin-center-frontier-activation.png)

> 💡 Without Frontier, Work IQ MCP Servers **do not show up** in the Copilot Studio or Azure AI Foundry catalog.

More details: [Microsoft Frontier Program](https://www.microsoft.com/microsoft-365-copilot/frontier-program).

---

## 3. Provision Service Principals + Admin Consent

### Option A: Provision via Microsoft Graph PowerShell

```powershell
# Prerequisite: PowerShell 7+ and the Microsoft.Graph module
Install-Module Microsoft.Graph -Scope CurrentUser
Connect-MgGraph -Scopes "Application.ReadWrite.All","AppRoleAssignment.ReadWrite.All"

# Provision the Work IQ service principals in your tenant by AppId
$workIqAppIds = @(
  "e1ef8955-6b2c-4f30-9e71-8ede31ae55ee" # Work IQ Tools (root)
  # Add the additional Work IQ AppIds documented by Microsoft as needed.
)
foreach ($appId in $workIqAppIds) {
  New-MgServicePrincipal -AppId $appId
}
```

![PowerShell terminal cloning work-iq and running the enable workflow](../images/enable-script-clone-and-run.png)

This provisions the Service Principals (**Work IQ Tools, Mail, Calendar, Teams, OneDrive, SharePoint, Word, Admin, Me and M365 Copilot**) so admin consent can be granted for the required permissions.

![Provisioning of MCP server service principals and admin consent for permissions](../images/enable-script-provisioning-output.png)

### Option B: Quick-consent URL (1 click)

```
https://login.microsoftonline.com/YOUR_TENANT_ID/adminconsent?client_id=e1ef8955-6b2c-4f30-9e71-8ede31ae55ee
```

> ⚠️ If it returns `AADSTS650052`, the Service Principal has not been provisioned yet, use **Option A**.

---

## 4. Verify the configuration

In `admin.microsoft.com → Agents and Tools` you should see every Work IQ Service Principal with status ✅. You can also list them via Microsoft Graph PowerShell:

```powershell
Get-MgServicePrincipal -Filter "startswith(displayName,'Work IQ')" | Select-Object DisplayName, AppId
```

Expected output: one entry per Work IQ Service Principal.

---

## 5. Manage MCP Servers in the Admin Center

1. Go to `admin.microsoft.com → Agents and Tools`.
2. View all active MCP Servers.
3. Use **Allow** / **Block** by organizational policy.

![M365 Admin Center, Agents → Tools → MCP Servers listing the 12 Work IQ servers available](../images/admin-center-mcp-servers.png)

To audit MCP calls in real time, use **Microsoft Defender → Advanced Hunting**.

---

## Next steps

- Admin done → hand the configured tenant over to devs/users.
- User/Dev → [`../cli/`](../cli/) to install the Work IQ CLI.
- Editor → [`../vscode-mcp/`](../vscode-mcp/) to configure it in VS Code.
