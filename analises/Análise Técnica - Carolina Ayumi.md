# **Análise Técnica - AI Canvas**

**Projeto:** AI Comunicações e Identificação de Churn / Validação de Crédito PF  
**Autor(a):** Carolina Ayumi  
**E-mail:** carol.nkb@gmail.com

---

## **Resumo do Problema**

**Contexto:** Crédito agrícola para Pessoa Física com validação de dados de mercado

**Ferramentas já mencionadas:** Plataformas de ML no-code (não especificadas)

**Micro-problemas identificados:**

1. **Volume grande de dados** no cadastro PF dificulta análise manual
2. **Validações complexas** necessárias: preço soja, clima, preço animal, etc.
3. **Inconsistências não detectadas**: Dados que não fazem sentido passam despercebidos
4. **Mesa de qualidade sobrecarregada**: Gargalo operacional
5. **Fraudes não identificadas**: Ausência de cruzamento inteligente de dados

---

## **Sugestão de Implementação**

### **Solução 1: Validação Inteligente com APIs de Mercado**
**Ferramentas:** Flowise + Make + APIs Públicas

**Arquitetura:**

**Dados do cliente PF (agricultor):**
```json
{
  "nome": "João da Silva",
  "cpf": "123.456.789-00",
  "propriedade": "150 hectares",
  "cultura": "Soja",
  "producao_declarada": "450 toneladas",
  "valor_solicitado": "R$ 2.500.000",
  "localizacao": "Sorriso-MT"
}
```

**Flowise valida automaticamente:**

**Agente 1: Validação de Produtividade (Make + APIs)**
```javascript
// Make busca dados de mercado
1. API IBGE/CONAB: Produtividade média soja em MT
   → Resultado: 58 sacas/hectare (média regional)

2. Cálculo esperado:
   150 ha × 58 sacas = 8.700 sacas = 522 ton

3. Cliente declarou: 450 ton

4. Flowise analisa:
   Diferença: -13.8% (dentro do aceitável)
   Score: ✅ CONSISTENTE
```

**Agente 2: Validação de Preço (API Commodities)**
```javascript
// Make consulta Bolsa de Chicago + B3
1. Preço atual soja: R$ 140/saca

2. Receita esperada:
   450 ton = 7.500 sacas × R$ 140 = R$ 1.050.000

3. Cliente pede: R$ 2.500.000

4. Flowise alerta:
   ⚠️ Valor solicitado 2.4x maior que receita esperada
   Score: ⚠️ ATENÇÃO - Justificar necessidade
```

**Agente 3: Validação Climática (API Meteorológica)**
```javascript
// Make consulta INPE + APIs climáticas
1. Histórico de chuvas em Sorriso-MT (últimos 90 dias)
   → Chuvas dentro da média, sem eventos extremos

2. Previsão safra (próximos 120 dias)
   → Normal, sem alertas de seca/geada

3. Flowise classifica:
   ✅ Condições climáticas favoráveis
   Score: ✅ BAIXO RISCO CLIMÁTICO
```

**Agente 4: Validação de Histórico (APIs Externas)**
```javascript
// Make consulta múltiplas bases
1. SCR Banco Central: R$ 800k em dívidas ativas
2. Serasa: Score 680 (bom)
3. Tribunais (Bright Data MCP scraping): 
   → 1 processo trabalhista (R$ 15k)

4. Flowise consolida:
   ⚠️ Exposição alta mas score ok
   Score: 🟡 RISCO MÉDIO
```

**Output consolidado para gerente:**
```json
{
  "cliente": "João da Silva",
  "score_final": 72,
  "classificacao": "MEDIO_RISCO",
  "decisao_sugerida": "APROVAR_COM_RESTRICAO",
  "valor_maximo": "R$ 1.800.000",
  "alertas": [
    "Valor solicitado acima da receita esperada",
    "Exposição total alta (SCR + novo crédito)"
  ],
  "validacoes": {
    "produtividade": "✅ CONSISTENTE",
    "preco_mercado": "⚠️ ATENÇÃO",
    "clima": "✅ FAVORÁVEL",
    "historico": "🟡 MÉDIO"
  },
  "tempo_analise": "4 minutos",
  "confianca": 87
}
```

---

### **Solução 2: Detecção de Fraude com ML**
**Ferramentas:** DataRobot + Flowise

**Como funciona:**

**1. Treinar modelo com histórico de fraudes:**
```
DataRobot recebe:
- 5.000 casos históricos
- 200 fraudes confirmadas (4%)
- Features: produtividade, valor, histórico, clima, etc.
```

**2. Modelo identifica padrões de fraude:**
```
Padrões detectados:
- Produtividade >30% acima da média regional
- Valor solicitado >3x receita esperada
- CPF com múltiplas solicitações (laranjas)
- Propriedade sem CAR (Cadastro Ambiental Rural)
- Histórico recente de inadimplência
```

