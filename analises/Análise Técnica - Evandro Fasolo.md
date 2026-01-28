# **Análise Técnica - AI Canvas**

**Projeto:** Recrutamento via IA  
**Autor(a):** Evandro Fasolo  
**E-mail:** evandro@cloudcanal.com.br

---

## **Resumo do Problema**

**Contexto:** Dificuldade em encontrar profissionais técnicos qualificados (desenvolvimento, implantação, suporte técnico)

**Ferramentas mencionadas:** LinkedIn, Glassdoor, GitHub, Stack Overflow, Jira, Trello, Coursera, Udacity

**Micro-problemas identificados:**

1. **Processo de recrutamento lento**: 6 etapas manuais consomem semanas
2. **Dificuldade em identificar talento técnico**: Recrutadores não entendem skills necessárias
3. **Triagem ineficiente de currículos**: Muitos candidatos inadequados passam
4. **Avaliação técnica subjetiva**: Falta validação prática de conhecimento
5. **Altos custos de contratação e turnover**: Contratações erradas custam caro

---

## **Sugestão de Implementação**

### **Solução 1: Triagem Automática de Currículos com IA**
**Ferramentas:** Flowise + Make + LinkedIn API

**Fluxo automatizado:**

**1. Recebimento (Make):**
```
Candidato aplica via:
- LinkedIn
- Email corporativo
- Formulário site
→ Make centraliza tudo
```

**2. Análise Inteligente (Flowise):**
- Modelo: **Claude 3.5 Sonnet** (melhor para análise técnica)
- **Componentes:**
  - **PDF Loader** (currículo)
  - **Custom Tool** para buscar GitHub/Stack Overflow
  - **Scoring Algorithm** baseado em requisitos
  - **Structured Output Parser** (JSON com análise)

**Critérios de avaliação:**
```javascript
requisitos_vaga = {
  obrigatorio: ["Python", "AWS", "Docker", "3+ anos exp"],
  desejavel: ["Kubernetes", "CI/CD", "Terraform"],
  diferencial: ["Contribuições open-source", "Certificações"]
};

// Flowise pontua:
score_obrigatorio = 80 (passa se >= 70)
score_desejavel = 60
score_diferencial = 40
score_final = 73 (média ponderada)
```

**Output:**
```json
{
  "candidato": "João Silva",
  "score": 73,
  "classificacao": "BOM_FIT",
  "match_obrigatorio": ["Python ✅", "AWS ✅", "Docker ✅", "5 anos exp ✅"],
  "match_desejavel": ["Kubernetes ✅", "CI/CD ❌", "Terraform ✅"],
  "diferenciais": ["10 repos GitHub", "Certificação AWS Solutions Architect"],
  "red_flags": [],
  "recomendacao": "AGENDAR_ENTREVISTA_TECNICA",
  "proximo_passo": "Teste prático Python + AWS"
}
```

---

### **Solução 2: Teste Técnico Automatizado**
**Ferramentas:** HackerRank/Codility + Make + Flowise

**Após triagem aprovada:**

**1. Make envia convite automático:**
```
Email personalizado:
"Olá João! Gostamos do seu perfil.

Próxima etapa: Desafio técnico (90 min)
- 2 questões Python
- 1 questão AWS (infraestrutura como código)
- 1 questão Docker (debugging)

Pode fazer quando quiser nos próximos 7 dias.
[COMEÇAR TESTE]"
```

**2. Plataforma (HackerRank) aplica automaticamente**

**3. Flowise analisa resultado:**
```javascript
// Além da nota, analisa COMO resolveu
Flowise.analyze({
  codigo: candidato.solucao,
  criterios: [
    "Clean Code",
    "Eficiência (Big O)",
    "Tratamento de erros",
    "Boas práticas",
    "Comentários úteis"
  ]
});

Output:
score_tecnico = 82
qualidade_codigo = "Alta"
comentario_ia = "Código limpo, eficiente. Bom tratamento
                 de exceções. Faltou apenas documentação
                 de funções complexas."
```

