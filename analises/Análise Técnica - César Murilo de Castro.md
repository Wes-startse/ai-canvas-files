# **Análise Técnica - AI Canvas**

**Projeto:** Prospecção / Cobrança Automatizada  
**Autor(a):** César Murilo de Castro (dados indicam Thamiris Sousa como autora)  
**E-mail:** thamiris.gsousa@gmail.com

---

## **Resumo do Problema**

**Contexto:** Alta inadimplência em empresas que vendem no crediário (materiais de construção, autopeças)

**Dados críticos:**
- 30% do faturamento com faturas >30 dias
- DSO (Days Sales Outstanding): 45 dias
- 25 horas/semana gastas em cobrança
- Custo: R$ 6,00 por fatura

**Ferramentas mencionadas:** Dialogflow, Google Contact Center AI, Rasa, Twilio, Vonage, OpenAI, Azure OpenAI

**Micro-problemas identificados:**

1. **Processo manual intensivo**: 25h/semana de ligações e cobranças repetitivas
2. **Alto custo operacional**: R$ 6/fatura é insustentável em volume
3. **Falta de priorização**: Não sabe quem tem maior chance de pagar
4. **Comunicação genérica**: Mesma abordagem para todos os inadimplentes
5. **Escalação tardia**: Cliente só vai para negociação após 60 dias

---

## **Sugestão de Implementação**

### **Solução 1: Cobrança Multicanal Automatizada**
**Ferramentas:** Make + Twilio + Flowise

**Arquitetura:**

**Trigger diário (Make - 8h da manhã):**
```javascript
// Consulta ERP/CRM
SELECT * FROM faturas
WHERE 
  data_vencimento < CURRENT_DATE
  AND status != 'PAGO'
  AND dias_atraso >= 1
ORDER BY valor DESC, dias_atraso DESC
```

**Flowise classifica inadimplentes:**
- Modelo: **GPT-4o-mini** (análise rápida e barata)
- **Componentes:**
  - **CSV Loader** com histórico de pagamentos
  - **Scoring Tool** (probabilidade de pagamento)
  - **Structured Output Parser** (JSON com estratégia)

**Classificação automática:**
```json
{
  "cliente_id": "12345",
  "nome": "João Silva Materiais",
  "valor": 15000,
  "dias_atraso": 35,
  "score_pagamento": 78,
  "categoria": "ALTA_PRIORIDADE",
  "canal_preferido": "WhatsApp",
  "melhor_horario": "14h-16h",
  "historico": "Sempre paga após 2º lembrete",
  "estrategia": "SMS_SEGUIDO_WHATSAPP"
}
```

**Sequência automatizada (Make + Twilio):**

**Dia 1 (1 dia de atraso):**
```
SMS amigável:
"Oi João! Notamos que a fatura #12345 (R$ 15.000)
venceu ontem. Tudo certo? Podemos ajudar?"

[PAGAR AGORA] [PRECISO DE AJUDA]
```

**Dia 3 (sem resposta):**
```
WhatsApp com boleto atualizado:
"João, segue boleto atualizado da fatura #12345.
Se preferir, podemos negociar um parcelamento."

[VER BOLETO] [PARCELAR]
```

**Dia 7 (ainda sem pagamento):**
```
Ligação automática (Twilio TTS):
"Olá, aqui é da [Empresa]. Estamos ligando sobre
a fatura #12345 no valor de R$ 15.000.
Venceu há 7 dias. Gostaria de negociar?"

Opções:
1 - Já paguei
2 - Quero parcelar
3 - Falar com atendente
```

**Dia 15 (escalação):**
```
Handover para humano:
Email automático para gerente de cobrança:
"Cliente João (#12345) não respondeu automação.
R$ 15k há 15 dias. Score: 78 (alto potencial).
Recomendação: Ligar pessoalmente."
```

---

### **Solução 2: Chatbot de Negociação com NLP**
**Ferramentas:** Flowise + Twilio WhatsApp API

**Chatbot inteligente (Flowise):**
- Modelo: **GPT-4o** (melhor para conversas complexas)
- **Componentes:**
  - **Conversational Chain** com memória
  - **Buffer Memory** (contexto da conversa)
  - **Custom Tool** para consultar opções de parcelamento
  - **Output Parser** para extrair intenções

**Exemplo de conversa:**

