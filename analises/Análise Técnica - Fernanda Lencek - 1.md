# **Análise Técnica - AI Canvas**

**Projeto:** Vendas Consultivas - Qualificação Inteligente de Leads  
**Autor(a):** Fernanda Lencek  
**E-mail:** fmlencek@gmail.com

---

## **Resumo do Problema**

**Contexto:** Vendas B2B complexas com ciclo longo (6-12 meses) e baixa conversão

**Dados críticos:**
- Executivos gastam 50% do tempo pesquisando contas
- Ciclo de vendas: 6-12 meses
- Taxa conversão (lead → closed): <20%
- Apenas 4-5 reuniões discovery/semana (baixo)

**Ferramentas mencionadas:** Salesforce/HubSpot, 6sense, Bombora, ML, LinkedIn, Reclame Aqui

**Micro-problemas identificados:**

1. **Leads não qualificados**: Executivos trabalham leads prematuros
2. **Falta de priorização**: Não sabe quem tem maior chance de comprar
3. **Pesquisa manual demorada**: 50% do tempo em research ao invés de vendas
4. **Sinais de compra ignorados**: Não identifica momento certo de abordar
5. **Baixa taxa de conversão discovery**: Apenas 50% viram proposta

---

## **Sugestão de Implementação**

### **Solução 1: Lead Scoring Preditivo com Sinais de Intenção**
**Ferramentas:** Flowise + Make + Bright Data MCP

**Coletar sinais de múltiplas fontes:**

**1. Dados financeiros (RI - Relações com Investidores):**
```javascript
// Bright Data scraping
websites_ri = [
  'ri.empresaX.com.br',
  'investidores.empresaY.com'
];

// Flowise analisa:
sinais_positivos = [
  "Anunciou investimento R$ 50M em transformação digital",
  "Contratação de novo CDO (Chief Digital Officer)",
  "Expansão para 3 novos estados"
];

score_financeiro = 85;
```

**2. Satisfação do cliente (Reclame Aqui, NPS):**
```javascript
// API Reclame Aqui
reclamacoes = {
  total: 450,
  respondidas: 320,
  indice_solucao: 71,
  nota_consumidor: 6.8
};

// Flowise analisa:
if (reclamacoes.total > 300 && nota_consumidor < 7.5) {
  problema_cx = "CRÍTICO";
  score_dor = 90; // Alta necessidade de solução CX
} else {
  score_dor = 45;
}
```

**3. Eventos de mercado (Recalls, processos):**
```javascript
// Scraping tribunais + Procon
alertas = [
  "Recall de 50k unidades - Janeiro/2025",
  "Processo coletivo atendimento - R$ 2M"
];

if (alertas.length > 0) {
  urgencia = "MUITO_ALTA";
  score_timing = 95;
}
```

**4. Sinais digitais (LinkedIn):**
```javascript
// Bright Data MCP scraping LinkedIn
posts_ceo = [
  "Precisamos melhorar experiência do cliente urgente",
  "Contratando time de CX para projeto grande"
];

if (posts_ceo.includes("urgente") || posts_ceo.includes("contratando")) {
  score_intencao = 88;
}
```

**Score consolidado (Flowise):**
```json
{
  "empresa": "Loja ABC S.A.",
  "score_total": 89,
  "categoria": "QUENTE",
  "sinais_positivos": [
    "Investimento R$ 50M digital (RI)",
    "Reclame Aqui: 450 reclamações (problema CX)",
    "CEO postou sobre CX no LinkedIn",
    "Recall recente (urgência)"
  ],
  "probabilidade_compra_90d": 73,
  "momento_ideal_abordar": "AGORA",
  "proximo_passo": "Pesquisar contato CDO + enviar case similar"
}
```

---

### **Solução 2: Pesquisa Automatizada de Contas**
**Ferramentas:** Flowise + Make + APIs Públicas

**Make compila dossiê automático:**

**Trigger:** Nova empresa entra no CRM como lead

**Workflow paralelo (5 minutos):**

**Thread 1: Dados corporativos**
```
ReceitaFederal API:
- CNPJ, razão social, porte
- Faturamento estimado
- Número de funcionários

LinkedIn Company:
- Decisores (CEO, CTO, CDO)
- Últimas contratações
- Posts e atualizações
```

**Thread 2: Sinais de dor**
```
Reclame Aqui API:
- Reclamações CX (quantidade, tipo)

Google News API:
- Menções em notícias (recalls, crises)

Tribunal Justiça (scraping):
- Processos relacionados a atendimento
```

**Thread 3: Contexto competitivo**
```
Similar Web API:
- Tráfego web
- Canais de aquisição

Bright Data:
- Tecnologias usadas (BuiltWith)
- Stack de CX atual
```

