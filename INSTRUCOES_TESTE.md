# Instruções de Teste - Marmitaria Sabor Caseiro

## 🌐 Acesso Online ao Chat

**Link direto para teste:** https://teste-logmanager.vercel.app/test/test-chat.html

### Alternativa: Acesso Local

1. Abra o arquivo `test/test-chat.html` em qualquer navegador moderno (Chrome, Firefox, Edge, Safari)
2. O chat iniciará automaticamente com a mensagem de boas-vindas
3. Digite seu nome quando solicitado
4. Comece a fazer seu pedido!

## URL do Webhook N8N

```
https://nexttech-n8n-nexthub.haas2a.easypanel.host/webhook/marmitaria-chat
```

## Fluxos de Teste Sugeridos

### Teste 1: Pedido Simples

```
Você: João
Bot: [Saudação]

Você: Ver o cardápio completo
Bot: [Mostra cardápio com 8 opções]

Você: Quero a marmita tradicional
Bot: [Confirma escolha e pergunta sobre personalizações]

Você: Confirmar pedido
Bot: [Confirma e registra no Google Sheets]
```

### Teste 2: Pedido com Personalizações

```
Você: Maria
Bot: [Saudação]

Você: Quero uma marmita fitness sem feijão e com ovo frito
Bot: [Calcula: R$ 20,00 + R$ 2,00 = R$ 22,00]

Você: Confirmar pedido
Bot: [Registra com personalizações]
```

### Teste 3: Pedido com Remoções e Adições

```
Você: Pedro
Bot: [Saudação]

Você: Marmita strogonoff sem batata palha, trocar arroz por macarrão e adicionar bacon
Bot: [Calcula: R$ 21,00 + R$ 3,00 = R$ 24,00]

Você: Confirmar pedido
Bot: [Registra corretamente]
```

### Teste 4: Exploração do Cardápio

```
Você: Ana
Bot: [Saudação]

Você: O que tem de opção fitness?
Bot: [Explica Marmita Fitness]

Você: E vegetariano?
Bot: [Explica Marmita Vegana]

Você: Quero a vegana
Bot: [Confirma R$ 17,00]

Você: Confirmar pedido
Bot: [Registra]
```

### Teste 5: Múltiplas Trocas

```
Você: Carlos
Bot: [Saudação]

Você: Marmita executiva com arroz integral, sem vinagrete, adicionar salada extra
Bot: [Calcula: R$ 22,00 + R$ 2,00 = R$ 24,00]

Você: Confirmar pedido
Bot: [Registra com todas as alterações]
```

## O Que Observar Durante os Testes

### 1. Compreensão de Linguagem Natural
- O agente entende sinônimos? (ex: "quero", "vou querer", "me dá")
- Aceita erros de digitação?
- Entende abreviações? (ex: "strog" para strogonoff)

### 2. Cálculo de Valores
- Os adicionais são somados corretamente?
- As remoções não alteram o preço base?
- O valor total está sempre correto?

### 3. Registro de Pedidos
- Acesse a planilha Google Sheets configurada no workflow
- Verifique se os pedidos confirmados aparecem com:
  - Data/Hora correta
  - Session ID único
  - Nome do cliente
  - Prato escolhido
  - Ingredientes finais (após personalizações)
  - Valor total correto
  - Status "confirmado"

### 4. Memória Conversacional
- O agente lembra do nome?
- Lembra de pedidos anteriores na mesma sessão?
- Mantém contexto entre mensagens?

### 5. Confirmação de Pedido
- Só registra após o cliente digitar "Confirmar pedido"
- Não registra se o cliente desistir
- Exibe resumo completo antes da confirmação

## Problemas Conhecidos e Soluções

### Erro: "Não foi possível conectar ao servidor"
**Solução:** Verifique se o workflow está ativo no N8N

### Erro: "Configure a URL do webhook"
**Solução:** O WEBHOOK_URL no HTML ainda está com valor padrão

### Mensagem não aparece no chat
**Possíveis causas:**
1. Workflow pausado
2. Credenciais da OpenAI inválidas
3. Erro no Response Body do node "Responder ao Cliente"

### Pedido não aparece no Google Sheets
**Possíveis causas:**
1. Node "Registrar Pedido - Google Sheets" está desabilitado
2. Credenciais do Google não autorizadas
3. Planilha foi deletada ou movida

## Verificação de Logs

### No Navegador (Console do Desenvolvedor)
1. Abra DevTools (F12)
2. Vá na aba "Console"
3. Observe os logs:
   - "Resposta recebida:" mostra o JSON retornado
   - Erros de conexão aparecerão aqui

### No N8N
1. Acesse o workflow
2. Clique em "Executions" no menu superior
3. Veja o histórico de execuções
4. Clique em uma execução para ver o fluxo detalhado
5. Cada node mostra input/output

## Comandos Especiais de Teste

| Comando | O Que Testa |
|---------|-------------|
| "Ver cardápio" | Exibição completa do menu |
| "Quero a [nome]" | Seleção direta de prato |
| "Sem [ingrediente]" | Remoção de itens |
| "Com [adicional]" | Adição de extras |
| "Trocar [A] por [B]" | Substituição de ingredientes |
| "Confirmar pedido" | Finalização e registro |

## Cenários de Erro Para Testar

1. **Pedido Vazio**: Tentar confirmar sem escolher nada
2. **Item Inexistente**: Pedir algo que não está no cardápio
3. **Personalização Impossível**: Pedir algo incompatível
4. **Desistência**: Iniciar pedido e não confirmar

## Tempo Médio de Resposta

- **Primeira mensagem**: ~2-3 segundos
- **Mensagens seguintes**: ~1-2 segundos
- **Confirmação com registro**: ~3-4 segundos

## Checklist de Avaliação

- [ ] Chat carrega corretamente
- [ ] Saudação inicial funciona
- [ ] Captura do nome funciona
- [ ] Cardápio é exibido quando solicitado
- [ ] Agente entende pedidos em linguagem natural
- [ ] Cálculos de valores estão corretos
- [ ] Personalizações são aceitas e processadas
- [ ] Confirmação exige comando explícito
- [ ] Pedidos são registrados no Google Sheets
- [ ] Dados registrados estão completos e corretos
- [ ] Memória conversacional funciona
- [ ] Tratamento de erros está adequado
- [ ] Interface é clara e intuitiva

## Contato em Caso de Problemas

Se encontrar algum problema durante os testes, documente:
1. O que você tentou fazer
2. O que esperava que acontecesse
3. O que realmente aconteceu
4. Print da tela (se possível)
5. Logs do console (se disponível)

---

**Boa avaliação!** 🍱
