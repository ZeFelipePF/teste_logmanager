# Agente de IA para Marmitaria Sabor Caseiro

> Desafio Técnico - Engenheiro de IA | LogManager

## Visão Geral

Sistema de atendimento automatizado para pedidos de marmitas utilizando Inteligência Artificial. O projeto implementa um agente conversacional capaz de apresentar cardápio, aceitar personalizações e registrar pedidos de forma totalmente automatizada.

## Tecnologias Utilizadas

- **N8N**: Orquestração do fluxo de automação
- **OpenAI GPT-3.5-turbo**: Modelo de linguagem para conversação
- **Google Sheets**: Registro persistente de pedidos
- **Redis**: Gerenciamento de sessões e memória conversacional
- **HTML/CSS/JavaScript**: Interface de chat do cliente

## Arquitetura do Sistema

### Fluxo Principal

```
Cliente (HTML/JS)
    ↓
Webhook N8N
    ↓
Preparar Contexto IA
    ↓
AI Agent (GPT-3.5 + Memória)
    ↓
Verificar Confirmação de Pedido
    ↓
┌─────────────────┬─────────────────┐
│  SIM            │  NÃO            │
│                 │                 │
│  AI Agent       │  Salvar Redis   │
│  (Estruturar)   │                 │
│      ↓          │                 │
│  Processar      │                 │
│  Resposta       │                 │
│      ↓          │                 │
│  Google Sheets  │                 │
│      ↓          │                 │
│  Limpar Redis   │                 │
└─────────┬───────┴─────────────────┘
          ↓
    Responder ao Cliente
```

### Componentes Principais

#### 1. Frontend (test-chat.html)
Interface web simples e intuitiva que permite:
- Captura do nome do cliente
- Envio de mensagens via webhook
- Exibição de respostas do agente
- Indicadores visuais de carregamento

#### 2. N8N Workflow (resolução_jose_felipe.json)

**Nodes principais:**

- **Webhook - Chat Público**: Recebe requisições POST do frontend
- **Preparar Contexto IA**: Extrai e estrutura dados da sessão
- **AI Agent1**: Agente principal com GPT-3.5 e memória conversacional
- **Pedido Confirmado?**: Detecta quando cliente confirma pedido
- **AI Agent (Estruturador)**: Extrai dados estruturados do pedido usando Output Parser
- **Processar Resposta IA**: Formata dados para registro
- **Google Sheets**: Registra pedidos confirmados
- **Redis**: Gerencia memória e estado das sessões

#### 3. Sistema de Memória
- Utiliza **Buffer Window Memory** do LangChain
- Mantém contexto conversacional por session_id
- Permite que o agente lembre de pedidos anteriores na mesma sessão

## Funcionalidades

### Cardápio Disponível

1. Marmita Tradicional - R$ 18,00
2. Marmita Frango - R$ 16,00
3. Marmita Fitness - R$ 20,00
4. Marmita Vegana - R$ 17,00
5. Marmita Executiva - R$ 22,00
6. Marmita Peixe - R$ 19,00
7. Marmita Kids - R$ 14,00
8. Marmita Strogonoff - R$ 21,00

### Personalizações Aceitas

**Trocas sem custo:**
- Arroz branco → arroz integral ou macarrão
- Feijão preto → feijão carioca
- Remoção de ingredientes

**Adicionais com custo:**
- Ovo frito: +R$ 2,00
- Bacon: +R$ 3,00
- Salada extra: +R$ 2,00
- Porção extra de proteína: +R$ 5,00

### Capacidades do Agente

- Apresentação inteligente do cardápio
- Entendimento de linguagem natural (aceita erros de digitação)
- Cálculo automático de valores com personalizações
- Confirmação explícita antes de finalizar
- Registro estruturado em planilha

## Registro de Pedidos

Cada pedido confirmado é salvo no Google Sheets com os seguintes campos:

| Campo | Descrição |
|-------|-----------|
| Data/Hora | Timestamp do pedido |
| Session ID | Identificador único da sessão |
| Nome Cliente | Nome informado pelo cliente |
| Pedido | Nome do prato escolhido |
| Itens do pedido | Lista completa de ingredientes finais |
| Valor Total | Valor total calculado |
| Status | Status do pedido (confirmado) |

