# Exemplos de uso (read-only)

> Catálogo de prompts em linguagem natural para usar com `workiq ask` no terminal **ou** com a tool `ask_work_iq` em qualquer cliente MCP (VS Code Copilot Chat, Copilot Studio, Azure AI Foundry, Claude…).

Todos os exemplos abaixo são **somente leitura**: consultam dados do Microsoft 365 via Microsoft Graph, sem alterar nenhum recurso.

---

## 📅 Calendário e reuniões

```bash
workiq ask -q "Quais reuniões tenho hoje?"
workiq ask -q "Quais reuniões tenho nas próximas 24 horas?"
workiq ask -q "Tenho alguma reunião esta semana com a equipe de vendas?"
workiq ask -q "Qual é o custo em horas das minhas reuniões recorrentes desta semana?"
workiq ask -q "Quem organizou a reunião de ontem às 14h?"
```

## 📧 E-mails

```bash
workiq ask -q "Resuma os e-mails não lidos de hoje por prioridade"
workiq ask -q "Existe algum e-mail do projeto X aguardando minha resposta?"
workiq ask -q "Quais e-mails na minha caixa são irrelevantes e poderiam ser arquivados?"
workiq ask -q "Existem e-mails urgentes da Sarah sobre o orçamento?"
```

## 📄 Documentos (SharePoint / OneDrive)

```bash
workiq ask -q "Encontre documentos que trabalhei nos últimos 3 dias"
workiq ask -q "Qual é o conteúdo da especificação do projeto Y no SharePoint?"
workiq ask -q "Quais arquivos do SharePoint estão relacionados ao cliente Contoso?"
workiq ask -q "Me liste os sites aos quais meu usuário tem acesso"
```

![CLI executando workiq ask sobre sites do SharePoint acessíveis ao usuário](../images/cli-ask-list-sharepoint-sites.png)

> 💡 Repare na resposta acima: o Work IQ **não é o Microsoft Graph**. Ele lista sites baseado em **evidência objetiva de acesso** (arquivos/páginas que você criou, editou ou visualizou) — não na lista exaustiva de permissões do tenant. Esse é um comportamento por design da camada de inteligência.

## 👥 Pessoas e organização

```bash
workiq ask -q "Quem é meu gestor?"
workiq ask -q "Quais são meus subordinados diretos?"
workiq ask -q "Quem está trabalhando no Projeto Alpha?"
workiq ask -q "Qual é o cargo de Maria Silva?"
workiq ask -q "Como entro em contato com o time de infraestrutura?"
```

## 💬 Teams

```bash
workiq ask -q "Resuma as mensagens de hoje no canal de Engenharia"
workiq ask -q "Houve alguma decisão importante no chat do squad ontem?"
```

---

## Cenários compostos

### 1. Radar de Semana (briefing executivo)

> Toda segunda-feira, gere um briefing completo da semana.

```bash
workiq ask -q "Crie um briefing executivo da minha semana: liste reuniões agendadas, e-mails prioritários não lidos, documentos modificados nos últimos 3 dias e calcule o custo total em horas de todas as reuniões desta semana"
```

### 2. Onboarding inteligente de novo colaborador

> Guia de onboarding personalizado, gerado a partir dos dados reais do tenant.

```bash
workiq ask -q "Sou novo no time de [ÁREA]. Com base nos e-mails, documentos e canais do Teams, me diga: quais são as pessoas-chave que devo conhecer, quais projetos estão ativos, quais documentos devo ler primeiro e quais foram as decisões importantes dos últimos 30 dias"
```

### 3. Auditor de reuniões desnecessárias

> Identifica reuniões recorrentes que poderiam virar updates assíncronos.

```bash
workiq ask -q "Analise minhas reuniões recorrentes dos últimos 30 dias. Para cada uma: liste participantes, duração média, frequência e se houve registro de decisões ou ações. Calcule o custo total em horas por pessoa e identifique quais poderiam ser substituídas por updates assíncronos"
```

### 4. Co-autor de Pull Request description (no VS Code)

> Com o MCP ativo no Copilot Chat, peça contexto antes de abrir o PR.

```
Busque no Work IQ todos os e-mails, reuniões e documentos relacionados à
feature [NOME]. Com esse contexto, gere uma descrição de PR completa
incluindo: contexto de negócio, decisões técnicas tomadas, quem deve
revisar e o link para a spec no SharePoint.
```

### 5. Contexto de cliente para suporte

```
Quais foram os últimos e-mails, reuniões e tickets relacionados ao
cliente Contoso? Resuma o status atual do relacionamento.
```

---

## Opções da CLI (`workiq ask`)

| Curta | Longa | Parâmetro | Descrição |
| --- | --- | --- | --- |
| `-q` | `--question` | `<question>` | Pergunta em linguagem natural. |
| `-f` | `--file-urls` | `<file-urls>` | URLs de arquivos do OneDrive/SharePoint para forçar como contexto raiz. |
| `-t` | `--tenant` | `<tenant-id>` | Tenant específico (quando você participa de mais de um). |
| `-v` | `--verbose` | — | Habilita logs detalhados (`request-id`, `conversation ID`). |
| `-d` | `--developer` | — | Modo desenvolvedor (payloads JSON brutos). |
| `-?, -h` | `--help` | — | Menu de ajuda. |

---

## Troubleshooting

| Erro | Causa | Solução |
| --- | --- | --- |
| `AADSTS650052` | Service Principal não provisionado | Execute [`Enable-WorkIQToolsForTenant.ps1`](../tenant-setup/Enable-WorkIQToolsForTenant.ps1) |
| `Access Denied` | Sem licença Copilot ou sem consent | Verifique licença em `admin.microsoft.com` e refaça o admin consent |
| MCP não aparece no Copilot Studio | Frontier não habilitado | Ative via Admin Center → Serviços → Microsoft 365 Insider |
| Cursor "piscando" após `workiq mcp` | Comportamento esperado — servidor aguardando cliente | Não é erro; configure no arquivo do agente (ver [../vscode-mcp/](../vscode-mcp/)) |
