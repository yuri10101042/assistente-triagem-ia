# Assistente de triagem pra clínicas

Montei isso pra mostrar um tipo de automação que faz sentido de verdade: o paciente fala no Telegram, o bot faz a triagem inicial e, no fim, o lead fica organizado pra recepção.

Não é sistema hospitalar. É uma PoC de atendimento com n8n + OpenAI + Telegram, com a opção de gravar os dados no Google Sheets.

## Em resumo

O bot pergunta, com calma:

1. nome
2. queixa
3. horário de preferência

Quando isso estiver completo, ele resume pra pessoa e monta um lead estruturado. Se você ligar o nó do Sheets, esses dados caem numa planilha.

## Por que Telegram

Quis evitar a dor de cabeça da API oficial do WhatsApp/Meta. Telegram dá pra testar rápido e já demonstra o fluxo ponta a ponta.

## Arquivos

- `workflow-triagem.json` — fluxo do n8n
- `system-prompt.txt` — regras da “recepcionista” (tom, limites, o que pode e o que não pode)
- `exemplos/leads-modelo.csv` — modelo das colunas da planilha
- `preview-*.jpeg` — prints (podem estar um pouco atrás do fluxo atual)

## Prints

Fluxo no n8n:

![Fluxo no n8n](preview-arquitetura.jpeg)

Conversa:

![Exemplo de conversa](preview-chat.jpeg)

## Como rodar

Sobe o n8n:

```bash
docker run -it --rm --name n8n -p 5678:5678 -v n8n_data:/home/node/.n8n docker.n8n.io/n8nio/n8n
```

Abre `http://localhost:5678`, cria as credenciais (OpenAI e Telegram) e importa o `workflow-triagem.json`.

Depois é só ativar o workflow e mandar mensagem pro bot.

### Planilha (opcional)

1. Cria uma planilha com a aba `Leads`
2. Usa os cabeçalhos do `exemplos/leads-modelo.csv`
3. No nó **Salvar Lead na Planilha**, troca o ID placeholder pelo ID da sua planilha
4. Ativa o nó (ele vem desligado de propósito)

## Como o lead é gerado

Quando a triagem fecha, o modelo manda um bloco técnico junto da resposta. O fluxo remove isso antes de enviar pro paciente e usa só os dados:

```text
<<<TRIAGEM>>>
{"nome":"...","queixa":"...","horario_preferido":"...","status":"completo"}
<<<FIM>>>
```

## Stack

n8n, OpenAI (`gpt-4o-mini`), Telegram, Google Sheets (opcional), Docker.

## Limitações

- Não marca consulta de verdade no sistema da clínica
- A memória da conversa fica no n8n; se reiniciar, pode resetar
- Não substitui médico nem recepção humana
- Usei IA no desenvolvimento — o foco é o fluxo e a utilidade, não “parece mágica”

## Autor

Yuri Carvalhais. Curtindo automação, integração e backend.
