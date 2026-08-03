# Assistente de Triagem para Clínicas

Prova de conceito de automação de atendimento inicial. O paciente conversa por Telegram, o fluxo conduz a triagem básica com OpenAI e, ao final, organiza os dados para a recepção — com opção de registro em Google Sheets.

O objetivo é demonstrar um caso prático de automação: conversa com regras de negócio claras e saída estruturada, sem pretender ser um sistema clínico completo.

## Escopo

O assistente coleta:

1. Nome do paciente  
2. Queixa principal  
3. Preferência de horário  

Em seguida, confirma o resumo com a pessoa e gera um lead estruturado. Se o nó do Google Sheets estiver ativo, os dados são gravados em planilha.

## Por que Telegram

A integração utiliza Telegram para facilitar testes e demonstração ponta a ponta, evitando a complexidade de configuração da API oficial do WhatsApp/Meta nesta PoC.

## Estrutura do repositório

- `workflow-triagem.json` — fluxo n8n  
- `system-prompt.txt` — instruções de tom, limites e conduta da recepção  
- `exemplos/leads-modelo.csv` — modelo de colunas da planilha  
- `preview-*.jpeg` — capturas de tela (podem corresponder a uma versão anterior do canvas)

## Demonstração

Fluxo no n8n:

![Fluxo no n8n](preview-arquitetura.jpeg)

Exemplo de conversa:

![Exemplo de conversa](preview-chat.jpeg)

## Como executar

Suba o n8n:

```bash
docker run -it --rm --name n8n -p 5678:5678 -v n8n_data:/home/node/.n8n docker.n8n.io/n8nio/n8n
```

Acesse `http://localhost:5678`, configure as credenciais de OpenAI e Telegram e importe `workflow-triagem.json`. Ative o workflow e inicie a conversa com o bot.

### Google Sheets (opcional)

1. Crie uma planilha com a aba `Leads`  
2. Utilize os cabeçalhos de `exemplos/leads-modelo.csv`  
3. No nó **Salvar Lead na Planilha**, substitua o ID placeholder pelo ID do documento  
4. Ative o nó (ele permanece desabilitado no export por padrão)

## Geração do lead

Ao concluir a triagem, o modelo inclui um bloco técnico na resposta. O fluxo remove esse bloco antes de enviar a mensagem ao paciente e utiliza os campos para registro:

```text
<<<TRIAGEM>>>
{"nome":"...","queixa":"...","horario_preferido":"...","status":"completo"}
<<<FIM>>>
```

## Tecnologias

n8n, OpenAI (`gpt-4o-mini`), Telegram Bot API, Google Sheets (opcional) e Docker.

## Limitações

- Não realiza agendamento efetivo no sistema da clínica  
- A memória da conversa depende do estado interno do n8n e pode ser reiniciada  
- Não substitui atendimento médico nem a recepção humana  
- Ferramentas de IA foram utilizadas no desenvolvimento; o foco do repositório é o fluxo e a aplicabilidade prática

## Autor

Yuri Carvalhais — desenvolvimento de software, automações e integrações.
