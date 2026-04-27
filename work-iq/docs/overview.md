# O que é o Work IQ

> Visão conceitual em pt-BR baseada na [documentação oficial da Microsoft](https://learn.microsoft.com/en-us/microsoft-365/copilot/extensibility/workiq-overview) e no repositório [microsoft/work-iq](https://github.com/microsoft/work-iq).

O **Microsoft Work IQ** é a camada de inteligência que conecta assistentes de IA aos dados do Microsoft 365 Copilot. Ele expõe **e-mails, reuniões, documentos, mensagens do Teams e insights organizacionais** via **Model Context Protocol (MCP)** — o mesmo protocolo aberto usado por Claude, VS Code, GitHub Copilot e outros clientes de IA.

> ⚠️ **Public Preview.** Funcionalidades e APIs podem mudar. Requer licença **Microsoft 365 Copilot add-on** e o **Programa Frontier** habilitado no tenant.

> 🔒 **Somente leitura.** Este diretório cobre exclusivamente o uso **read-only** do Work IQ (consultas via CLI e MCP). Operações de escrita não fazem parte do produto nativo e estão fora do escopo aqui.

---

## Arquitetura em três camadas

| Camada | O que faz |
| --- | --- |
| **Data** | Indexação semântica de e-mails, documentos, chats e arquivos (SharePoint, OneDrive, Outlook, Teams). |
| **Memory** | Contexto persistente — prioridades, padrões de colaboração, histórico de trabalho. |
| **Inference** | Modelos de linguagem + ferramentas MCP + agentes — raciocínio sobre os dados. |

Tudo é **recuperado sob demanda**: o Work IQ não armazena dados do M365.

---

## Como o Work IQ se encaixa

```
Você (VS Code, Copilot CLI, Copilot Studio, Claude, Foundry…)
      │
      ▼
Cliente MCP  ──────►  Work IQ MCP Server  ──────►  Microsoft Graph
                       (autentica como você)         (e-mails, calendário,
                                                      docs, Teams, pessoas)
```

O Work IQ opera em modelo **Delegated Permissions**: ele assume a identidade do usuário conectado (token OAuth/JWT). Se você não tem permissão para ler a caixa do CEO no Outlook, a IA também não terá. Políticas de **Acesso Condicional** (MFA, conformidade de dispositivo) configuradas no Microsoft Entra ID são aplicadas de ponta a ponta.

---

## Repositório oficial

| | `microsoft/work-iq` |
| --- | --- |
| **O que é** | CLI + MCP server + plugins prontos |
| **Público-alvo** | Usuários e devs que consomem dados do M365 via IA |
| **Linguagem** | TypeScript / Node.js (pacote NPM `@microsoft/workiq`) |
| **Autenticação** | Automática via credenciais M365 |
| **Tool MCP exposta** | `ask_work_iq` (linguagem natural) |

### Estrutura do repo oficial

```
microsoft/work-iq/
├── scripts/
│   ├── Enable-WorkIQToolsForTenant.ps1   ← habilita MCPs + admin consent
│   └── Verify-WorkIQSetup.ps1            ← diagnóstico somente leitura
├── plugins/
│   ├── workiq/                           ← plugin principal
│   │   ├── .mcp.json                     ← config do servidor MCP
│   │   └── skills/workiq/SKILL.md        ← define quando/como usar ask_work_iq
│   ├── workiq-productivity/              ← insights de produtividade
│   │   ├── email-analytics
│   │   ├── meeting-cost-analyzer
│   │   ├── org-chart
│   │   └── channel-audit
│   └── microsoft-365-agents-toolkit/     ← scaffolding de agentes declarativos
├── server.json                           ← definição do MCP server (npm package)
├── README.md
├── ADMIN-INSTRUCTIONS.md
└── PLUGINS.md
```

---

## Modos de execução

### `workiq ask` — humanos no terminal

```bash
workiq ask
# Chat interativo no terminal — você digita perguntas em linguagem natural.
# Apenas leitura: não cria, altera ou exclui nada.
```

### `workiq mcp` — agentes de IA

Quando você roda `workiq mcp`, o cursor "fica piscando" porque o processo virou um **servidor de fundo**. Ele não espera input humano: aguarda que um cliente MCP (VS Code, Claude, Copilot Studio, Azure AI Foundry) envie comandos JSON-RPC.

```
Você (interface do agente)
    ↓ "Quais reuniões tenho amanhã?"
Cliente MCP (VS Code Copilot, Claude, Foundry…)
    ↓ inicia: workiq mcp (em background)
    ↓ envia JSON-RPC: tools/call → ask_work_iq
Work IQ MCP Server
    ↓ autentica com Microsoft Entra
    ↓ consulta Microsoft Graph
    ↓ retorna JSON
Cliente MCP
    ↓ formata a resposta
Você ← resposta legível
```

> 💡 `workiq mcp` **não é um comando para você digitar respostas**: é um endpoint para clientes MCP. Ele entra em arquivos como `.mcp.json`, `mcp-config.json` ou `settings.json` do VS Code.

---

## Segurança e governança

- **Sem armazenamento:** nenhum dado do M365 é persistido — tudo é fetched sob demanda.
- **Delegated Permissions:** o servidor herda exatamente as permissões do usuário autenticado.
- **Admin visibility:** administradores controlam o uso via `admin.microsoft.com → Agentes e Ferramentas` (Permitir / Bloquear por política).
- **Auditoria:** logs de chamadas MCP ficam disponíveis em **Microsoft Defender → Advanced Hunting**.
- **Tokens JWT:** expiram em ~1 hora; nunca os armazene em código ou repositórios.

---

## Pré-requisitos

### Obrigatórios

- **Licença Microsoft 365 Copilot add-on** — sem ela, toda chamada retorna `Access Denied`.
- **Node.js ≥ 18** ([nodejs.org](https://nodejs.org/)) — o WorkIQ usa `npx` para rodar o servidor MCP.
- **Programa Frontier habilitado** no tenant — os MCP Servers do Work IQ fazem parte do preview Frontier.
- **Permissão de Administrador** no tenant para o admin consent inicial (Global Admin, Cloud Application Admin ou Application Admin).

### Opcionais

- **GitHub Copilot CLI** — para usar Work IQ como plugin no terminal.
- **VS Code + GitHub Copilot Chat** — para usar Work IQ como MCP server no editor.
- **Copilot Studio** — para criar agentes low-code.
- **Azure AI Foundry** — para desenvolvimento pro-code de agentes.
- **PowerShell 7+** — para os scripts de habilitação do tenant.

---

## Próximos passos

1. **Admin do tenant:** [../tenant-setup/](../tenant-setup/) — habilite Frontier e faça o admin consent.
2. **Usuário/Dev:** [../cli/](../cli/) — instale o Work IQ CLI.
3. **Editor:** [../vscode-mcp/](../vscode-mcp/) — configure o MCP no VS Code.
4. **Casos de uso:** [./examples.md](./examples.md) — exemplos prontos de perguntas (read-only).

---

## Referências oficiais

- [Microsoft Work IQ CLI (preview)](https://learn.microsoft.com/en-us/microsoft-365/copilot/extensibility/workiq-overview)
- [Work IQ MCP Overview — Microsoft Agent 365](https://learn.microsoft.com/en-us/microsoft-agent-365/tooling-servers-overview)
- [Work IQ no Copilot Studio](https://learn.microsoft.com/en-us/microsoft-copilot-studio/use-work-iq)
- [microsoft/work-iq (repo oficial)](https://github.com/microsoft/work-iq)
- [ADMIN-INSTRUCTIONS.md (oficial)](https://github.com/microsoft/work-iq/blob/main/ADMIN-INSTRUCTIONS.md)
- [User and Admin Consent Overview — Microsoft Entra](https://learn.microsoft.com/en-us/entra/identity/enterprise-apps/user-admin-consent-overview)
- [Model Context Protocol — Spec oficial](https://modelcontextprotocol.io/)
