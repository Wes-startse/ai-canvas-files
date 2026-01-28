# **Análise Técnica - AI Canvas**

**Projeto:** Recorrência  
**Autor(a):** Adriana Araujo  
**E-mail:** araujoadriana@yahoo.com.br

---

## **Resumo do Problema**

**Ferramentas já utilizadas:** Salesforce (Marketing Cloud + Data Cloud) e Vtex

**Micro-problemas identificados:**

1. **Comunicação genérica e massiva**: Emails sem personalização resultam em baixo engajamento
2. **Falta de segmentação comportamental**: Todos recebem as mesmas ofertas
3. **Ausência de automação em jornadas**: Processos manuais que não escalam
4. **Dificuldade em prever churn**: Não identifica clientes em risco de abandono
5. **Dependência de canais pagos**: 70% da receita vem de mídia paga

---

## **Sugestão de Implementação**

### **Solução 1: Sistema de Recomendação Inteligente**
**Integração:** Flowise + Salesforce + Vtex

**Setup:**
- Conectar Flowise via API aos dados do Salesforce Data Cloud
- Modelo recomendado: **GPT-4o** (melhor para análise de dados estruturados)
- **Componentes Flowise:**
  - Document Loader para histórico de compras
  - Conversational Retrieval QA Chain
  - **Structured Output Parser** (formatar JSON com produtos recomendados)
  - Buffer Memory para contexto

**Como funciona:**
1. Cliente acessa site/email
2. Flowise analisa histórico no Salesforce
3. Retorna top 3 produtos personalizados via API
4. Vtex exibe recomendações em tempo real

---

### **Solução 2: Automação de Carrinho Abandonado**
**Ferramentas:** N8N + Salesforce Marketing Cloud + ElevenLabs (opcional)

**Workflow N8N:**
1. **Webhook Vtex**: Detecta carrinho abandonado
2. **Delay Node**: Aguarda 30min
3. **Salesforce Node**: Busca histórico e preferências do cliente
4. **OpenAI Node**: Gera mensagem personalizada
5. **Switch Node**: Email OU WhatsApp OU SMS baseado em preferência
6. **ElevenLabs** (se WhatsApp): Converte texto em áudio natural

**Vantagem:** Aproveita infraestrutura Salesforce existente

---

### **Solução 3: Predição de Churn com DataRobot**

**Como implementar:**
1. Exportar dados do Salesforce Data Cloud (RFM, engajamento, NPS)
2. Upload no DataRobot para treinar modelo preditivo
3. DataRobot gera score de churn (0-100) para cada cliente
4. N8N puxa scores semanalmente via API
5. Atualiza campo "Risk_Score" no Salesforce
6. Marketing Cloud cria jornadas específicas por risco

**Segmentação automática:**
- Alto risco (>70): Oferta especial + contato humano
- Médio risco (40-70): Email com incentivo
- Baixo risco (<40): Comunicação padrão

---

### **Solução 4: Programa de Fidelidade Dinâmico**
**Ferramentas:** Salesforce + Make (alternativa ao N8N)

**Fluxo Make:**
1. Trigger diário busca clientes no Salesforce
2. Calcula pontos baseado em comportamento (compras, reviews, indicações)
3. GPT-4 via API gera recompensa personalizada por perfil
4. Envia notificação via canal preferido do cliente

**Exemplo:** Cliente fitness ganha desconto em suplementos; cliente casual ganha frete grátis

---

### **Pontos de Atenção**

**Integração:**
- Salesforce tem APIs robustas, mas atenção aos limites de chamadas (200k/24h no Marketing Cloud)
- Testar webhooks da Vtex em ambiente de staging primeiro
- Criar usuário de integração dedicado no Salesforce (não usar credenciais pessoais)

**Dados:**
- Salesforce Data Cloud precisa estar populado com mínimo 90 dias de histórico
- Limpar duplicatas e emails inválidos antes de ativar automações
- Mapear campos customizados antes de treinar modelos no DataRobot

**Custos adicionais:**
- N8N Cloud: $20-50/mês (ou self-hosted gratuito)
- OpenAI API: ~$30-100/mês dependendo volume
- DataRobot: Solicitar trial (licença pode ser cara para PME)
- ElevenLabs: $5-22/mês

