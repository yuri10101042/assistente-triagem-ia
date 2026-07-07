# 🤖 Assistente Virtual para Triagem em Clínicas

Uma Prova de Conceito (PoC) de um assistente conversacional inteligente desenvolvido para automatizar o primeiro atendimento de pacientes, realizar triagem básica e direcionar agendamentos. 

Este projeto foi construído utilizando **n8n** (self-hosted via Docker) integrado à API da **OpenAI**, processando linguagem natural para entregar respostas precisas e humanizadas de acordo com regras de negócio estritas.

## ⚙️ Arquitetura do Fluxo

O sistema opera em um fluxo de três etapas principais:
1. **Gatilho (Webhook/Chat):** Recebe o input (mensagem) do paciente.
2. **Processamento (LLM):** A mensagem é enviada para o modelo da OpenAI, que processa a intenção baseada em um *System Prompt* rigoroso de restrições médicas e tom de voz.
3. **Tratamento de Dados:** O payload (JSON) retornado pela IA é filtrado para extrair apenas o texto final, garantindo uma entrega limpa para a interface do usuário.

## 🛠️ Tecnologias Utilizadas

*   **n8n:** Orquestração do fluxo e interface de chat.
*   **OpenAI API:** Motor de Inteligência Artificial.
*   **Docker:** Conteinerização do ambiente local para execução segura e isolada.

## 📸 Demonstração

**Arquitetura do Fluxo:**
![Arquitetura do Fluxo no n8n](preview-arquitetura.png)

**Resultado do Atendimento:**
![Chat da IA funcionando](preview-chat.png)

## 🚀 Como Rodar Localmente

1. Garanta que você possui o Docker instalado.
2. Clone este repositório.
3. Inicie o n8n via terminal com o comando abaixo:
   
   `docker run -it --rm --name n8n -p 5678:5678 -v n8n_data:/home/node/.n8n docker.n8n.io/n8nio/n8n`

4. Acesse localhost:5678, importe o arquivo .json do fluxo e insira sua chave de API da OpenAI no bloco do modelo.