**Thread 4: Histórico interno**
```
Salesforce API:
- Interações anteriores
- Propostas enviadas
- Motivos de não fechamento (se aplicável)
```

**Flowise consolida em briefing:**
```markdown
# BRIEFING AUTOMÁTICO - Loja ABC S.A.

## DADOS BÁSICOS
- Faturamento: R$ 250M/ano
- Funcionários: 1.200
- Segmento: Varejo (Eletrodomésticos)
- Locais: 45 lojas (Sul/Sudeste)

## SINAIS DE COMPRA (Score: 89 - QUENTE)
✅ Investiu R$ 50M em digital (RI, Dez/2024)
✅ 450 reclamações Reclame Aqui (CX crítico)
✅ Recall 50k produtos (Jan/2025) - Crise reputacional
✅ CEO postou sobre CX no LinkedIn (há 3 dias)
✅ Contratando: CDO + Time CX (LinkedIn Jobs)

## DOR IDENTIFICADA
Problema: Experiência do cliente ruim + Crise reputacional
Urgência: MUITO ALTA (recall + pressão pública)
Budget provável: R$ 3-5M (baseado em investimento anunciado)

## DECISORES
- CEO: João Silva (LinkedIn ativo, 12k seguidores)
- CDO: Maria Santos (nova, contratada há 2 meses)
- CMO: Pedro Costa (15 anos na casa)

## HISTÓRICO
Última interação: 2022 (não avançou - "sem budget")
Contexto mudou: Nova liderança + investimento aprovado

## PRÓXIMOS PASSOS RECOMENDADOS
1. Conexão LinkedIn com CDO Maria
2. Compartilhar case similar: Varejo Eletro (recall → recuperação)
3. Mencionar CEO João em post sobre CX
4. Email personalizado em 48h
5. Propor reunião consultiva (não pitch de venda)

## ABORDAGEM SUGERIDA
"Maria, vi que você assumiu recentemente como CDO da ABC.
Trabalhamos com outras redes de varejo que passaram por
recalls e precisaram recuperar reputação rapidamente.
Que tal 30 min para eu compartilhar o que funcionou?"
```

---

### **Solução 3: Monitoramento de Sinais em Tempo Real**
**Ferramentas:** Make + Bright Data + Slack/Email

**Alertas automáticos para executivos:**

**Trigger 1: Movimentação no LinkedIn**
```
CEO da conta-alvo postou:
"Investindo pesado em CX este ano. 
Quem tem cases de sucesso?"

→ Slack para executivo:
"🔥 CONTA QUENTE - Loja ABC

CEO João Silva acabou de postar sobre CX!
Probabilidade fechar: 78%

Ação sugerida:
- Comentar no post (compartilhar case)
- Enviar DM em 24h
- Agendar call consultiva

[VER POST] [ABRIR CRM] [SUGERIR ABORDAGEM]"
```

**Trigger 2: Notícia negativa**
```
Google Alert detecta:
"Loja ABC enfrenta crise após recall"

→ Email para executivo:
"📰 OPORTUNIDADE - Loja ABC

Detectamos crise reputacional:
- Recall 50k unidades
- Imprensa negativa
- Pressão nas redes sociais

Esta é a janela perfeita para abordar!

Nossa solução de CX já ajudou 3 clientes
em situações similares.

Abordagem sugerida em anexo.
[INICIAR OUTREACH]"
```

**Trigger 3: Movimentação interna**
```
LinkedIn detecta:
Nova contratação: CDO na Loja ABC

→ CRM atualiza automático:
"🆕 MUDANÇA NO ORGANOGRAMA

Maria Santos assumiu como CDO.
Perfil dela indica foco em transformação digital.

Histórico Maria:
- Ex-Varejo XYZ (implantou CX em 18 meses)
- Certificação Salesforce
- Palestrante em eventos CX

Momento IDEAL para abordar!
Score subiu: 72 → 89"
```

---

### **Solução 4: Priorização Inteligente de Pipeline**
**Ferramentas:** DataRobot + Salesforce

**Treinar modelo com histórico:**
```
Features:
- Score de sinais de compra
- Tamanho da empresa
- Budget estimado
- Dores identificadas (críticas vs leves)
- Timing (urgência)
- Histórico de interações
- Stakeholders mapeados

Target: Fechou nos próximos 90 dias? (Sim/Não)
```

