# **Análise Técnica - AI Canvas**

**Projeto:** Vendas Consultivas - Account Planning Automatizado  
**Autor(a):** Fernanda Lencek  
**E-mail:** fmlencek@gmail.com

---

## **Resumo do Problema**

**Contexto:** Complemento do canvas anterior - foco em mapear stakeholders e criar account plans

**Dados críticos:**
- 50% do tempo gasto mapeando decisores
- 10 dias para criar um account plan
- Plans incompletos e com gaps
- Conv lead→venda: <20%
- Conv proposta→negociação: <40%

**Ferramentas mencionadas:** LinkedIn, 6sense, Salesforce, ZoomInfo

**Micro-problemas identificados:**

1. **Account plan manual e lento**: 10 dias por conta
2. **Mapeamento incompleto**: Faltam dados críticos de decisores
3. **Tempo desperdiçado**: 50% em research ao invés de vender
4. **Baixa conversão**: Plans ruins resultam em deals perdidos
5. **Escalabilidade limitada**: Poucos accounts trabalhados simultaneamente

---

## **Sugestão de Implementação**

### **Solução 1: Agente de Account Plan Automatizado**
**Ferramentas:** Flowise + Make + Bright Data MCP + ZoomInfo

**Framework de Account Plan (template):**
```
1. VISÃO GERAL DA EMPRESA
- Faturamento, funcionários, locais
- Estrutura organizacional
- Iniciativas estratégicas recentes

2. MAPA DE STAKEHOLDERS
- Decisores (C-level)
- Influenciadores (Diretores, Gerentes)
- Campeões internos potenciais
- Bloqueadores conhecidos

3. ANÁLISE DE PODER E INFLUÊNCIA
- Quem tem budget?
- Quem sofre a dor?
- Quem decide?

4. PROCESSO DE COMPRA
- Como compram (RFP, consultiva, etc)?
- Tempo médio de decisão
- Comitês envolvidos

5. SITUAÇÃO ATUAL
- Solução atual em uso
- Dores identificadas
- Budget estimado

6. ESTRATÉGIA DE ABORDAGEM
- Ponto de entrada ideal
- Mensagem personalizada
- Próximos passos
```

**Flowise gera em 1 dia (vs 10 dias manual):**

**Step 1: Coleta de dados (Make - 2 horas):**
```javascript
// Paralelo
await Promise.all([
  ZoomInfo.getCompanyProfile(cnpj),
  LinkedIn.searchEmployees(empresa, cargos_chave),
  Bright_Data.scrapeWebsite(url_empresa),
  Bright_Data.scrapeRI(url_ri),
  Salesforce.getHistorico(empresa)
]);
```

**Step 2: Análise com IA (Flowise - 30 min):**
```
Modelo: Claude 3.5 Sonnet (melhor para análise complexa)

Componentes:
- Multiple Data Loaders (ZoomInfo, LinkedIn, RI)
- Org Chart Builder (constrói organograma)
- Stakeholder Analysis Tool
- Power Mapping Algorithm
- Structured Output Parser (JSON account plan)
```

**Step 3: Validação humana (executivo - 2 horas):**
```
Executivo revisa e enriquece com:
- Conversas anteriores
- Insights de networking
- Informações confidenciais
```

