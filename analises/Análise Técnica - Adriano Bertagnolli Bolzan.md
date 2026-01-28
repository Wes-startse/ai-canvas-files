# **Análise Técnica - AI Canvas**

**Projeto:** Automação Análise de Crédito  
**Autor(a):** Adriano Bertagnolli Bolzan  
**E-mail:** adrianobbolzan@gmail.com

---

## **Resumo do Problema**

**Ferramentas já utilizadas:** CRM, Denodo (virtualização de dados), SCR (Sistema de Informações de Crédito)

**Micro-problemas identificados:**

1. **Inadimplência crescente**: Aumento de calotes devido ao cenário econômico
2. **Análise manual lenta**: Analistas gastam muito tempo em verificações repetitivas
3. **Falta de dados consolidados**: Informações dispersas (Serasa, SCR, Tribunais, mídias negativas)
4. **Custo operacional alto**: Necessidade de mais analistas para demanda crescente
5. **Decisões inconsistentes**: Subjetividade na concessão de crédito entre analistas

---

## **Sugestão de Implementação**

### **Solução 1: Motor de Rating com IA**
**Ferramentas:** Make/N8N + Flowise + APIs públicas

**Arquitetura:**
1. **Make coleta dados de múltiplas fontes:**
   - Serasa (via API)
   - SCR do Banco Central
   - Tribunais de Justiça (web scraping com Bright Data MCP)
   - Mídias negativas (Google News API)

2. **Flowise processa e calcula score:**
   - Modelo: **Claude 3.5 Sonnet** (melhor para análise de dados estruturados + texto)
   - **Componentes Flowise:**
     - Multiple Document Loaders (para diferentes fontes)
     - **Custom Tool** para consultar APIs financeiras
     - **Structured Output Parser** (retorna JSON com score + justificativa)
     - **Bright Data MCP** para scraping de tribunais

3. **Denodo unifica dados** para dashboard

**Output esperado:**
```json
{
  "score_credito": 725,
  "classificacao": "Baixo Risco",
  "recomendacao": "Aprovar até R$ 50.000",
  "alertas": ["Processo judicial ativo - valor R$ 12k"],
  "confianca": 89
}
```

---

### **Solução 2: Agente de Análise Automática**
**Ferramentas:** Flowise + Make + CRM existente

**Como funciona:**
1. Solicitação de crédito entra no CRM
2. Make aciona Flowise automaticamente
3. **Agente Flowise executa checklist:**
   - Consulta Serasa (score e restrições)
   - Verifica SCR (dívidas em outras instituições)
   - Busca processos judiciais
   - Analisa histórico no CRM (pagamentos anteriores)

4. **Decision Tree no Flowise:**
   - Score > 700 + Sem restrições = **Aprovação automática**
   - Score 500-700 = **Análise humana necessária**
   - Score < 500 = **Rejeição automática com justificativa**

5. Decisão volta ao CRM e notifica gerente

**Modelos Flowise recomendados:**
- **GPT-4o** para análise principal (raciocínio complexo)
- **GPT-4o-mini** para classificação simples (custo menor)

---

### **Solução 3: Automação de Verificações com RPA Light**
**Ferramentas:** Make + Integrações nativas

**Fluxo Make:**
1. **Webhook**: Novo cliente solicita crédito
2. **HTTP Requests paralelos:**
   - Serasa API (score)
   - SCR API (exposição total)
   - API Tribunais (processos)
3. **Aggregate Node**: Consolida respostas
4. **OpenAI Node**: Analisa dados e gera relatório
5. **Router**: 
   - Se aprovado → Atualiza CRM + notifica gerente
   - Se negado → Email automático ao cliente com motivo
   - Se dúvida → Cria task para analista revisar

**Vantagem:** Usa integrações prontas (sem código)

---

### **Solução 4: Dashboard Preditivo com DataRobot**

**Implementação:**
1. Exportar histórico de crédito (aprovados + inadimplentes) do CRM
2. Upload no DataRobot com features:
   - Score Serasa
   - Valor solicitado / Renda
   - Tempo de relacionamento
   - Histórico de pagamento
   - Processos judiciais

3. DataRobot treina modelo preditivo de inadimplência
4. Gera API de predição em tempo real
5. Integra com CRM via Make

**Decisão automatizada:**
- Probabilidade inadimplência < 10% → Aprovação automática
- 10-30% → Aprovação com limite reduzido
- > 30% → Rejeição ou análise manual

---

### **Pontos de Atenção**

**Integrações:**
- Serasa e SCR têm APIs oficiais, mas podem ter custo por consulta
- Tribunais geralmente não têm API - usar scraping responsável (Bright Data)
- Denodo já unifica dados, aproveitar essa infraestrutura
- Validar SLA das APIs (algumas demoram 30s+ por consulta)

**Dados:**
- Precisa de mínimo 1000 casos históricos para treinar no DataRobot
- Equilibrar dataset (50% adimplentes / 50% inadimplentes)
- Anonimizar dados sensíveis em ambientes de teste (LGPD)
- Atualizar modelos trimestralmente (comportamento muda com economia)