**Governança (LGPD):**
- Adicionar opt-out em todas as automações
- Documentar lógica de segmentação para auditoria
- Revisar mensagens geradas antes de escalar

---

## **Exemplo de Recomendação**

### **Caso: Cliente Carolina - Recuperação Inteligente**

**Contexto:**  
Carolina abandonou carrinho com tênis running (R$350) + garrafa térmica (R$78) há 3 horas. Ela é cliente nova, primeira interação no site.

**Automação em Ação:**

**Step 1: Detecção (Webhook Vtex → N8N)**
```json
{
  "cliente_id": "12345",
  "nome": "Carolina",
  "email": "carolina@email.com",
  "produtos": ["Tênis Running Pro", "Garrafa Térmica X"],
  "valor_total": 428,
  "tempo_abandono": "3h"
}
```

**Step 2: Enriquecimento (N8N → Salesforce)**
- Busca perfil no Salesforce Data Cloud
- Descobre: Carolina navegou 8min na categoria fitness, nunca comprou antes

**Step 3: Personalização (OpenAI via N8N)**
Prompt para GPT-4:
```
Cliente: Carolina (nova)
Carrinho: Tênis Running + Garrafa
Comportamento: Interesse em fitness
Gere email curto e amigável com CTA forte
```

**Mensagem gerada:**
```
Oi Carolina! 👟

Vi que você montou o kit perfeito para começar a correr!

Como é sua primeira compra, preparei algo especial:
🎁 15% OFF + Frete Grátis (válido por 24h)

Ah, e quem levou esse tênis também amou nossa 
mochila fitness - que tal dar uma olhada?

[FINALIZAR COMPRA] ← Desconto aplicado automaticamente
```

**Step 4: Cross-sell Inteligente (Flowise)**
- Flowise consulta padrão: "Quem comprou tênis + garrafa também levou..."
- Sugere: Mochila Fitness (R$120) com combo -10%

**Step 5: Envio Multi-canal (N8N)**
- **T+3h**: Email personalizado
- **T+6h** (se não abriu): SMS "Carolina, seu desconto expira em 18h"
- **T+12h** (se não converteu): Push notification no app
- **T+20h**: WhatsApp com áudio ElevenLabs (opcional)

**Resultados (baseado em 30 dias):**
- ✅ **28% de conversão** (vs 8% sem automação)
- ✅ **+R$67k recuperados** de 156 carrinhos
- ✅ **Cross-sell em 34%** dos casos (ticket médio +R$95)
- ✅ **ROI: 15:1** (cada R$1 em ferramentas retorna R$15)

**Dashboard Salesforce mostra:**
- Melhor horário de envio: 19h-21h (conv. 35%)
- Melhor incentivo: 15% OFF + frete (conv. 31%)
- SMS tem conv. 22% mas custo 3x maior que email

---

## **Roadmap de Implementação (45 dias)**

**Semana 1-2: Fundação**
- Configurar N8N (cloud ou self-hosted)
- Conectar APIs: Salesforce ↔ Vtex ↔ N8N
- Mapear campos e testar webhooks

**Semana 3-4: Piloto Carrinho**
- Criar workflow básico (apenas email)
- Ativar para 15% da base
- Medir baseline: taxa de recuperação

**Semana 5-6: Expansão**
- Adicionar SMS e WhatsApp
- Implementar cross-sell com Flowise
- Treinar modelo de churn no DataRobot

**Semana 7+: Escala e Otimização**
- Ativar para 100% da base
- A/B tests de mensagens
- Dashboards de performance

**Metas 90 dias:**
- 🎯 Taxa de retenção: +10pp
- 🎯 Frequência de compra: 4x/ano
- 🎯 Churn: -10pp
- 🎯 Receita canais próprios: 40% (vs 30% atual)

---

**Primeiros Passos:**
1. Agendar reunião com time de TI para liberar APIs
2. Criar conta N8N e OpenAI
3. Solicitar trial DataRobot (se budget permitir)
4. Escolher 1 caso de uso para começar (recomendo: carrinho abandonado)