**Classificação automática no CRM:**
```
PIPELINE PRIORIZADO - João (Executivo)

🔥 FOCO IMEDIATO (prob >70%):
1. Loja ABC S.A. - 89% - R$ 4M - Abordar HOJE
2. Varejo XYZ - 78% - R$ 3.5M - Ligar amanhã
3. Rede DEF - 74% - R$ 2.8M - Email + call semana que vem

🟡 NURTURING (prob 40-70%):
4. Empresa GHI - 62% - R$ 5M - Ainda sem urgência
5. Grupo JKL - 58% - R$ 3M - Aguardar Q2
[+ 8 contas]

⚪ LONGO PRAZO (prob <40%):
14. Corp MNO - 32% - R$ 6M - Revisitar em 6 meses
[+ 23 contas]

RECOMENDAÇÃO IA:
Focar 100% nas top 3 esta semana.
Potencial: R$ 10.3M em 90 dias.
```

---

### **Solução 5: Personalização em Escala**
**Ferramentas:** Flowise + Gamma + Salesforce

**Para cada conta quente:**

**1. Case personalizado (GPT-4o):**
```
Prompt Flowise:
"Empresa: Loja ABC (varejo eletro, recall recente)
Gere case study de cliente similar que resolveu
problema de reputação com nossa solução CX"

Output:
"Case: Rede Eletro XYZ
Situação: Recall 80k produtos, crise reputacional
Solução: Plataforma CX em 90 dias
Resultado: NPS 45 → 78, vendas +25% em 6 meses"
```

**2. Apresentação visual (Gamma):**
```
Gamma gera automaticamente:
- Slide 1: "Por que estamos falando?" (contexto deles)
- Slide 2: Desafios similares (empresas do mesmo setor)
- Slide 3: Nossa solução aplicada ao contexto deles
- Slide 4: ROI estimado para Loja ABC
- Slide 5: Próximos passos
```

**3. Email hiperpersonalizado:**
```
"Maria,

Vi que você assumiu como CDO há 2 meses, bem no momento
do recall. Imagino que a pressão para melhorar CX é grande.

Trabalhamos com a Rede Eletro XYZ em situação similar:
- Recall de 80k produtos
- NPS em 45
- Crise nas redes sociais

Em 6 meses, eles viraram o jogo:
- NPS subiu para 78
- Vendas +25%
- Reputação recuperada

Preparei uma apresentação de 10 min sobre como fizemos.
Topa conversar quinta 15h?

Abs,
João"

[AGENDAR] [VER APRESENTAÇÃO]
```

---

### **Pontos de Atenção**

**Ética e Privacidade:**
- **Web scraping**: Respeitar robots.txt e termos de uso
- **LGPD**: Dados públicos ainda têm limites de uso
- **Transparência**: Não mentir sobre fontes de informação
- **Consentimento**: Opt-in para comunicações

**Qualidade de Dados:**
- **Reclame Aqui não é absoluto**: Empresas B2C têm mais reclamações
- **LinkedIn pode estar desatualizado**: Validar informações
- **RI nem sempre é público**: Empresas de capital fechado têm menos dados
- **Viés de seleção**: Grandes empresas têm mais sinais digitais

**Operacional:**
- **Manutenção de modelos**: Retreinar trimestralmente
- **Validação humana**: IA sugere, humano decide
- **Feedback loop**: Marcar quando IA acertou/errou
- **Falsos positivos**: Não abordar leads só porque score é alto

**Custos:**
- Bright Data: R$ 1-2k/mês (scraping em volume)
- 6sense/Bombora: $2-5k/mês (intent data)
- OpenAI: R$ 200-500/mês
- Make: R$ 150-300/mês
- DataRobot: Trial gratuito
- **Total:** R$ 5-12k/mês
- **ROI:** Fechar 1 deal extra/mês = R$ 200k+ (payback imediato)

---

## **Exemplo de Recomendação**

### **Caso: João Fecha R$ 4M em 45 Dias (vs 10 Meses Típico)**

**Segunda, 9h - Sistema detecta oportunidade:**

```
🔥 ALERTA CRÍTICO

Loja ABC S.A. - Score: 89 (QUENTE)

Sinais detectados nas últimas 48h:
1. CEO postou sobre CX no LinkedIn (ontem)
2. Abriram vaga CDO (há 5 dias)
3. Recall anunciado (há 7 dias)
4. Menção em notícia negativa (há 3 dias)

Probabilidade fechar 90 dias: 73%
Valor estimado: R$ 3-5M

→ João, FOQUE NESTA CONTA AGORA!
```

**9:15h - João abre briefing automático:**

```
Lê em 3 minutos:
- Contexto da empresa
- Dores identificadas
- Decisores
- Abordagem sugerida

João: "Perfeito! Vou comentar no post do CEO agora"
```

**9:30h - Primeira interação (LinkedIn):**

```
Post CEO: "Investindo pesado em CX este ano"

João comenta:
"João Silva, justo! Trabalhamos com Rede Eletro XYZ
que passou por recall similar ano passado.
Eles viraram o jogo em 6 meses. Se quiser trocar
uma ideia sobre o que funcionou, me chama no DM!"
```

