# 🤖 Automação de Atendimento ao Cliente com n8n e Telegram

Projeto desenvolvido como atividade prática para a disciplina de **Tópicos Especiais em Programação**.

---

## 🎓 Identificação Acadêmica

- **Instituição:** Instituto Federal do Piauí (IFPI)
- **Curso:** Tecnologia em Análise e Desenvolvimento de Sistemas (ADS)
- **Disciplina:** Tópicos Especiais em Programação
- **Professor:** Prof. Maykol Sampaio
- **Aluna:** Mikaelle Raulino Barroso

---

## 📌 Contexto da Atividade

Este projeto consiste em um fluxo de **automação de triagem e atendimento ao cliente** integrando uma interface web (formulário HTML/JS), um **Webhook no n8n** para processamento e validação dos dados, e o **Telegram** para envio de notificações em tempo real à equipe de suporte de acordo com o nível de urgência do chamado.

### Arquitetura do Fluxo

1. **Interface Web (`index.html`):**
   - Formulário de "Central de Atendimento" onde o cliente informa:
     - Nome Completo (`nome_cliente`)
     - E-mail (`email`)
     - Categoria (`Suporte Técnico`, `Financeiro`, `Comercial`, `Dúvidas Gerais`)
     - Nível de Urgência (`BAIXA`, `MÉDIA`, `ALTA`)
     - Mensagem detalhada (`mensagem`)
   - Envia os dados via requisição `POST` (JSON) para o endpoint Webhook do n8n.

2. **Workflow no n8n (`workflow-telegram.json`):**
   - **Webhook Trigger (`POST /atendimento-cliente`):** Recebe os dados enviados pelo formulário web.
   - **Tratamento e Validação (`Code Node`):** Higieniza as strings recebidas, define valores padrão caso algum campo esteja ausente, normaliza a urgência para letras maiúsculas e adiciona o carimbo de data/hora (`data_recebimento`) no fuso horário `America/Sao_Paulo`.
   - **Triagem Condicional (`If Node - Urgência ALTA?`):** Avalia se o campo `urgencia` é igual a `ALTA`.
     - **Ramo Verdadeiro (`Telegram Alert`):** Dispara um **Alerta de Emergência** destacado para o plantão no Telegram exigindo ação imediata.
     - **Ramo Falso (`Telegram Message`):** Registra e envia a notificação como um **Novo Chamado de Rotina** no Telegram.

---

## 📂 Estrutura do Repositório

```text
├── index.html               # Interface web do formulário de solicitação de atendimento
├── workflow-telegram.json   # Workflow exportado do n8n com a automação e integração Telegram
└── README.md                # Documentação da atividade
```

---

## 🚀 Como Executar e Testar

### 1. Importar e Configurar o Workflow no n8n
1. Acesse sua instância do **n8n** (Cloud ou local).
2. Crie um novo workflow e importe o arquivo [`workflow-telegram.json`](./workflow-telegram.json).
3. Nos nós **Telegram Alert** e **Telegram Message**:
   - Configure suas credenciais de Bot do Telegram (criado via `@BotFather`).
   - Substitua `SEU_CHAT_ID_AQUI` pelo seu `Chat ID` real do Telegram.
4. Copie a URL de teste (ou produção) gerada pelo nó **Webhook Trigger**.

### 2. Executar o Formulário Web
1. No arquivo [`index.html`](./index.html), verifique a constante `WEBHOOK_URL` (linha 151) e certifique-se de que corresponde à URL do seu Webhook no n8n:
   ```javascript
   const WEBHOOK_URL = "https://mikaellebarroso.app.n8n.cloud/webhook-test/atendimento-cliente";
   ```
2. No n8n, clique em **Listen for test event** (ou ative o workflow em produção).
3. Abra o arquivo `index.html` no navegador, preencha os dados do chamado (testando com urgência **ALTA** e depois **BAIXA** ou **MÉDIA**) e clique em **Enviar Solicitação**.
4. Verifique o recebimento das mensagens formatadas no Telegram!