```
Cliente: "Tô com dificuldade esse mês"

Bot: "Entendo, João. Vamos achar uma solução juntos.
     Você conseguiria pagar R$ 3.000 agora e o 
     restante em 4x de R$ 3.000?"

Cliente: "4x não dá. Pode ser 6x?"

Bot: "Posso fazer 6x de R$ 2.000 com entrada 
     de R$ 3.000. Fecha?"

Cliente: "Fecha!"

Bot: "Ótimo! Vou gerar os boletos. 
     1ª parcela (R$ 3.000) vence em 5 dias.
     [VER BOLETOS]"

// Atualiza ERP automaticamente
// Envia boletos por email e WhatsApp
// Cria lembretes para próximas parcelas
```

**Detecção de intenções (NLP):**
- "Já paguei" → Verifica no sistema, atualiza status
- "Não recebi boleto" → Reenvia automaticamente
- "Quero parcelar" → Oferece opções baseadas em política
- "Empresa fechou" → Escala para recuperação judicial
- Intenção complexa → Transfere para humano

---

### **Solução 3: Modelo Preditivo de Inadimplência**
**Ferramentas:** DataRobot + Make

**Treinar modelo com histórico:**

**Dados de entrada (últimos 24 meses):**
```
Features:
- Valor da fatura
- Dias de atraso atual
- Histórico de atrasos (últimos 12 meses)
- Ticket médio do cliente
- Tempo de relacionamento
- Sazonalidade (mês do ano)
- Setor da empresa
- Localização geográfica

Target:
- Pagou após cobrança? (Sim/Não)
```

**DataRobot gera modelo preditivo:**
```
Acurácia: 84%
Features mais importantes:
1. Histórico de atrasos (35%)
2. Valor da fatura (22%)
3. Tempo de relacionamento (18%)
4. Ticket médio (12%)
5. Sazonalidade (8%)
```

**Aplicação prática (Make):**
```javascript
// Consulta DataRobot API para cada inadimplente
const score = await DataRobot.predict({
  valor: 15000,
  dias_atraso: 35,
  historico_atrasos: 2, // últimos 12 meses
  ticket_medio: 12000,
  tempo_cliente: 36 // meses
});

// score.probabilidade_pagamento = 78%

if (score.probabilidade_pagamento > 70) {
  estrategia = "COBRANCA_LEVE"; // Vai pagar, apenas lembrar
} else if (score.probabilidade_pagamento > 40) {
  estrategia = "NEGOCIACAO"; // Oferecer parcelamento
} else {
  estrategia = "JURIDICO"; // Baixa chance, protesto rápido
}
```

---

### **Solução 4: Dashboard de Cobrança Inteligente**
**Ferramentas:** Google Data Studio + BigQuery

**Métricas em tempo real:**

```
PAINEL DE COBRANÇA - 27/01/2025

💰 Faturamento em atraso: R$ 450.000 (30%)
📊 DSO atual: 45 dias (meta: 30 dias)

SEGMENTAÇÃO AUTOMÁTICA (por IA):

🟢 ALTA PROBABILIDADE (>70%): R$ 280k (62%)
   → 47 clientes
   → Estratégia: Lembrete amigável
   → Taxa recuperação estimada: 85%

🟡 MÉDIA PROBABILIDADE (40-70%): R$ 120k (27%)
   → 23 clientes
   → Estratégia: Negociação parcelamento
   → Taxa recuperação estimada: 60%

🔴 BAIXA PROBABILIDADE (<40%): R$ 50k (11%)
   → 8 clientes
   → Estratégia: Protesto/Jurídico
   → Taxa recuperação estimada: 20%

AÇÕES AUTOMÁTICAS HOJE:
✅ 68 SMSs enviados (R$ 0,10/cada)
✅ 34 WhatsApps enviados
✅ 12 ligações automáticas
✅ 5 negociações fechadas (R$ 65k recuperado)
⚠️ 3 casos escalados para humano

ECONOMIA:
Horas economizadas: 18h (vs processo manual)
Custo por fatura: R$ 0,80 (vs R$ 6,00 manual)
ROI: 7.5:1
```

---

### **Solução 5: Voz Automatizada com TTS/ASR**
**Ferramentas:** Twilio + ElevenLabs + Flowise

**Como funciona:**

**1. Ligação automática (Twilio):**
```
Discagem para cliente em atraso
```

**2. Mensagem TTS (ElevenLabs):**
```
Voz natural em português BR:
"Olá, aqui é da Materiais Silva. 
Estamos ligando sobre a fatura #12345 
no valor de R$ 15.000, vencida há 7 dias.
Você gostaria de regularizar?"
```