---

### **Solução 3: Entrevista com IA (Screening Inicial)**
**Ferramentas:** Flowise + ElevenLabs + Whisper

**Entrevista assíncrona por vídeo:**

**Setup:**
- Candidato recebe link para plataforma
- Responde 5-7 perguntas em vídeo (5 min cada)
- IA analisa respostas

**Perguntas geradas por GPT-4:**
```
1. "Conte sobre um projeto desafiador com AWS.
    Como você resolveu?"

2. "Explique a diferença entre Docker e VM para
    alguém não-técnico."

3. "Você está debugando uma app que deu timeout.
    Qual seu processo de investigação?"
```

**Flowise analisa vídeos:**
- **Whisper** transcreve áudio
- **GPT-4o** analisa conteúdo técnico
- **Sentiment Analysis** avalia comunicação

**Output:**
```json
{
  "conhecimento_tecnico": 8.5,
  "comunicacao": 9.0,
  "problem_solving": 8.0,
  "fit_cultural": 8.5,
  "red_flags": [],
  "destaques": [
    "Excelente comunicação técnica",
    "Experiência sólida em troubleshooting",
    "Proativo em buscar soluções"
  ],
  "recomendacao": "AVANÇAR_PARA_ENTREVISTA_PRESENCIAL"
}
```

---

### **Solução 4: Match de Cultura com NLP**
**Ferramentas:** Flowise + LinkedIn Scraping

**Análise além do técnico:**

**Flowise analisa:**
1. **LinkedIn** do candidato:
   - Posts e comentários
   - Interações e temas de interesse
   - Estilo de comunicação

2. **GitHub** (se público):
   - README dos projetos (escrita)
   - Issues e PRs (colaboração)
   - Commits (consistência)

**GPT-4o gera perfil comportamental:**
```
João Silva - Perfil Comportamental

ESTILO DE TRABALHO:
- Colaborativo (80% dos commits em equipe)
- Detail-oriented (READMEs completos)
- Comunicativo (ativo em issues)

VALORES:
- Código limpo e documentado
- Aprendizado contínuo (certificações)
- Open-source (contribuições regulares)

FIT CULTURAL COM EMPRESA:
Match: 87%
- Alinhado com valores de colaboração ✅
- Busca excelência técnica ✅
- Ativo em comunidade dev ✅

PONTOS DE ATENÇÃO:
- Pode preferir trabalho remoto (90% posts home office)
- Gosta de autonomia (projetos solo no GitHub)
```

---

### **Solução 5: Onboarding Acelerado com IA**
**Ferramentas:** Flowise + Gamma + Notion

**Após contratação:**

**1. Plano de 90 dias personalizado:**
```
Flowise analisa:
- Skills do candidato
- Gaps vs necessidades da vaga
- Projetos disponíveis para alocar

GPT-4o gera plano:
"João - Plano de Integração 90 dias

SEMANA 1-2: FUNDAMENTOS
- Setup ambiente (Docker, AWS, repos)
- Conhecer time e processos
- Projeto piloto: Migrar microserviço X

SEMANA 3-4: AUTONOMIA CRESCENTE
- Implementar feature Y
- Code review com sênior
- Treinamento Kubernetes

MÊS 2: OWNERSHIP
- Assumir microserviço Z
- Participar planning
- Mentorar júnior

MÊS 3: VALIDAÇÃO
- Projeto crítico solo
- Apresentação técnica time
- Avaliação final"
```

**2. Gamma gera apresentações:**
```
Treinamentos personalizados:
- "Arquitetura da Empresa" (nível João)
- "Boas Práticas DevOps" (gaps identificados)
- "Cultura e Processos"
```

**3. Acompanhamento automático:**
```
Make agenda check-ins:
- Dia 7: "Como foi a primeira semana?"
- Dia 30: "Avaliação do primeiro mês"
- Dia 60: "Feedback sobre autonomia"
- Dia 90: "Avaliação final de experiência"
```

