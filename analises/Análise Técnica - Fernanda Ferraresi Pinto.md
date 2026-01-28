# **Análise Técnica - AI Canvas**

**Projeto:** Mapa de Vagas  
**Autor(a):** Fernanda Ferraresi Pinto  
**E-mail:** Fernanda.ferraresi01@gmail.com

---

## **Resumo do Problema**

**Contexto:** Sistema de gestão e controle de vagas abertas para gestores

**Ferramentas mencionadas:** Planilhas Excel (atual), plataformas de gerenciamento de projetos, agente de IA generativa

**Micro-problemas identificados:**

1. **Falta de visibilidade**: Gestores não sabem status das vagas em tempo real
2. **Controle manual em planilhas**: Propenso a erros e desatualização
3. **Sem previsibilidade**: Não sabe quanto tempo vai levar para fechar vaga
4. **Dificuldade em priorizar**: Não sabe quais vagas são mais críticas
5. **Relatórios manuais**: Tempo gasto compilando dados ao invés de agir

---

## **Sugestão de Implementação**

### **Solução 1: Dashboard Inteligente com Previsão**
**Ferramentas:** Google Sheets + AppScript + Flowise + Data Studio

**Google Sheets como base central:**
```
Colunas automáticas:
- ID Vaga
- Cargo
- Setor/Departamento
- Data Abertura
- Status (Aberta, Em Triagem, Em Entrevistas, Finalizada)
- Dias em Aberto
- Previsão de Fechamento (IA)
- Urgência (Alta/Média/Baixa - IA)
- Candidatos no Funil
- Última Atualização
```

**Flowise analisa e prevê:**
- Modelo: **GPT-4o-mini** (análise de dados históricos)
- **Componentes:**
  - Google Sheets Loader
  - Time Series Analysis
  - Prediction Tool
  - Structured Output

**AppScript atualiza automaticamente:**
```javascript
// Roda todo dia 8h
function atualizarDashboard() {
  // Calcula dias em aberto
  // Consulta Flowise para previsões
  // Classifica urgência
  // Envia alertas para gestores
}
```

**Data Studio visualiza:**
```
MAPA DE VAGAS - TEMPO REAL

📊 VISÃO GERAL:
Total abertas: 23 vagas
Tempo médio: 38 dias
Previsão fechamento: 15 dias (média)

🔴 CRÍTICAS (>60 dias): 4 vagas
  - Dev Senior Backend (82 dias)
  - Gerente Financeiro (67 dias)
  - UX Designer (64 dias)
  - Analista BI (61 dias)

🟡 ATENÇÃO (30-60 dias): 8 vagas
🟢 NO PRAZO (<30 dias): 11 vagas

📈 POR SETOR:
TI: 9 vagas (avg 42 dias)
Financeiro: 5 vagas (avg 35 dias)
RH: 3 vagas (avg 28 dias)
```

---

### **Solução 2: Previsão de Tempo com Machine Learning**
**Ferramentas:** DataRobot + Google Sheets

**Treinar modelo com histórico:**
```
Features:
- Cargo/Senioridade
- Setor
- Salário range
- Localização (remoto/presencial)
- Sazonalidade (mês do ano)
- Candidatos disponíveis no mercado

Target: Dias até fechar vaga
```

**Previsões em tempo real:**
```
Vaga: Dev Senior Backend
Histórico similar: 45-60 dias

Análise DataRobot:
- Cargo técnico senior: +15 dias
- Mercado aquecido: +10 dias
- Salário competitivo: -5 dias
- Janeiro (baixa movimentação): +7 dias

PREVISÃO: 62 dias (±8 dias)
Probabilidade fechar em:
- 30 dias: 15%
- 45 dias: 40%
- 60 dias: 75%
- 90 dias: 95%
```

---

### **Solução 3: Alertas Inteligentes e Recomendações**
**Ferramentas:** Make + Flowise + Slack/Email

**Tipos de alertas:**

**1. Vaga parada (sem movimento):**
```
Slack para Gestor:
"⚠️ Vaga Dev Backend está há 7 dias sem candidatos.

Ações sugeridas pela IA:
1. Revisar requisitos (podem estar restritivos)
2. Aumentar divulgação LinkedIn
3. Considerar flexibilizar salário
4. Abrir para remoto

[VER DETALHES] [TOMAR AÇÃO]"
```

**2. Vaga próxima do SLA:**
```
Email para RH:
"🕐 Vaga Gerente Financeiro em 55 dias (SLA: 60).

Candidatos no funil: 2 (em entrevista final)
Probabilidade fechar no prazo: 68%

Riscos:
- Candidato A pode receber outra oferta
- Candidato B pediu 10 dias para decidir

Sugestões:
- Acelerar decisão interna
- Preparar proposta competitiva
- Ter plano B (lista reserva)"
```

