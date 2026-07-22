# Assistente de Triagem para Clínicas (n8n + IA + Telegram)

PoC de automação de atendimento inicial: um bot no **Telegram** conduz a triagem (nome, queixa, preferência de horário) com **OpenAI**, respeita limites de recepção (sem diagnóstico) e, ao concluir, prepara o lead para registro em **Google Sheets**.

Útil como demonstração de automação vendável: canal de conversa → regras de negócio → dado estruturado na planilha da recepção.

## O que resolve

| Antes | Depois |
| --- | --- |
| Paciente manda mensagem sem padrão | Conversa guiada, uma pergunta por vez |
| Dados se perdem no chat | Lead estruturado (`nome`, `queixa`, `horario_preferido`) |
| Recepção responde tudo manualmente | Triagem inicial automatizada 24/7 (confirmação humana no fim) |

## Arquitetura

```text
Telegram → memória da conversa → OpenAI (gpt-4o-mini)
        → resposta limpa ao paciente
        → se triagem completa → Google Sheets (opcional)
```

Arquivos principais:

- `workflow-triagem.json` — fluxo n8n pronto para importar
- `system-prompt.txt` — regras de negócio / tom / limites clínicos
- `exemplos/leads-modelo.csv` — colunas sugeridas da planilha
- `preview-*.jpeg` — prints do fluxo e do chat

## Demonstração

**Fluxo no n8n:**

![Arquitetura do fluxo no n8n](preview-arquitetura.jpeg)

**Conversa de atendimento:**

![Chat da IA funcionando](preview-chat.jpeg)

> Os prints podem refletir uma versão anterior do canvas. O JSON atual inclui nós de memória, extração de lead e Sheets (desativado por padrão).

## Como rodar

### 1. Subir o n8n

```bash
docker run -it --rm --name n8n -p 5678:5678 -v n8n_data:/home/node/.n8n docker.n8n.io/n8nio/n8n
```

Acesse `http://localhost:5678`.

### 2. Credenciais no n8n

1. **OpenAI** — API key
2. **Telegram** — bot token (via [@BotFather](https://t.me/BotFather))
3. **Google Sheets** (opcional) — OAuth da conta Google

### 3. Importar o fluxo

1. Import Strategies → `workflow-triagem.json`
2. Relacione as credenciais nos nós Telegram e OpenAI
3. Ative o workflow e envie `/start` (ou qualquer texto) ao bot

### 4. Planilha (opcional, recomendado para demo completa)

1. Crie uma planilha com a aba `Leads`
2. Cabeçalhos iguais a `exemplos/leads-modelo.csv`
3. No nó **Salvar Lead na Planilha**: troque `SUBSTITUA_PELO_ID_DA_PLANILHA` pelo ID do documento
4. Ative o nó (está `disabled` de propósito no export)

## Como a triagem vira lead

Quando nome + queixa + horário estão completos, o modelo inclui um bloco técnico na resposta:

```text
<<<TRIAGEM>>>
{"nome":"...","queixa":"...","horario_preferido":"...","status":"completo"}
<<<FIM>>>
```

O nó **Extrair Lead e Memoria** remove esse bloco antes de enviar ao Telegram e marca `leadCompleto = true` para gravar na planilha.

## Stack

- n8n (orquestração)
- OpenAI (`gpt-4o-mini`)
- Telegram Bot API
- Google Sheets (opcional)
- Docker

## Limitações (PoC)

- Não confirma agenda real no sistema da clínica
- Memória da conversa fica no static data do workflow (reinício do n8n pode limpar)
- Não substitui recepção humana nem atendimento médico
- Prints do README podem estar desatualizados em relação ao canvas mais recente

## Autor

Yuri Carvalhais — automação de processos, integrações e backends.