**Output final:**
```markdown
# ACCOUNT PLAN - Empresa XYZ S.A.

## RESUMO EXECUTIVO
Oportunidade: Alto potencial (Score: 87)
Valor estimado: R$ 3-5M
Probabilidade: 68%
Timeline: 4-6 meses

## MAPA DE STAKEHOLDERS

### DECISORES (C-Level)
**CEO: João Silva**
- LinkedIn: 15k seguidores, ativo
- Prioridades: Transformação digital, redução custos
- Abordagem: Via indicação do CFO (ex-colega do nosso CMO)

**CFO: Maria Santos** ⭐ CAMPEÃ POTENCIAL
- Novo na empresa (3 meses)
- Ex-Cliente ABC (referência nossa)
- Abordagem: Email direto mencionando case ABC

**CTO: Pedro Costa** ⚠️ BLOQUEADOR
- Defendeu solução atual em reunião board (RI Dez/2024)
- Precisa ser neutralizado com dados
- Abordagem: Demonstração técnica comparativa

### INFLUENCIADORES
**Dir. Operações: Ana Lima**
- Sofre a dor diretamente
- 8 reclamações públicas no LinkedIn
- Abordagem: Consultoria gratuita

### ORGANOGRAMA
CEO (João)
├─ CFO (Maria) ⭐
│  └─ Dir Finanças
├─ CTO (Pedro) ⚠️
│  ├─ Dir TI (Carlos)
│  └─ Dir Projetos
└─ COO
   └─ Dir Ops (Ana) 💡

## PROCESSO DE COMPRA
Tipo: RFP formal (histórico 2022)
Comitê: CEO + CFO + CTO + Dir TI
Timeline típico: 5-7 meses
Aprovação: Board trimestral

## SITUAÇÃO ATUAL
Solução: Concorrente Legacy XYZ
Contrato: Expira em 6 meses
Dores:
- Alto custo manutenção (R$ 800k/ano)
- Sistema ultrapassado
- Integrações problemáticas

## FORÇAS DA CONTA
✅ CFO é ex-cliente (campeã)
✅ Dor clara e quantificável
✅ Budget aprovado (RI)
✅ Timing perfeito (renovação)

## FRAQUEZAS
❌ CTO resistente (defender escolha anterior)
❌ RFP formal (competitivo)
❌ Relação longa com concorrente (8 anos)

## ESTRATÉGIA VENCEDORA

### FASE 1: CONSTRUIR BASE (Mês 1-2)
1. Ativar relacionamento CFO Maria
   - Email personalizado: "Vi que assumiu na XYZ..."
   - Case estudo ABC detalhado
   - Café informal (não venda)

2. Conquistar Dir Ops Ana
   - Consultoria diagnóstico gratuita
   - Quick wins de 30 dias
   - Transformar em advogada interna

### FASE 2: NEUTRALIZAR CTO (Mês 2-3)
1. Demonstração técnica superior
   - POC comparativa (nosso vs atual)
   - Dados de performance
   - Depoimento cliente tech similar

2. Aliança com Dir TI Carlos
   - Facilitar vida dele
   - Mostrar integrações simples
   - Redução 50% em tickets

### FASE 3: FECHAR C-LEVEL (Mês 3-4)
1. Apresentação board (CEO João)
   - ROI claro: Economia R$ 400k/ano
   - Redução risco (case disaster recovery)
   - Timeline implementação curta

2. CFO Maria apresenta internamente
   - Ela vende por nós
   - Usa case ABC como prova

### FASE 4: GANHAR RFP (Mês 4-5)
1. Influenciar requisitos
   - Via Ana e Carlos
   - Incluir diferenciais nossos

2. Proposta irresistível
   - Preço competitivo
   - Garantias fortes
   - Migração sem risco

## PRÓXIMOS PASSOS (esta semana)
☐ Segunda: Email para CFO Maria
☐ Terça: Conexão LinkedIn Dir Ops Ana
☐ Quarta: Preparar case ABC atualizado
☐ Quinta: Ligar para validar timing renovação
☐ Sexta: Propor call introdutória
```

---

### **Solução 2: Mapa de Stakeholders Dinâmico**
**Ferramentas:** LinkedIn Sales Navigator + Flowise + Salesforce

**Atualização contínua:**
```javascript
// Make monitora mudanças diárias
stakeholders.forEach(async (pessoa) => {
  const mudancas = await LinkedIn.checkUpdates(pessoa);
  
  if (mudancas.novo_cargo) {
    alert("⚠️ João Silva mudou de CEO para Chairman!");
  }
  if (mudancas.novo_post && mudancas.post.includes("nossa_area")) {
    alert("🔥 João postou sobre transformação digital!");
  }
  if (mudancas.conexao_nova && mudancas.conexao == "nosso_contato") {
    alert("💡 João se conectou com nosso advogado!");
  }
});
```

---

### **Solução 3: Power Mapping com IA**
**Ferramentas:** Flowise + Orgchart Visualization

**Análise de influência:**
```javascript
Flowise.analyzePower({
  stakeholders: [...],
  criterios: [
    "budget_control",
    "decision_authority",
    "pain_level",
    "relationship_with_us"
  ]
});

Output:
Maria (CFO): 
- Poder: 9/10 (controla budget)
- Influência: 8/10 (nova, quer resultados)
- Dor: 7/10 (custo alto atual)
- Relacionamento: 9/10 (ex-cliente)
→ CAMPEÃ IDEAL (score 8.25)

Pedro (CTO):
- Poder: 7/10
- Influência: 8/10 (técnico respeitado)
- Dor: 3/10 (ele defendeu solução atual)
- Relacionamento: 2/10 (nunca interagiu)
→ BLOQUEADOR (score 5.0)
```