**3. Benchmark e comparação:**
```
"📊 Relatório Semanal - Vagas

Sua performance vs empresa:
- Tempo médio: 38 dias (empresa: 42) ✅
- Taxa de fechamento: 85% (empresa: 78%) ✅
- Vagas críticas: 4 (empresa avg: 2) ⚠️

Setor TI está lento:
9 vagas, 42 dias médio (vs 35 outros setores)

Recomendação: Revisar estratégia recrutamento TI"
```

---

### **Solução 4: Assistente Virtual para Gestores**
**Ferramentas:** Flowise + WhatsApp/Slack Bot

**Chatbot integrado ao dashboard:**

```
Gestor: "Status da vaga de Designer?"

Bot consulta Google Sheets:
"Vaga UX Designer (#VG-045):
📅 Aberta há: 64 dias
📊 Status: Em entrevistas finais
👥 Candidatos: 3 (2 em 2ª entrevista, 1 aguardando)
⏱️ Previsão fechamento: 12 dias
🎯 Urgência: Alta

Última atualização: Hoje, 14h
Por: Ana (RH)

[VER CANDIDATOS] [HISTÓRICO] [URGIR RH]"

---

Gestor: "Por que tá demorando tanto?"

Bot (Flowise analisa histórico):
"Analisei o processo e identifiquei:

Gargalos:
1. Triagem inicial demorou 18 dias (avg: 7)
   → RH estava com equipe reduzida
2. Feedback de entrevistas lentos (5-7 dias)
   → Sugestão: Definir SLA 48h
3. Requisitos técnicos específicos
   → Poucos candidatos no mercado

Ações tomadas:
✅ RH priorizou a vaga
✅ 2 candidatos fortes em final
✅ Previsão positiva próximos dias"
```

---

### **Solução 5: Automação de Atualizações**
**Ferramentas:** Make + Formulários + Integração ATS

**Fluxo automatizado:**

**1. Integração com ATS (Applicant Tracking System):**
```javascript
// Make conecta via API
Gupy/Recruiter.com → Google Sheets

Atualiza automaticamente:
- Novos candidatos aplicados
- Mudanças de etapa (triagem → entrevista)
- Candidatos aprovados/reprovados
- Feedback de entrevistadores
```

**2. Formulário rápido para gestores:**
```
Google Forms mobile-friendly:
"Atualização Vaga #VG-045"

- Houve entrevista hoje? Sim/Não
- Como foi? 1-5 estrelas
- Avança para próxima etapa? Sim/Não
- Observações: _______

Submit → Google Sheets atualiza → IA recalcula previsão
```

**3. Relatório automático sexta 17h:**
```
Email para todos os gestores:

"📊 RESUMO SEMANAL - SUAS VAGAS

TI (João):
- 4 vagas abertas
- 2 fechadas esta semana ✅
- 1 crítica (atenção!)

Financeiro (Maria):
- 3 vagas abertas
- 1 fechada ✅
- Todas no prazo ✅

DESTAQUES:
🏆 Maria fechou Analista Fiscal em 22 dias!
⚠️ João, vaga Dev Backend precisa atenção

[VER DASHBOARD COMPLETO]"
```

---

### **Pontos de Atenção**

**Dados:**
- **Qualidade do histórico**: Precisa 12+ meses para previsões confiáveis
- **Padronização**: Cargos e status devem ser consistentes
- **Atualização em tempo real**: Integração com ATS é essencial
- **Privacidade**: Dados de candidatos são sensíveis (LGPD)

**Gestão de Mudanças:**
- **Adoção**: Gestores precisam atualizar regularmente
- **Treinamento**: Dashboard deve ser intuitivo
- **Responsabilidades**: Definir quem atualiza o quê
- **Disciplina**: Sistema só funciona se alimentado

**Técnico:**
- **Google Sheets limite**: 5M células, considerar BigQuery se crescer
- **AppScript quotas**: Limitações de execução/dia
- **APIs**: Nem todo ATS tem API aberta
- **Backup**: Sempre manter cópias de segurança

**Custos:**
- Google Workspace: R$ 30-60/usuário/mês
- Data Studio: Gratuito
- Make: R$ 50-150/mês
- DataRobot: Trial gratuito, depois negociar
- **Total:** ~R$ 500-1.500/mês
- **ROI:** Reduzir 10 dias no fechamento de vagas = R$ 50k+ economizado/ano

---

## **Exemplo de Recomendação**

### **Caso: Reduzir Tempo Médio de 42 para 28 Dias em 3 Meses**

**Situação inicial (janeiro):**
```
23 vagas abertas
Tempo médio fechamento: 42 dias
4 vagas críticas (>60 dias)
Gestores sem visibilidade
Processos manuais
```