## Como Testar

### Pré-requisitos

- N8N instalado e configurado
- Conta OpenAI com API Key válida
- Conta Google com acesso ao Google Sheets
- Redis (opcional, para memória persistente)

### Passo a Passo

1. **Importar o workflow no N8N**
   ```
   - Abra o N8N
   - Vá em "Workflows" > "Import from File"
   - Selecione o arquivo: test/resolução_jose_felipe.json
   ```

2. **Configurar credenciais**
   - OpenAI API: Adicione sua API Key
   - Google Sheets: Autentique sua conta Google
   - Redis: Configure conexão (se disponível)

3. **Ativar o workflow**
   - Clique em "Active" no canto superior direito
   - Copie a URL do webhook gerado

4. **Configurar o frontend**
   - Abra o arquivo `test/test-chat.html`
   - Na linha 269, altere a URL do webhook:
     ```javascript
     const WEBHOOK_URL = 'SUA_URL_AQUI';
     ```

5. **Testar o chat**
   - Abra `test-chat.html` no navegador
   - Informe seu nome
   - Converse com o agente e faça um pedido

### Exemplo de Conversa

```
Cliente: José
Bot: Olá, José! Que bom ter você aqui na Marmitaria Sabor Caseiro. 😊
     Como posso te ajudar hoje?
     📋 Ver o cardápio completo
     🍱 Fazer um pedido
     💬 Tirar dúvidas

Cliente: Ver o cardápio completo
Bot: [Exibe cardápio completo com preços]

Cliente: Quero a marmita strogonoff sem feijão e com ovo frito
Bot: Entendi! Seu pedido ficou assim:
     🍱 Marmita Strogonoff: R$ 21,00
     ➖ Sem feijão
     ➕ Ovo frito: R$ 2,00

     💰 Total: R$ 23,00

     Digite 'Confirmar pedido' para confirmar. ✅

Cliente: Confirmar pedido
Bot: Pedido confirmado! 🎉 Seu pedido foi registrado com sucesso.
     Obrigado e bom apetite!
```

## Estrutura do Projeto

```
Teste-Blue/
├── README.md                              # Este arquivo
├── test/
│   ├── resolução_jose_felipe.json        # Workflow N8N
│   ├── test-chat.html                    # Interface do chat
│   └── styles.css                        # Estilos do frontend
```

## Decisões Técnicas

### Por que N8N?
- Interface visual para fluxos complexos
- Integração nativa com LangChain
- Facilidade para conectar múltiplos serviços

### Por que GPT-3.5-turbo?
- Custo-benefício ideal para chatbots
- Velocidade de resposta adequada
- Capacidade suficiente para entender pedidos

### Por que Google Sheets?
- Fácil visualização e consulta
- Não requer banco de dados dedicado
- Permite análise posterior em ferramentas como Excel

### Estruturação de Dados
O workflow utiliza um segundo AI Agent com **Structured Output Parser** para extrair dados estruturados do pedido confirmado. Isso garante:
- Formato padronizado no registro
- Lista precisa de ingredientes finais
- Valores corretamente extraídos

## Melhorias Futuras

- [ ] Integração com sistema de pagamento
- [ ] Notificações via WhatsApp/Telegram
- [ ] Painel administrativo para gestão de pedidos
- [ ] Suporte a múltiplos idiomas
- [ ] Sistema de avaliação pós-pedido
- [ ] Cálculo de tempo de entrega
- [ ] Integração com sistemas de delivery

## Contato

**Desenvolvido por:** José Felipe
**Email:** [Seu email]
**Data:** Novembro/2025
**Desafio:** LogManager - Engenheiro de IA

---

## 🌐 Links para Teste

**🚀 Chat Online:** https://teste-logmanager.vercel.app/test/test-chat.html

**📦 Repositório GitHub:** https://github.com/ZeFelipePF/teste_logmanager

**🔗 Webhook N8N:** `https://nexttech-n8n-nexthub.haas2a.easypanel.host/webhook/marmitaria-chat`

## 📸 Prints do Workflow

Os prints do fluxo N8N estão disponíveis na pasta [test/prints/](test/prints/).

---

*Este projeto foi desenvolvido como parte do processo seletivo para Engenheiro de IA na LogManager.*