**3. ASR (Speech Recognition - Twilio):**
```
Cliente: "Sim, quero parcelar"

Sistema transcreve: "sim quero parcelar"
```

**4. Flowise processa resposta:**
```
NLP detecta intenção: PARCELAMENTO
Consulta opções disponíveis
Gera oferta personalizada
```

**5. TTS responde:**
```
"Ótimo! Posso fazer em 6 parcelas de R$ 2.500.
Você aceita?"

Cliente: "Aceito"

"Perfeito! Vou enviar os boletos por WhatsApp
e email. Obrigado!"
```

**6. Ações automáticas:**
```
✅ Gera parcelas no ERP
✅ Envia boletos
✅ Atualiza status
✅ Agenda lembretes
```

---

### **Pontos de Atenção**

**Compliance e Regulação:**
- **Procon/CDC:** Cobrança automatizada deve seguir Código de Defesa do Consumidor
- **Horários permitidos:** Ligações apenas 8h-20h em dias úteis
- **Frequência limitada:** Máximo 3 tentativas de contato/dia por canal
- **Opt-out:** Sempre permitir descadastro de mensagens automáticas
- **LGPD:** Dados de inadimplência são sensíveis, proteger adequadamente

**Técnico:**
- **Twilio custo:** SMS (R$ 0,10), WhatsApp (R$ 0,15), Voz (R$ 0,30/min)
- **Rate limits:** WhatsApp tem limite de mensagens/dia por número
- **Latência:** Ligações podem ter delay de 1-2s (aceitável)
- **Fallback:** Sempre ter humano disponível para casos complexos

**Operacional:**
- **Tom de voz:** Mensagens devem ser firmes mas respeitosas
- **Testes A/B:** Rodar variações para otimizar conversão
- **Treinamento:** Equipe precisa saber quando intervir
- **Monitoramento:** Revisar conversas do bot semanalmente

**Custos:**
- Make: R$ 50-150/mês
- Twilio: R$ 500-2k/mês (depende do volume)
- ElevenLabs: R$ 500-1k/mês
- OpenAI: R$ 200-500/mês
- DataRobot: Trial gratuito, depois negociar
- **Total:** R$ 1.5k-4k/mês
- **Economia:** 18h/semana × R$ 80/h × 4 = R$ 5.760/mês
- **ROI:** 2-4:1

---

## **Exemplo de Recomendação**

### **Caso: Recuperação de R$ 450k em 30 Dias**

**Situação inicial (1º de fevereiro):**
```
Total em atraso: R$ 450.000
Clientes inadimplentes: 78
Equipe: 2 pessoas (50h/semana cada)
Custo operacional: R$ 6/fatura
```

**Dia 1 - Deploy da automação**

**8h - Sistema analisa carteira:**
```
Flowise processa 78 clientes:
- 47 clientes (R$ 280k): Score >70% (vão pagar)
- 23 clientes (R$ 120k): Score 40-70% (negociação)
- 8 clientes (R$ 50k): Score <40% (jurídico)

Tempo de análise: 3 minutos (vs 5 horas manual)
```

**9h - Primeira onda de contatos (automática):**

**47 clientes alta probabilidade:**
```
SMS amigável:
"Bom dia! Notamos que sua fatura #XXX está pendente.
Podemos ajudar? [PAGAR] [PRECISO AJUDA]"

Custo: 47 × R$ 0,10 = R$ 4,70
```

**23 clientes média probabilidade:**
```
WhatsApp com oferta:
"Olá! Vimos que a fatura #XXX está atrasada.
Que tal parcelar em até 6x sem juros?"

Custo: 23 × R$ 0,15 = R$ 3,45
```

**8 clientes baixa probabilidade:**
```
Email formal + protesto (se >30 dias):
"Notificação de protesto em 5 dias úteis..."

Custo: R$ 0 (apenas email)
```

**Custo total: R$ 8,15 (vs R$ 468 manual)**

---

**Dia 1 - Tarde (14h-18h):**

**Respostas recebidas:**
```
18 clientes pagaram direto (R$ 112k)
12 clientes pediram parcelamento
5 clientes pediram para falar com humano
60 não responderam
```