**Semana 1 - Deploy do sistema:**

**Setup:**
- Google Sheets centralizado
- Data Studio dashboard
- Integração com Gupy (ATS)
- Flowise configurado

**Migração de dados:**
```
Importa 100 vagas históricas (12 meses)
DataRobot treina modelo preditivo
Acurácia inicial: 78%
```

---

**Semana 2-4 - Primeiros insights:**

**Dashboard revela problemas:**
```
GARGALOS IDENTIFICADOS:

1. Triagem inicial: 12 dias (deveria ser 3-5)
   → RH sobrecarregado

2. Feedback entrevistas: 7 dias (deveria ser 48h)
   → Gestores não priorizam

3. Vagas TI: 52 dias médio (vs 35 outros)
   → Requisitos muito específicos
```

**IA sugere ações:**
```
Recomendações Flowise:

Para RH:
- Contratar temporário para triagens
- Implementar pre-screening com IA

Para Gestores:
- SLA 48h para feedback entrevistas
- Criar template padrão de avaliação

Para TI:
- Revisar requisitos (menos "must have")
- Abrir mais para remoto (ampliar pool)
```

---

**Mês 2 - Melhorias implementadas:**

**Ações tomadas:**
```
✅ RH contratou estagiário
✅ SLA 48h feedback implementado
✅ TI flexibilizou 60% dos requisitos
✅ 40% vagas TI abertas para remoto
```

**Primeiros resultados:**
```
5 vagas fechadas
Tempo médio: 38 dias (-10%)
Vagas críticas: 2 (-50%)

Destaques:
- Dev Backend fechou em 28 dias (vs 82 anterior)
- Analista BI fechou em 24 dias (vs 61 anterior)
```

---

**Mês 3 - Otimização contínua:**

**IA aprende e melhora:**
```
Acurácia previsões: 78% → 86%

Novos insights:
- Vagas com salário 15%+ mercado fecham 40% mais rápido
- Remoto reduz tempo em 25%
- Janeiro-Fevereiro são mais lentos (+12 dias)
```

**Gestores usam proativamente:**
```
João (TI): "IA previu 55 dias para vaga Backend.
            Aumentei salário 10%, fechou em 32!"

Maria (Financeiro): "Alerta indicou risco de perder
                     candidato. Acelerei proposta,
                     fechou em 18 dias!"
```

---

**Resultado final (fim do trimestre):**

**Métricas:**
```
| Métrica | Antes | Depois | Melhoria |
|---------|-------|--------|----------|
| Tempo médio | 42 dias | 28 dias | **-33%** |
| Vagas críticas | 4 | 0 | **-100%** |
| Taxa fechamento | 78% | 92% | **+18%** |
| Satisfação gestores | 6/10 | 9/10 | **+50%** |
| Horas RH/semana | 25h | 12h | **-52%** |

Vagas fechadas no trimestre: 47
Tempo economizado: 658 dias-vaga
Custo economizado: R$ 180k (custo de posições vagas)
```

**ROI:**
```
Investimento: R$ 4.500 (3 meses)
Economia: R$ 180k
ROI: 40:1
```

**Depoimentos:**
```
João (Gestor TI): "Antes eu não sabia o status das
vagas. Agora tenho visibilidade total e a IA me avisa
antes dos problemas."

Ana (RH): "O dashboard me mostra exatamente onde
focar. Não perco mais tempo com planilhas."

Maria (Gerente Financeiro): "Consegui prever quando
minha equipe estaria completa. Isso mudou tudo."
```

---

## **Roadmap de Implementação (30 dias)**

**Semana 1: Estruturação**
- Criar Google Sheets modelo
- Definir campos e status padrão
- Importar dados históricos
- Treinar DataRobot

**Semana 2: Dashboard**
- Configurar Data Studio
- Criar visualizações principais
- Implementar alertas Make
- Testar com 5 gestores piloto

**Semana 3: IA e Automação**
- Integrar Flowise para previsões
- Conectar ATS via API
- Criar chatbot (opcional)
- Automatizar relatórios

**Semana 4: Rollout**
- Treinamento todos os gestores
- Documentação e FAQs
- Suporte intensivo primeira semana
- Coleta de feedback

**Metas 90 dias:**
- 🎯 Tempo médio: -30%
- 🎯 Vagas críticas: Zero
- 🎯 Satisfação gestores: 9/10
- 🎯 Taxa fechamento: +15%
- 🎯 Previsões acuradas: 85%+

---

**Primeiros Passos:**
1. Exportar histórico últimos 12 meses
2. Padronizar nomenclaturas (cargos, status)
3. Criar conta Google Data Studio
4. Mapear integrações disponíveis (ATS, email)
5. Recrutar 3-5 gestores para piloto