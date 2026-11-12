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

## 📌 O Problema

A **Empresa X** deseja automatizar seu canal de atendimento ao cliente. Atualmente, quando um cliente preenche o formulário de contato no site, os dados ficam perdidos em uma caixa de e-mail genérica.

### 🎯 Missão
Criar um workflow no **n8n** que receba os dados do formulário via **Webhook**, analise o nível de urgência da solicitação e:
- **Se a urgência for `"ALTA"`:** Envie imediatamente um alerta de emergência com emojis e destaque no Telegram da equipe de plantão.
- **Se a urgência for `"MÉDIA"` ou `"BAIXA"`:** Envie uma notificação simples de acompanhamento normal de rotina.

---

## 🏗️ Arquitetura do Workflow no n8n

O fluxo de automação segue a sequência visual abaixo:

```text
[ Webhook Trigger ] > [ Code / Edit Fields ] > [ If (Decisão) ] ──> (True - Urgente) ──> [ Telegram Alert ]
                                                                └──> (False - Normal) ──> [ Telegram Message ]
```

### Estrutura do Payload JSON (POST)
O Webhook aguarda uma requisição HTTP do tipo `POST` com um corpo (`body`) em formato JSON contendo a seguinte estrutura:

```json
{
  "nome_cliente": "Maria Oliveira",
  "email": "maria@empresa.com",
  "categoria": "Suporte Técnico",
  "urgencia": "ALTA",
  "mensagem": "Servidor principal fora do ar!"
}
```

---

## 🛠️ Passos de Desenvolvimento

- **P1: Criar o Bot no Telegram** — Criação do bot via `@BotFather`, obtenção do Token da API e identificação do `Chat ID`.
- **P2: Configurar o Trigger de Webhook no n8n** — Configuração do nó `Webhook Trigger` para escutar requisições `POST` no caminho `/atendimento-cliente`.
- **P3: Enviar o Formulário de um site ou uma Requisição POST no Postman** — Desenvolvimento da página web ([`index.html`](./index.html)) com formulário integrado via `fetch()` enviando o JSON ao Webhook do n8n.
- **P4: Tratamento de Dados e Validação** — Uso do nó `Code` (`Tratamento e Validação`) para higienizar os campos recebidos, definir valores padrão caso algum dado esteja vazio, padronizar a `urgencia` em caixa alta e registrar a data/hora do recebimento (`America/Sao_Paulo`).
- **P5: Tomada de Decisão (Nó If)** — Verificação condicional (`$json.urgencia == "ALTA"`) separando chamados críticos dos chamados de rotina.
- **P6: Notificação no Telegram** — Disparo da mensagem formatada em Markdown pelo bot do Telegram conforme a prioridade (`Telegram Alert` para emergências e `Telegram Message` para chamados normais).

---

## 📂 Estrutura do Repositório

```text
├── index.html               # Interface web do formulário de solicitação de atendimento (Passo P3)
├── workflow-telegram.json   # Workflow exportado do n8n com a automação completa (Passos P2, P4, P5 e P6)
└── README.md                # Documentação da atividade
```

---

## 🚀 Como Executar e Testar

1. **Importar o Workflow no n8n:**
   - No n8n, importe o arquivo [`workflow-telegram.json`](./workflow-telegram.json).
   - Nos nós **Telegram Alert** e **Telegram Message**, selecione sua credencial do Bot do Telegram e substitua `SEU_CHAT_ID_AQUI` pelo seu `Chat ID`.
2. **Testar o Envio pelo Site (`index.html`) ou Postman:**
   - Verifique a variável `WEBHOOK_URL` no arquivo [`index.html`](./index.html) com a URL do seu Webhook no n8n.
   - Clique em **Listen for test event** no n8n.
   - Abra o [`index.html`](./index.html) no navegador, preencha os campos e envie solicitações com urgência `ALTA`, `MÉDIA` e `BAIXA` para observar o roteamento no n8n e as notificações no Telegram.