**Chatbot negocia automaticamente:**
```
Cliente: "Posso parcelar?"
Bot: "Sim! Até 6x. Qual valor de entrada consegue?"
Cliente: "R$ 2.000"
Bot: "Perfeito! R$ 2.000 entrada + 6x R$ 2.166.
     Envio os boletos?"
Cliente: "Sim"

✅ 9 negociações fechadas automaticamente (R$ 65k)
⚠️ 3 casos complexos escalados para humano
```

**Equipe humana (4 horas):**
```
Gerente liga para 5 casos escalados
3 fecham parcelamento (R$ 28k)
2 prometem para próxima semana
```

---

**Dia 7 - Primeira semana:**

**Resultados:**
```
💰 Recuperado: R$ 205.000 (45.5% do total)
  - Pagamento integral: R$ 140.000 (31%)
  - Parcelamentos: R$ 65.000 (14%)

⏱️ Horas da equipe: 12h (vs 50h manual)
💵 Custo operacional: R$ 85 (vs R$ 468 manual)

📊 Taxa de resposta:
  - SMS: 42%
  - WhatsApp: 58%
  - Ligação automática: 31%
  - Chatbot parcelamento: 75% fechamento
```

**Segunda onda (clientes que não responderam):**
```
47 clientes sem resposta

Dia 8: WhatsApp mais direto
Dia 10: Ligação automática
Dia 12: Email formal
Dia 14: Escalação para humano
```

---

**Dia 30 - Resultado final:**

**Recuperação total:**
```
💰 R$ 346.500 (77% do total em atraso)

Breakdown:
- Pagamento integral: R$ 260k (58%)
- Parcelamentos fechados: R$ 86.5k (19%)
- Ainda em negociação: R$ 45k
- Perdas (jurídico): R$ 58.5k (13%)

Comparativo:

| Métrica | Manual | Com IA | Melhoria |
|---------|--------|--------|----------|
| Taxa recuperação | 62% | 77% | **+24%** |
| Tempo recuperação | 45 dias | 30 dias | **-33%** |
| Horas equipe | 200h | 48h | **-76%** |
| Custo total | R$ 2.340 | R$ 420 | **-82%** |
| Custo/fatura | R$ 6,00 | R$ 1,08 | **-82%** |

ROI mensal:
- Economia operacional: R$ 1.920
- Receita extra recuperada: R$ 67.500 (15% a mais)
- Investimento ferramentas: R$ 3.500
- **ROI: 19:1**
```

**Indicadores atingidos (vs metas):**

✅ Faturamento >60 dias: De 30% → 13% (**-57%**, meta -50%)  
✅ Taxa de recuperação: De 62% → 77% (**+24%**)  
✅ Horas cobrança: De 200h → 48h (**-76%**, meta -50%)  
✅ Agente IA: 45% automação (meta 30-50%)  
✅ Handover humano: 18% (meta <=25%)  
✅ Custo por real recuperado: De R$ 0,52 → R$ 0,12 (**-77%**, meta -50%)

---

## **Roadmap de Implementação (45 dias)**

**Semana 1-2: Setup Infraestrutura**
- Contratar Twilio (SMS + WhatsApp + Voz)
- Configurar Make com webhooks
- Conectar APIs do ERP/CRM
- Criar fluxos básicos de cobrança

**Semana 3-4: Inteligência e Chatbot**
- Configurar Flowise com modelo de scoring
- Treinar DataRobot com histórico de 24 meses
- Implementar chatbot de negociação
- Testar ligações automáticas (TTS/ASR)

**Semana 5-6: Piloto Controlado**
- Ativar para 20% da carteira
- Monitorar taxa de resposta
- Ajustar mensagens e thresholds
- Validar handover para humano

**Semana 7: Produção e Escala**
- Expandir para 100% dos inadimplentes
- Treinar equipe em novos processos
- Dashboard em tempo real
- Otimização contínua

**Metas 90 dias:**
- 🎯 DSO: De 45 → 30 dias (-33%)
- 🎯 Taxa recuperação: +30%
- 🎯 Horas cobrança: -50%
- 🎯 Custo/fatura: -82%
- 🎯 Faturamento >60d: -50%

---

**Primeiros Passos:**
1. Exportar histórico 24 meses (faturas + pagamentos)
2. Mapear jornada atual de cobrança (fluxograma)
3. Criar conta Twilio + testar SMS/WhatsApp
4. Definir políticas de parcelamento (até 6x? 12x?)
5. Configurar Make com primeiro fluxo simples (SMS 3 dias após vencimento)