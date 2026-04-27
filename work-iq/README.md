# 🧠 Work IQ — Referência consolidada (pt-BR)

> Guia comunitário em português para colocar o **Microsoft Work IQ** em uso em um tenant Microsoft 365 — **somente leitura**.
>
> Conteúdo consolidado a partir do repositório [CarlosErutan/Work-IQ](https://github.com/CarlosErutan/Work-IQ) e da documentação oficial [microsoft/work-iq](https://github.com/microsoft/work-iq).

O **Microsoft Work IQ** é a camada de inteligência que conecta assistentes de IA aos dados do Microsoft 365 Copilot — e-mails, reuniões, documentos, mensagens do Teams e insights organizacionais — via **Model Context Protocol (MCP)**.

> 🔒 **Escopo deste diretório:** somente o uso **read-only** do Work IQ (CLI + MCP). Operações de escrita não fazem parte do produto nativo e estão **fora do escopo**.

> ⚠️ **Public Preview.** Funcionalidades e APIs podem mudar. Requer licença **Microsoft 365 Copilot add-on** e o **Programa Frontier** habilitado.

---

## Estrutura do diretório

```
work-iq/
├── README.md                           ← você está aqui
│
├── docs/                               ← explicação conceitual + exemplos
│   ├── README.md
│   ├── overview.md                     ← o que é, arquitetura, segurança
│   └── examples.md                     ← catálogo de prompts read-only
│
├── tenant-setup/                       ← Admin M365 prepara o tenant
│   ├── README.md                       ← passo a passo do admin consent
│   ├── Enable-WorkIQToolsForTenant.ps1 ← provisiona Service Principals
│   └── Verify-WorkIQSetup.ps1          ← diagnóstico somente leitura
│
├── cli/                                ← Work IQ CLI (terminal)
│   ├── README.md                       ← instalação + uso da CLI
│   ├── setup-workiq-cli.sh             ← bootstrap macOS/Linux
│   └── setup-workiq-cli.ps1            ← bootstrap Windows
│
└── vscode-mcp/                         ← Work IQ MCP no VS Code (e demais clientes MCP)
    ├── README.md                       ← config + uso no Copilot Chat
    └── mcp-config.json                 ← snippet MCP único (VS Code + outros)

images/                                 ← screenshots usadas na documentação
```

---

## Por onde começar

| Eu sou… | Comece por |
| --- | --- |
| Quero entender o produto | [docs/overview.md](./docs/overview.md) |
| Admin M365 preparando o tenant | [tenant-setup/](./tenant-setup/) |
| Dev/Usuário instalando localmente | [cli/](./cli/) |
| Quero usar no editor | [vscode-mcp/](./vscode-mcp/) |
| Procurando exemplos de prompts | [docs/examples.md](./docs/examples.md) |

---

## Fluxo end-to-end

```
1. Admin M365                          tenant-setup/
   └─ Habilita Frontier
   └─ Roda Enable-WorkIQToolsForTenant.ps1 (admin consent)
   └─ Verifica com Verify-WorkIQSetup.ps1
                │
                ▼
2. Usuário/Dev                          cli/
   └─ Instala @microsoft/workiq (script ou npm/npx)
   └─ workiq accept-eula
   └─ workiq ask -q "Quais reuniões tenho hoje?"
                │
                ▼
3. Editor (opcional)                    vscode-mcp/
   └─ Cria .vscode/mcp.json (ou User MCP config) com o snippet
   └─ Ativa "workiq" em Ferramentas do Copilot Chat
   └─ Pergunta em linguagem natural dentro do VS Code
```

---

## Pré-requisitos resumidos

- Licença **Microsoft 365 Copilot add-on** (sem ela: `Access Denied`).
- **Node.js ≥ 18**.
- **PowerShell 7+** para os scripts de tenant.
- **Programa Frontier** habilitado no tenant.
- **Admin** (Global / Cloud Application / Application) para o consent inicial.

Detalhes em [docs/overview.md](./docs/overview.md#pré-requisitos).

---

## Referências oficiais

- [Microsoft Work IQ CLI (preview)](https://learn.microsoft.com/en-us/microsoft-365/copilot/extensibility/workiq-overview)
- [Work IQ MCP Overview — Microsoft Agent 365](https://learn.microsoft.com/en-us/microsoft-agent-365/tooling-servers-overview)
- [Work IQ no Copilot Studio](https://learn.microsoft.com/en-us/microsoft-copilot-studio/use-work-iq)
- [microsoft/work-iq (repo oficial)](https://github.com/microsoft/work-iq)
- [Model Context Protocol — Spec oficial](https://modelcontextprotocol.io/)

---

> **Aviso:** referência comunitária em português, **não afiliada oficialmente à Microsoft**. O Work IQ é um produto Microsoft em Public Preview.