**3. Scoring em tempo real:**
```json
{
  "probabilidade_fraude": 78,
  "classificacao": "ALTO_RISCO_FRAUDE",
  "motivos": [
    "Produtividade declarada 45% acima da média",
    "Propriedade sem CAR ativo",
    "CPF em 3 solicitações nos últimos 6 meses"
  ],
  "acao": "NEGAR_AUTOMATICAMENTE"
}
```

---

### **Solução 3: Enriquecimento Automático de Dados**
**Ferramentas:** Make + APIs Públicas + Web Scraping (Bright Data)

**Fluxo automatizado:**

**Cliente preenche apenas dados básicos:**
- CPF
- Propriedade (coordenadas GPS ou matrícula)
- Cultura principal

**Make enriquece automaticamente:**

**1. INCRA/CAR (via API):**
```
- Tamanho real da propriedade: 148 ha (vs 150 declarado)
- Status: Regular
- Restrições ambientais: Nenhuma
```

**2. Embrapa/IBGE:**
```
- Produtividade média local: 58 sacas/ha
- Histórico de safras: Últimos 3 anos regulares
```

**3. Cepea/B3:**
```
- Preço atual soja: R$ 142/saca
- Tendência: Estável (+2% últimos 30 dias)
```

**4. INPE:**
```
- Índice de vegetação (NDVI): 0.72 (saudável)
- Chuvas acumuladas: 380mm (adequado)
```

**5. Bright Data (web scraping):**
```
- Notícias sobre região: Sem alertas
- Cooperativas locais: Ativas e saudáveis
- Revendas de insumos: Operando normalmente
```

**Resultado:** Dossiê completo em 5 minutos (vs 2-3 horas manual)

---

### **Solução 4: Pré-Aprovado Instantâneo**
**Ferramentas:** Flowise + Motor de Regras

**Como funciona:**

**Cliente solicita crédito online:**
```
1. Preenche formulário (3 minutos)
2. Sistema valida dados em tempo real
3. Resposta imediata: Pré-aprovado ou análise manual
```

**Critérios de pré-aprovação automática:**
```javascript
if (
  score_fraude < 20 &&
  produtividade_consistente &&
  clima_favoravel &&
  scr_exposicao < 50% &&
  serasa_score > 600 &&
  valor_solicitado < 2x_receita_esperada
) {
  return "PRÉ-APROVADO_AUTOMATICAMENTE";
} else if (score_fraude > 70) {
  return "NEGADO_AUTOMATICAMENTE";
} else {
  return "ANÁLISE_MANUAL_NECESSÁRIA";
}
```

**Exemplo de resposta instantânea:**
```
✅ Parabéns, João!

Seu crédito foi PRÉ-APROVADO:
💰 Valor: até R$ 1.800.000
📅 Prazo: 24 meses
💳 Taxa: 8,5% a.a.

Próximos passos:
1. Assinar contrato digitalmente
2. Vistoria da propriedade (agendada)
3. Liberação: 5 dias úteis

[ACEITAR] [VER DETALHES]
```

---

### **Solução 5: Dashboard de Qualidade**
**Ferramentas:** Google Data Studio + BigQuery

**Métricas em tempo real:**

**Para mesa de qualidade:**
```
HOJE - 27/01/2025

📊 Solicitações processadas: 47
✅ Pré-aprovadas automaticamente: 28 (60%)
⚠️ Análise manual: 15 (32%)
❌ Negadas automaticamente: 4 (8%)

⏱️ Tempo médio de análise:
- Automática: 4 minutos
- Manual: 35 minutos (vs 90 min antes)

🎯 Taxa de inconsistências detectadas:
- IA: 94%
- Manual (antes): 67%

🚨 Fraudes evitadas: 2 (R$ 1.2M)
```

**Para gerentes:**
```
SEMANA 04/2025

Volume total: 312 solicitações
Aprovações: 187 (60%)
Ticket médio: R$ 1.850.000
Exposição total: R$ 346M

Top 3 motivos de negação:
1. Fraude detectada (15%)
2. Super-endividamento (12%)
3. Dados inconsistentes (8%)

Economia mesa de qualidade: 210 horas
```

---

### **Pontos de Atenção**

**Dados e APIs:**
- **APIs públicas podem ter latência:** IBGE/CONAB nem sempre respondem rápido
- **Dados desatualizados:** Preços de commodities mudam diariamente, cache inteligente
- **CAR/INCRA:** Nem todas propriedades estão regularizadas
- **LGPD:** Consentimento do cliente para consultar bases externas

