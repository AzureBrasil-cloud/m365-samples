# Work IQ MCP no VS Code (read-only)

Configure o **GitHub Copilot Chat** do VS Code (ou qualquer outro cliente MCP) para consumir o **Work IQ MCP Server**. A IA passa a ter acesso somente leitura aos seus e-mails, calendário, documentos, Teams e organização — sem nunca alterar nada.

---

## Pré-requisitos

- **VS Code** atualizado com a extensão **GitHub Copilot Chat**.
- **Node.js ≥ 18** instalado (para `npx`).
- Tenant configurado pelo admin → [`../tenant-setup/`](../tenant-setup/).
- Licença **Microsoft 365 Copilot add-on** ativa.
- (Opcional) `workiq accept-eula` rodado uma vez — ver [`../cli/`](../cli/).

> Você **não precisa** instalar `@microsoft/workiq` globalmente. O snippet abaixo usa `npx`, que baixa o pacote sob demanda.

---

## O snippet

O arquivo [`mcp-config.json`](./mcp-config.json) é o snippet **único e portável** que serve tanto para o VS Code quanto para qualquer outro cliente MCP (Claude Desktop, Azure AI Foundry, Copilot Studio em config avançada, agentes customizados):

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

## Configuração no VS Code

O VS Code lê configurações MCP de um arquivo `mcp.json`. Há duas opções:

### Opção A — Workspace (recomendado)

Crie `.vscode/mcp.json` na raiz do seu projeto e cole o conteúdo de [`mcp-config.json`](./mcp-config.json).

### Opção B — User-wide

`Cmd/Ctrl + Shift + P` → **MCP: Open User Configuration** → cole o conteúdo de [`mcp-config.json`](./mcp-config.json).

### Ativar no Copilot Chat

1. Reinicie o VS Code.
2. No painel do Copilot Chat → ícone 🔧 **Ferramentas** → marque **`workiq`**.
3. A primeira chamada vai pedir consentimento de login Microsoft 365.

---

## Outros clientes MCP

O mesmo arquivo [`mcp-config.json`](./mcp-config.json) funciona em qualquer cliente que leia o formato MCP padrão. Exemplos:

| Cliente | Onde colar |
| --- | --- |
| **Claude Desktop** | `claude_desktop_config.json` |
| **Azure AI Foundry** | Configuração de agente → Tools → MCP |
| **Copilot Studio** | Tools → Add MCP server (config avançada) |
| **Agente customizado** | O arquivo que seu cliente MCP convencionar |

---

## Como usar

Com o `workiq` ativo, basta perguntar em linguagem natural no Copilot Chat:

```
Quais são minhas reuniões de hoje?
Resuma os e-mails não lidos por prioridade.
Quais documentos do SharePoint estão relacionados ao cliente Contoso?
Quem é meu gestor?
Liste decisões tomadas nas últimas reuniões com o time de Engenharia.
```

### Cenário típico — contexto para código

```
Antes de eu implementar a feature de checkout:
1. Busque no Work IQ todas as reuniões, e-mails e specs sobre "checkout v2".
2. Resuma os requisitos discutidos.
3. Liste quem deve ser adicionado como reviewer no PR.
```

Mais exemplos: [`../docs/examples.md`](../docs/examples.md).

---

## Troubleshooting

| Sintoma | Causa provável | Ação |
| --- | --- | --- |
| `workiq` não aparece nas Ferramentas | `mcp.json` não foi salvo / VS Code não reiniciado | Salve o arquivo e reinicie o VS Code. |
| `Access Denied` na primeira chamada | Sem licença Copilot ou consent não feito | Veja [`../tenant-setup/`](../tenant-setup/). |
| `command not found: npx` | Node.js não instalado | Instale Node.js ≥ 18. |
| Respostas vazias ou "não tenho acesso" | O usuário logado realmente não tem permissão Graph para o recurso | Esperado — Work IQ herda permissões delegadas. |

---

## Arquivos deste diretório

| Arquivo | Descrição |
| --- | --- |
| [mcp-config.json](./mcp-config.json) | Snippet MCP único — funciona no VS Code (`.vscode/mcp.json`) e em qualquer outro cliente MCP. |