---

### **Pontos de Atenção**

**Dados:**
- **ZoomInfo custo:** $15-30k/ano (considerar ROI)
- **LinkedIn Sales Navigator:** $80-100/usuário/mês
- **Dados podem estar desatualizados:** Sempre validar
- **LGPD:** Dados públicos, mas transparência no uso

**Operacional:**
- **Validação humana essencial:** IA erra em nuances
- **Atualização frequente:** Accounts mudam (pessoas saem)
- **Customização por vertical:** B2B tech ≠ Varejo ≠ Indústria
- **Integração com sales process:** Account plan deve guiar ações

**Custos:**
- ZoomInfo: $2k-5k/mês
- LinkedIn Sales Nav: $80-100/executivo/mês
- 6sense: $2-5k/mês (opcional)
- OpenAI: $200-500/mês
- Make: $150-300/mês
- **Total:** ~$5-12k/mês
- **ROI:** 1 deal extra fechado = $200k+ (payback imediato)

---

## **Exemplo de Recomendação**

### **Caso: Account Plan em 1 Dia vs 10 Dias**

**Segunda, 9h - Executivo recebe lead:**
```
Nova oportunidade: Empresa XYZ
Valor potencial: R$ 4M
```

**9:05h - Dispara automação (Make):**
```
✅ ZoomInfo: Dados empresa coletados
✅ LinkedIn: 12 stakeholders mapeados
✅ Bright Data: Website + RI scrapados
✅ Salesforce: Histórico compilado
```

**11h - Flowise gera draft account plan:**
```
Account Plan - 80% completo
Tempo: 2 horas (vs 10 dias)

Stakeholders mapeados: 12
Decisores identificados: 3
Campeão potencial: 1 (CFO ex-cliente)
Bloqueador: 1 (CTO)
```

**14h-16h - Executivo enriquece:**
```
Adiciona:
- Conversa informal com CFO (ex-colega)
- Intel competitiva de parceiro
- Detalhes técnicos específicos
```

**17h - Account plan final pronto:**
```
✅ Completo em 1 dia (vs 10)
✅ 90% precisão validada
✅ Estratégia clara definida
✅ Próximos passos priorizados
```

**Terça, 9h - Executivo já está executando:**
```
Email para CFO enviado
LinkedIn Dir Ops conectado
Case study preparado
Call agendada sexta
```

---

**Comparativo trimestre:**

| Métrica | Antes | Depois | Melhoria |
|---------|-------|--------|----------|
| Tempo/account plan | 10 dias | 1 dia | **-90%** |
| Accounts trabalhados | 9/trimestre | 60/trimestre | **+567%** |
| Qualidade plans | 60% completo | 90% completo | **+50%** |
| Conv lead→venda | 20% | 40% | **+100%** |
| Conv proposta→negoc | 40% | 65% | **+62%** |
| Reuniões negoc/sem | 2-3 | 4+ | **+50%** |

**ROI:**
- Investimento: R$ 36k (3 meses)
- 3 deals extras fechados: R$ 12M
- **ROI: 333:1**

---

## **Roadmap de Implementação (45 dias)**

**Semana 1-2: Setup Ferramentas**
- Contratar ZoomInfo + LinkedIn Sales Nav
- Configurar Bright Data para scraping
- Integrar com Salesforce
- Criar template account plan

**Semana 3-4: Automação**
- Make workflows de coleta
- Flowise para análise e geração
- Testar com 5 accounts reais
- Validar precisão vs manual

**Semana 5-6: Escala**
- Rollout para todo time vendas
- Treinamento em uso da ferramenta
- Monitorar qualidade e ajustar
- Criar biblioteca de best practices

**Metas 90 dias:**
- 🎯 Tempo account plan: 10 dias → 1 dia
- 🎯 Accounts trabalhados/exec: +500%
- 🎯 Conv lead→venda: 20% → 40%
- 🎯 Conv proposta→negoc: 40% → 65%
- 🎯 Reuniões negoc/sem: 4+

---

**Primeiros Passos:**
1. Selecionar 5 accounts prioritárias para pilotar
2. Mapear manualmente 1 account (baseline)
3. Contratar ZoomInfo trial
4. Criar template padrão de account plan
5. Automatizar coleta de dados dessas 5 accounts