**Validações:**
- **Exceções regionais:** MT tem produtividade diferente de RS, ajustar por estado
- **Culturas diversas:** Soja ≠ Milho ≠ Gado, criar regras específicas
- **Sazonalidade:** Preços variam conforme safra/entressafra
- **Força maior:** Seca severa, geada, pragas podem invalidar histórico

**Compliance:**
- **Bacen regulação:** Validar se automação está conforme normas
- **Direito de contestação:** Cliente pode questionar decisão automatizada (LGPD Art. 20)
- **Auditoria:** Todas decisões da IA precisam ser explicáveis e rastreáveis
- **Alçadas:** Definir limites para aprovação automática (ex: até R$ 500k)

**Custos:**
- APIs de mercado: R$ 500-2k/mês (B3, CONAB, INPE)
- Bright Data: R$ 500-1k/mês (scraping de notícias)
- DataRobot: Trial gratuito, depois negociar
- Make: R$ 50-150/mês
- BigQuery: R$ 200-500/mês
- **Total:** R$ 2k-5k/mês
- **ROI:** Eliminar mesa de qualidade economiza R$ 45k/mês (3 analistas)

---

## **Exemplo de Recomendação**

### **Caso: Validação Completa em 5 Minutos vs 2 Horas**

**Solicitação recebida (10h):**
```
Cliente: Maria Ferreira
CPF: 987.654.321-00
Propriedade: 200 ha
Cultura: Soja + Milho (rotação)
Produção declarada: 600 ton (soja) + 400 ton (milho)
Valor solicitado: R$ 3.200.000
Localização: Rio Verde-GO
```

**10:01h - Sistema inicia validação automática**

**Thread 1: Dados Cadastrais (INCRA/CAR)**
```
Make busca via API:
- Propriedade: 198 ha (vs 200 declarado) ✅
- CAR: Ativo e regular ✅
- Proprietária: Maria Ferreira (confirmado) ✅

Tempo: 30 segundos
```

**Thread 2: Produtividade Agrícola (IBGE/Embrapa)**
```
Flowise calcula:

SOJA:
- Média regional GO: 62 sacas/ha
- Esperado: 198 ha × 62 = 12.276 sacas = 737 ton
- Declarado: 600 ton
- Diferença: -18.5%
- Análise: Conservadora, aceitável ✅

MILHO:
- Média regional GO: 110 sacas/ha
- Esperado: 198 ha × 110 = 21.780 sacas = 1.307 ton
- Declarado: 400 ton
- Diferença: -69%
- Análise: ⚠️ MUITO ABAIXO - Verificar rotação

Tempo: 45 segundos
```

**Thread 3: Validação de Mercado (B3/Cepea)**
```
Make consulta preços:

SOJA: R$ 145/saca
Receita: 10.000 sacas × R$ 145 = R$ 1.450.000

MILHO: R$ 68/saca
Receita: 6.667 sacas × R$ 68 = R$ 453.000

RECEITA TOTAL ESTIMADA: R$ 1.903.000
SOLICITADO: R$ 3.200.000
RAZÃO: 1.68x

Flowise: ⚠️ Valor alto mas não crítico
(Pode ter outras fontes de renda)

Tempo: 30 segundos
```

**Thread 4: Clima e Safra (INPE)**
```
API INPE retorna:
- NDVI atual: 0.68 (vegetação saudável)
- Chuvas 90 dias: 425mm (acima da média +8%)
- Previsão: Sem alertas de seca/geada
- Histórico: Últimas 3 safras regulares

Flowise: ✅ Condições excelentes

Tempo: 40 segundos
```

**Thread 5: Histórico Financeiro (Paralelo)**
```
SCR Banco Central: R$ 1.200.000 em dívidas ativas
Serasa: Score 720 (muito bom)
Tribunais: 0 processos

Bright Data scraping:
- Notícias: Cooperativa local reporta boa safra
- Sem alertas negativos sobre região

Flowise: ✅ Histórico sólido

Tempo: 1 minuto
```

**Thread 6: Detecção de Fraude (DataRobot)**
```
Modelo analisa:
- Produtividade: Dentro da média (conservadora)
- CPF: 1 solicitação (normal)
- Propriedade: Regular no CAR
- Histórico: Positivo
- Padrão: Similar a clientes bons pagadores

Score de fraude: 8% (MUITO BAIXO)

Tempo: 30 segundos
```

**10:05h - Consolidação e Decisão (Flowise)**