---

### **Pontos de Atenção**

**Viés Algorítmico:**
- IA pode discriminar involuntariamente (gênero, idade, origem)
- Revisar periodicamente decisões da IA
- Manter humano na decisão final (IA recomenda, humano decide)
- Transparência com candidatos sobre uso de IA

**Qualidade dos Dados:**
- Perfis LinkedIn podem estar desatualizados
- GitHub não reflete 100% das habilidades
- Teste técnico avalia apenas parte das competências
- Entrevista humana ainda é essencial

**Experiência do Candidato:**
- Processo muito automatizado pode parecer frio
- Manter comunicação humanizada
- Feedback rápido em cada etapa (24-48h)
- Transparência sobre etapas e critérios

**Custos:**
- HackerRank: $300-500/mês (até 100 testes)
- LinkedIn API: $50-200/mês
- OpenAI: $100-300/mês
- Make: $50-150/mês
- **Total:** ~$500-1.150/mês
- **ROI:** Uma contratação errada custa R$ 50k+ (recontratação + treinamento)

---

## **Exemplo de Recomendação**

### **Caso: Contratação de Dev Senior em 14 Dias (vs 60 dias manual)**

**Dia 1 - Abertura da vaga (segunda, 9h):**

**RH cria vaga no sistema:**
```
Título: Dev Backend Senior - Python/AWS
Requisitos obrigatórios: Python, AWS, Docker, 5+ anos
Desejáveis: K8s, Terraform, CI/CD
```

**Make publica automaticamente:**
- LinkedIn Jobs
- Programathor
- GitHub Jobs
- Site da empresa

---

**Dia 1-3 - Candidaturas chegam:**

**78 candidatos aplicam**

**Flowise processa em 2 horas:**
```
Análise de 78 currículos:

✅ 12 candidatos: Score >70 (BOM_FIT)
🟡 28 candidatos: Score 50-70 (MÉDIO_FIT)
❌ 38 candidatos: Score <50 (NAO_FIT)

Top 3:
1. João Silva - Score 87
2. Maria Santos - Score 82
3. Pedro Costa - Score 79
```

**Make envia emails personalizados:**
```
✅ Top 12: "Parabéns! Próxima etapa: teste técnico"
🟡 Top 28: "Você está na lista reserva"
❌ Demais: "Obrigado pelo interesse. Não avançou desta vez."
```

---

**Dia 4-6 - Testes técnicos:**

**10 dos 12 fazem o teste**

**Resultados automáticos:**
```
João Silva: 88/100
  - Python: Excelente
  - AWS: Muito bom
  - Docker: Bom
  Qualidade código: Alta
  Tempo: 75 min (eficiente)

Maria Santos: 85/100
  - Python: Excelente
  - AWS: Excelente  
  - Docker: Médio
  Qualidade código: Muito alta
  Tempo: 89 min

Pedro Costa: 72/100
  - Python: Bom
  - AWS: Médio
  - Docker: Muito bom
  Qualidade código: Média
  Tempo: 90 min (usou todo tempo)
```

**Flowise reordena ranking:**
```
1. Maria Santos (score final: 85)
2. João Silva (score final: 84)
3. Pedro Costa (score final: 71)
```

---

**Dia 7 - Entrevistas com IA:**

**Top 3 recebem convite:**
```
"Última etapa antes da entrevista presencial:
Responda 5 perguntas em vídeo (30 min total).
Faça quando quiser nas próximas 48h."
```

**Todos fazem no mesmo dia**

**Flowise analisa:**
```
Maria Santos:
- Comunicação: 9.5/10 (clara, didática)
- Conhecimento: 9/10 (explicações profundas)
- Problem-solving: 8.5/10 (estruturada)
- Fit cultural: 9/10 (valores alinhados)
Red flags: Nenhum
Destaques: "Excelente comunicação técnica"

João Silva:
- Comunicação: 7/10 (menos fluente)
- Conhecimento: 9/10 (muito técnico)
- Problem-solving: 9/10 (criativo)
- Fit cultural: 8/10
Red flags: Nenhum
Destaques: "Técnico forte, pragmático"
```