**Compliance:**
- Seguir regulamentação do Banco Central sobre crédito
- Documentar critérios de decisão para auditorias
- Permitir contestação de decisões automatizadas (LGPD Art. 20)
- Ter humano na validação final para valores altos (>R$100k)

**Custos:**
- Make: $9-29/mês (até 10k operações)
- Serasa API: ~R$2-5 por consulta
- OpenAI: ~R$50-200/mês
- DataRobot: Trial gratuito, depois negociar licença
- Bright Data: $500-1000/mês para scraping em escala

---

## **Exemplo de Recomendação**

### **Caso: Empresa XYZ Ltda - Análise Automática**

**Solicitação:**  
Empresa XYZ solicita crédito de R$ 80.000 para capital de giro. CNPJ: 12.345.678/0001-90

**Fluxo Automatizado (4 minutos total):**

**T+0min: Disparo automático (Make)**
```
Webhook recebe solicitação do CRM
→ Inicia 4 buscas paralelas
```

**T+1min: Coleta de dados**

1. **Serasa API:**
```json
{
  "score": 680,
  "restricoes": 0,
  "protestos": 0
}
```

2. **SCR Banco Central:**
```json
{
  "dividas_total": "R$ 45.000",
  "maior_atraso": "15 dias",
  "instituicoes": 2
}
```

3. **Tribunais (via Bright Data):**
```
Processos encontrados: 1
Tipo: Trabalhista
Valor: R$ 8.500
Status: Em andamento
```

4. **CRM histórico:**
```json
{
  "cliente_desde": "2022",
  "pagamentos_pontualidade": "95%",
  "ticket_medio": "R$ 25k"
}
```

**T+2min: Análise IA (Flowise)**

Prompt para Claude 3.5 Sonnet:
```
Analise crédito:
- Score: 680 (bom)
- Dívidas: R$ 45k em 2 bancos
- 1 processo trabalhista R$ 8.5k
- Cliente há 2 anos, 95% pontual
- Solicita: R$ 80k

Retorne JSON com decisão
```

**Output Flowise:**
```json
{
  "decisao": "APROVADO_COM_RESTRICAO",
  "valor_maximo": 60000,
  "taxa_sugerida": 2.8,
  "prazo_maximo": "18 meses",
  "justificativa": "Score adequado e histórico positivo, mas exposição 
                    total ficaria alta (R$ 105k). Reduzir para R$ 60k 
                    mantém saúde financeira.",
  "alertas": [
    "Processo trabalhista ativo",
    "Limite conservador devido exposição"
  ],
  "confianca": 87
}
```

**T+3min: Enriquecimento DataRobot (opcional)**
- Consulta modelo preditivo
- Probabilidade inadimplência: 12%
- Confirma decisão de aprovar com restrição

**T+4min: Ação automática**
- ✅ Atualiza CRM com decisão
- ✅ Notifica gerente por email
- ✅ Gera minuta de contrato pré-preenchida
- ✅ Agenda call com cliente para formalização

**Comparativo:**

| Métrica | Antes (Manual) | Depois (IA) |
|---------|----------------|-------------|
| Tempo análise | 45-90 min | 4 min |
| Custo por análise | R$ 85 (hora analista) | R$ 3 (APIs) |
| Consistência | 68% | 94% |
| Inadimplência | 8.5% | 4.2% |

**Resultado mensal (1000 análises):**
- ⏱️ **750 horas economizadas** (analistas focam em casos complexos)
- 💰 **R$ 82k salvos** em custo operacional
- 📉 **Inadimplência reduzida em 50%** (R$ 340k menos em perdas)
- 🎯 **ROI: 28:1** em 6 meses

---

## **Roadmap de Implementação (60 dias)**

**Semana 1-2: Setup**
- Mapear APIs disponíveis (Serasa, SCR, Tribunais)
- Configurar Make e conectar ao CRM
- Criar conta Flowise e testar integrações

**Semana 3-4: Piloto Básico**
- Implementar fluxo apenas Serasa + SCR
- Testar com 50 casos reais históricos
- Ajustar thresholds de aprovação

**Semana 5-6: Expansão Completa**
- Adicionar scraping tribunais (Bright Data)
- Implementar agente Flowise com decisão inteligente
- Treinar modelo DataRobot com histórico

**Semana 7-8: Produção**
- Ativar para 100% dos casos < R$ 50k
- Casos >R$ 50k passam por validação humana
- Monitorar inadimplência semanalmente

**Metas 90 dias:**
- 🎯 80% de análises totalmente automatizadas
- 🎯 Tempo médio: <5 minutos
- 🎯 Inadimplência: -30%
- 🎯 Reduzir 40% da equipe de analistas (realocação)

---

**Primeiros Passos:**
1. Solicitar acesso APIs: Serasa, SCR
2. Criar conta Make + Flowise
3. Exportar 1000 casos históricos do CRM
4. Agendar workshop com analistas (entender critérios atuais)