**10h - CEO responde:**
```
"Interessante! Me manda seu contato."
```

**10:30h - João envia apresentação:**
```
Email com Gamma presentation:
"João, como prometido.

Caso Rede Eletro XYZ:
Situação similar à de vocês, resultado em 6 meses.

5 slides, 3 minutos de leitura.

Se fizer sentido, que tal 30 min amanhã 14h
para eu detalhar como adaptaríamos pra vocês?"
```

**Terça, 11h - CEO aceita:**
```
"Gostei! Mas quem precisa ver isso é nossa nova CDO.
Encaminhei pra ela. Maria Santos, se apresenta."
```

**Terça, 14h - Call com Maria (CDO):**

João usa briefing da IA:
```
- Fala dos desafios dela (recall, contratação recente)
- Mostra case específico do varejo
- Não faz pitch, faz consultoria
- Entende dores a fundo
```

Maria: "Faz muito sentido. Preciso apresentar pro board.
       Pode preparar uma proposta pra sexta?"

**Sexta, 9h - João envia proposta:**

Flowise gerou proposta personalizada:
```
- ROI estimado para Loja ABC
- Timeline 90 dias (urgência do recall)
- Precificação: R$ 4.2M
- Case studies similares
- Garantias e SLA
```

**Segunda seguinte - Feedback:**

Maria: "Board aprovou! Vamos fechar."

**45 dias depois - Contrato assinado:**

```
Valor: R$ 4.000.000
Ciclo de vendas: 45 dias (vs 10 meses típico)

Fatores de sucesso:
✅ Timing perfeito (urgência real)
✅ Abordagem consultiva (não vendeu, ajudou)
✅ Personalização extrema (não genérico)
✅ Velocidade (respondeu em horas, não dias)
✅ Decisores certos (CEO + CDO)
```

---

**Comparativo trimestre:**

**João ANTES da IA:**
```
| Métrica | Valor |
|---------|-------|
| Leads trabalhados | 80 |
| Reuniões discovery | 16 (4/semana) |
| Propostas enviadas | 8 (50% conversão) |
| Deals fechados | 1.5 (19% conversão) |
| Ticket médio | R$ 2.5M |
| Revenue | R$ 3.75M |
| Ciclo médio | 10 meses |
| Tempo pesquisa | 50% (20h/sem) |
```

**João DEPOIS da IA:**
```
| Métrica | Valor | Melhoria |
|---------|-------|----------|
| Leads trabalhados | 30 (só quentes) | **-62%** |
| Reuniões discovery | 32 (8/semana) | **+100%** |
| Propostas enviadas | 26 (81% conversão) | **+62%** |
| Deals fechados | 5.2 (20% → 35% conv) | **+75%** |
| Ticket médio | R$ 3.8M | **+52%** |
| **Revenue** | **R$ 19.76M** | **+427%** |
| Ciclo médio | 6 meses | **-40%** |
| Tempo pesquisa | 10% (4h/sem) | **-80%** |

ROI IA: 
Investimento: R$ 36k (3 meses)
Revenue extra: R$ 16M
ROI: 444:1
```

---

## **Roadmap de Implementação (60 dias)**

**Semana 1-2: Fontes de Dados**
- Contratar Bright Data para scraping
- Integrar APIs (Reclame Aqui, LinkedIn, RI)
- Mapear 100 contas para treino
- Configurar Make com workflows

**Semana 3-4: Scoring e Priorização**
- Configurar Flowise com modelo de scoring
- Treinar DataRobot com histórico de deals
- Criar alertas automáticos (Slack/Email)
- Validar com 10 contas reais

**Semana 5-6: Personalização**
- Templates de briefing automático
- Gamma para apresentações
- Integração com Salesforce
- Testes com 3 executivos

**Semana 7-8: Escala e Otimização**
- Rollout para todo time de vendas
- Monitorar conversão vs baseline
- Ajustar modelos com feedback
- Documentação e treinamento

**Metas 90 dias:**
- 🎯 Ciclo de vendas: De 10 → 6 meses (-40%)
- 🎯 Conv lead→proposta: De 50% → 80%
- 🎯 Reuniões/semana: De 4-5 → 8
- 🎯 Taxa conversão total: De <20% → 35%
- 🎯 Revenue/executivo: +300%

---

**Primeiros Passos:**
1. Mapear contas atuais com sinais de compra (manual)
2. Validar fontes de dados disponíveis (APIs, scraping)
3. Definir ICP (Ideal Customer Profile) detalhado
4. Exportar histórico 24 meses (deals ganhos/perdidos)
5. Pilotar com 1-2 executivos top performers