---

**Dia 10 - Entrevista presencial:**

**Tech Lead entrevista Top 2** (Maria e João)

**Dashboard para Tech Lead:**
```
MARIA SANTOS - Candidata Recomendada #1

Resumo IA:
✅ Score técnico: 85
✅ Comunicação: Excelente
✅ Fit cultural: 87%
✅ 0 red flags

Pontos fortes:
- 7 anos Python, 4 anos AWS
- Código limpo e documentado
- Proativa em comunidade tech
- Ótima comunicadora

Pontos de atenção:
- Docker em nível intermediário (treinar)
- Preferência por trabalho híbrido

Perguntas sugeridas:
1. "Conte sobre seu maior desafio em AWS"
2. "Como você lida com prazos apertados?"
3. "Interesse em mentorar júniores?"
```

**Entrevistas confirmam análise da IA**

**Tech Lead decide: Maria**

---

**Dia 14 - Oferta e aceitação:**

**Proposta enviada:**
```
Salário: R$ 18k
Benefícios: VR, VA, plano saúde
Modelo: Híbrido (3x escritório)
```

**Maria aceita! 🎉**

---

**Comparativo processo:**

| Etapa | Manual | Com IA | Economia |
|-------|--------|--------|----------|
| Triagem currículos | 15h | 2h | **-87%** |
| Agendamento testes | 8h | Automático | **-100%** |
| Análise testes | 6h | Automático | **-100%** |
| Screening inicial | 12h | 1h (revisão) | **-92%** |
| **TOTAL** | **60 dias** | **14 dias** | **-77%** |

**Qualidade da contratação:**
- ✅ Fit técnico: 85/100 (validado)
- ✅ Fit cultural: 87/100 (IA previu corretamente)
- ✅ Comunicação: Excelente (confirmado na prática)
- ✅ Produtiva desde semana 1 (onboarding personalizado)

**ROI:**
- Custo processo IA: R$ 1.500
- Economia tempo RH/Tech Lead: 46h × R$ 150/h = R$ 6.900
- Contratação assertiva (vs turnover): R$ 50k economizado
- **ROI: 34:1**

---

## **Roadmap de Implementação (60 dias)**

**Semana 1-2: Triagem Inteligente**
- Configurar Flowise com scoring
- Conectar LinkedIn API + email
- Treinar com 100 currículos históricos
- Validar precisão vs triagem manual

**Semana 3-4: Testes Técnicos**
- Contratar HackerRank/Codility
- Criar biblioteca de testes por stack
- Integrar com Make para automação
- Pilotar com 10 candidatos

**Semana 5-6: Entrevista com IA**
- Implementar análise de vídeo (Flowise + Whisper)
- Criar banco de perguntas por vaga
- Testar com candidatos internos
- Ajustar critérios de avaliação

**Semana 7-8: Onboarding**
- Criar templates de planos 90 dias
- Gamma para materiais de treinamento
- Automação de check-ins (Make)
- Monitorar satisfação e retenção

**Metas 90 dias:**
- 🎯 Tempo de contratação: De 60 → 20 dias
- 🎯 Qualidade (fit técnico): +30%
- 🎯 Taxa de aprovação período experiência: De 70% → 95%
- 🎯 Satisfação candidatos (NPS): 8+/10
- 🎯 Custo por contratação: -50%

---

**Primeiros Passos:**
1. Mapear 5 vagas mais críticas (tech stack)
2. Coletar 100 currículos históricos (bons e ruins)
3. Definir critérios de scoring por vaga
4. Criar conta LinkedIn Recruiter + HackerRank
5. Pilotar com 1 vaga aberta