**Claude 3.5 Sonnet analisa:**
```
Cliente: Maria Ferreira é perfil sólido.

Pontos positivos:
✅ Propriedade regular
✅ Produtividade conservadora (realista)
✅ Score Serasa excelente (720)
✅ Condições climáticas favoráveis
✅ Histórico limpo
✅ Fraude improvável (8%)

Pontos de atenção:
⚠️ Milho muito abaixo do esperado
   → Verificar: Rotação? Consórcio? Outra cultura?
⚠️ Valor solicitado 1.68x receita agrícola
   → Verificar: Tem outras fontes? Pecuária?

Recomendação:
🟡 ANÁLISE MANUAL COM PRIORIDADE

Sugestão de aprovação:
- Valor: até R$ 2.400.000 (vs R$ 3.2M solicitado)
- Condição: Vistoria confirmar rotação
- Prazo: 24 meses com carência de 6 meses
```

**10:06h - Mesa de Qualidade recebe:**

```
📋 MARIA FERREIRA - Análise Manual Necessária

Score geral: 78/100 (BOM)
Prioridade: ALTA (cliente sólido)

Pré-análise da IA:
- Produtividade ok
- Histórico excelente
- Clima favorável
- ⚠️ Verificar: Rotação milho + outras rendas

Documentos anexos:
✅ CAR, INCRA, SCR, Serasa
✅ Análise climática INPE
✅ Comparativo produtividade regional

Tempo economizado: 1h45min
Validações já feitas: 90%

Ação sugerida:
1. Ligar para Maria (5 min)
2. Confirmar rotação e outras rendas
3. Aprovar R$ 2.4M ou ajustar
```

**10:15h - Analista liga para Maria:**

```
Analista: "Maria, vi sua solicitação. Explica pra mim 
           a rotação do milho? Tá baixa."

Maria: "Ah sim! Faço rotação, só planto milho em 100ha.
        Os outros 98ha fico com soja direto. E tenho
        50 cabeças de gado que não declarei."

Analista: "Perfeito! Vou aprovar R$ 2.8M então.
           Tudo certo?"

Maria: "Ótimo!"
```

**10:20h - Aprovação final:**

```
✅ CRÉDITO APROVADO

Cliente: Maria Ferreira
Valor: R$ 2.800.000
Prazo: 24 meses (6 carência)
Taxa: 8,2% a.a.

Próximos passos:
1. Vistoria agendada: 30/01
2. Assinatura digital contrato
3. Liberação: 05/02
```

**Comparativo:**

| Etapa | Processo Manual | Com IA | Ganho |
|-------|-----------------|--------|-------|
| Coleta de dados | 30 min | 1 min | **97% mais rápido** |
| Validação produtividade | 20 min | 30 seg | **97% mais rápido** |
| Validação mercado | 15 min | 30 seg | **97% mais rápido** |
| Consulta histórico | 25 min | 1 min | **96% mais rápido** |
| Análise consolidada | 30 min | 2 min | **93% mais rápido** |
| **TOTAL** | **2 horas** | **5 minutos** | **96% mais rápido** |

**Resultado mensal (500 solicitações):**

| Métrica | Antes | Com IA | Melhoria |
|---------|-------|--------|----------|
| Tempo total mesa | 1.000h | 150h | **-85%** |
| Taxa de inconsistências | 67% | 94% | **+40%** |
| Fraudes detectadas | 60% | 95% | **+58%** |
| Pré-aprovados instant. | 0% | 60% | **+infinito** |
| Satisfação cliente | 6/10 | 9/10 | **+50%** |

**ROI:**
- Economia mesa: R$ 45k/mês (redução de 3 → 1 analista)
- Fraudes evitadas: R$ 180k/mês (5 casos × R$ 36k médio)
- Investimento: R$ 5k/mês (ferramentas)
- **ROI: 45:1**

---

## **Roadmap de Implementação (45 dias)**

**Semana 1-2: Setup de APIs**
- Contratar APIs: IBGE, CONAB, B3, INPE, SCR
- Configurar Make com integrações
- Testar latência e confiabilidade

**Semana 3-4: Treinamento do Modelo**
- Exportar 5.000 casos históricos
- Treinar modelo de fraude no DataRobot
- Validar acurácia com casos conhecidos

**Semana 5-6: Flowise e Automação**
- Configurar agentes de validação
- Implementar lógica de decisão
- Criar dashboard no Data Studio

**Semana 7: Piloto e Ajustes**
- Rodar 50 casos em paralelo (manual + IA)
- Comparar resultados
- Ajustar thresholds e regras

**Metas 90 dias:**
- 🎯 60% de pré-aprovados automáticos
- 🎯 Redução de 85% no tempo de análise
- 🎯 Taxa de detecção de fraude: 95%
- 🎯 Satisfação do cliente: 9/10
- 🎯 Economia: R$ 135k (3 meses)

---

**Primeiros Passos:**
1. Mapear APIs disponíveis (públicas e pagas)
2. Exportar histórico de 24 meses (casos + fraudes)
3. Validar regulamentação Bacen para automação
4. Contratar Bright Data para scraping
5. Agendar POC com 100 casos históricos