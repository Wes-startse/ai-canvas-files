# **Análise Técnica - AI Canvas**

**Projeto:** Redução de Turnover e Melhoria de Treinamento  
**Autor(a):** Carlos Eduardo Murrer Gomes  
**E-mail:** carlos@protrade.com.br

**Contexto:** Franquia McDonald's com 8 unidades e ~350 funcionários

---

## **Resumo do Problema**

**Ferramentas já mencionadas:** Aplicativo Android/Web (a ser desenvolvido), dispositivos corporativos

**Micro-problemas identificados:**

1. **Treinamento inadequado**: Prioridade na operação impede ensino correto
2. **Falta de acolhimento humano**: Novos colaboradores se sentem abandonados
3. **Ambiente de alta pressão**: Picos de movimento dificultam onboarding
4. **Aprendizado incompleto**: Erros por falta de capacitação geram insegurança
5. **Ciclo negativo**: Pressão → Erro → Mais pressão → Turnover → Mais pressão

**Indicadores atuais alarmantes:**
- Turnover alto nos primeiros 90 dias
- Absenteísmo frequente
- Colaboradores desmotivados

---

## **Sugestão de Implementação**

### **Solução 1: App de Check-in Inteligente com IA**
**Ferramentas:** Flowise + Google AppSheet + Firebase

**Arquitetura:**

**App Mobile (Google AppSheet - No-Code):**
- Interface simples para check-in/out
- Funciona offline (sincroniza depois)
- Integrado com Firebase para dados em tempo real

**Flowise como "cérebro" do sistema:**
- Modelo: **GPT-4o** (melhor para empatia e personalização)
- **Componentes:**
  - **Memory/Vector Store** com histórico do colaborador
  - **Sentiment Analysis** para detectar estado emocional
  - **Conversational Chain** para perguntas adaptativas
  - **Structured Output Parser** (prioridades para liderança)

**Fluxo de uso:**

**7h - Check-in início do turno (2 min no totem)**
```
App: "Bom dia, João! Como você está se sentindo hoje?"

João: "Um pouco nervoso, é meu 3º dia"

IA analisa:
- Nível experiência: Iniciante (dia 3)
- Sentimento: Ansiedade moderada
- Foco: Estação de frituras (ontem teve dificuldade)

Output para GP:
```json
{
  "colaborador": "João Silva",
  "nivel": "INICIANTE_DIA_3",
  "sentimento": "ANSIOSO",
  "prioridade": "ALTA",
  "foco_atencao": "Estação frituras - teve dificuldade ontem",
  "sugestao_padrinho": "Carlos (experiente em frituras)",
  "alertas": ["Primeiro pico de movimento hoje", "Observar confiança"],
  "microtreinamento": "Revisão rápida: tempos de fritura"
}
```

**7:05h - GP faz acolhimento presencial**
```
GP: "João, vi que você está um pouco nervoso.
     Hoje vou te colocar com o Carlos nas frituras.
     Ele vai te ajudar, sem pressa. Combinado?"

João se sente acolhido e direcionado ✅
```

---

### **Solução 2: Microtreinamento Personalizado no Intervalo**
**Ferramentas:** Flowise + ElevenLabs + Google AppSheet

**Durante intervalo/almoço (10 min):**

**App oferece check-in intermediário:**
```
App: "E aí João, como foi a manhã?"

João: "Confuso com os tempos de fritura, 
       não sei se estou fazendo certo"

IA detecta:
- Dúvida específica: Tempos de fritura
- Confiança: Baixa
- Necessidade: Microtreinamento urgente
```

**App oferece conteúdo personalizado:**

**Opção 1: Vídeo curto (2 min)**
- "Tempos de fritura - Guia rápido"
- Voz gerada por ElevenLabs (natural e amigável)
- Mostra cronômetro visual

**Opção 2: Quiz interativo**
```
"Quantos minutos para batata frita?"
a) 2:30
b) 3:00 ✅
c) 3:30

"Isso mesmo! Vou te lembrar disso à tarde."
```

**Opção 3: Chamar padrinho**
```
"Quer que eu peça pro Carlos vir conversar com você?"
[SIM] [DEPOIS]
```

**Make notifica padrinho:**
```
WhatsApp Carlos: "João está com dúvida em frituras.
Pode dar uma passada nele depois do almoço?"
```

---

### **Solução 3: Check-out com Feedback Emocional**
**Ferramentas:** Flowise + Sentiment Analysis + Dashboard

**17h - Final do turno (3 min):**

**App faz check-out:**
```
App: "João, como foi seu dia?"

João: "Melhor que ontem! Carlos me ajudou muito"

IA analisa:
- Sentimento: POSITIVO (evolução vs manhã)
- Confiança: Aumentou 
- Padrinho Carlos: Efetivo
- Ponto de melhoria: Ainda tem dúvidas, mas está progredindo
```

**GP recebe resumo do dia:**
```
RESUMO - João Silva (Dia 3)

Check-in: 😟 Ansioso
Check-out: 😊 Melhor

Evolução: ↗️ Positiva
Padrinho: Carlos (efetivo)
Dúvidas: Tempos de fritura (parcialmente resolvido)

Ação para amanhã:
- Reforço positivo no início do turno
- 10 min de prática guiada em frituras
- Continuar com Carlos como padrinho
```

**GP encerra dia com conversa presencial (1 min):**
```
GP: "João, vi que você evoluiu hoje! 
     Amanhã vamos praticar mais um pouco.
     Tá indo bem, continue assim!"

João sai motivado ✅
```

---

### **Solução 4: Dashboard Preditivo de Turnover**
**Ferramentas:** Flowise + DataRobot + Google Data Studio

**Análise semanal automatizada:**

**Dados consolidados:**
- Check-ins diários (sentimento, confiança)
- Frequência de dúvidas
- Absenteísmo
- Feedback de padrinhos
- Erros operacionais

**DataRobot treina modelo preditivo:**
```
Input features:
- Sentimento médio (7 dias)
- Número de dúvidas não resolvidas
- Faltas (primeiros 30 dias)
- Interação com padrinho (frequência)
- Erros cometidos

Output:
- Probabilidade de turnover 90 dias
```

**Dashboard para DHO e Gerentes:**
```
RISCOS DE TURNOVER - Semana 04

🔴 ALTO RISCO (85%+)
   - Maria Santos (Loja 3): 92%
     Motivo: 3 faltas + sentimento negativo + sem padrinho
     Ação: Reunião 1:1 urgente + realocar padrinho

🟡 MÉDIO RISCO (60-85%)
   - Pedro Costa (Loja 1): 71%
     Motivo: Dúvidas não resolvidas + baixa confiança
     Ação: Reforço treinamento + acompanhamento GP

🟢 BAIXO RISCO (<60%)
   - 28 colaboradores
     Média satisfação: 8.2/10
```

**Ação proativa:**
- Make dispara alertas para GUN quando risco >80%
- GP recebe checklist de intervenção
- DHO acompanha semanalmente

---

### **Solução 5: Biblioteca de Conteúdo Personalizado**
**Ferramentas:** ChatGPT + Gamma + ElevenLabs

**Criação automatizada de microtreinamentos:**

**1. Identificar top 10 dúvidas recorrentes:**
```
Flowise analisa últimos 90 dias:
- Dúvida #1: Tempos de fritura (47 ocorrências)
- Dúvida #2: Montagem Big Mac (39 ocorrências)
- Dúvida #3: Atendimento drive-thru (31 ocorrências)
```

**2. ChatGPT gera scripts de 2 minutos:**
```
Prompt: "Crie explicação simples e amigável sobre
'Tempos de Fritura do McDonald's' para novo funcionário.
Formato: passo a passo visual, máx 2 minutos."
```

**3. Gamma transforma em apresentação visual**
- Slides com imagens de produto
- Cronômetros visuais
- Checklist de validação

**4. ElevenLabs converte em áudio/vídeo**
- Narração natural em português BR
- Tom encorajador e didático

**5. Disponível no app para acesso sob demanda**

---

### **Pontos de Atenção**

**Tecnologia:**
- **App Android prioritário** (maioria dos colaboradores)
- **Offline-first:** Lojas podem ter WiFi instável
- **UX simples:** Colaboradores têm níveis variados de alfabetização digital
- **Privacidade:** Dados emocionais são sensíveis (LGPD)

**Implementação:**
- **Não usar IA durante operação:** Zero distração, foco total no atendimento
- **Momentos permitidos:** Apenas check-in (início), intervalo e check-out (fim)
- **Humano sempre presente:** IA prepara, humano executa o acolhimento
- **Liderança treinada:** GPs precisam saber como usar insights da IA

**Cultural:**
- **Transparência:** Explicar aos colaboradores que IA os ajuda, não vigia
- **Opt-in para sentimentos:** Deixar claro que podem compartilhar ou não
- **Padrinho voluntário:** Não forçar, incentivar colaboradores experientes
- **Celebração de progresso:** Dashboard mostra evolução positiva, não só problemas

**Custos:**
- Google AppSheet: $5-10/usuário/mês (350 usuários = R$ 8k-16k/mês)
- OpenAI API: R$ 200-500/mês (microtreinamentos + análise)
- ElevenLabs: R$ 500-1k/mês (narração de vídeos)
- Firebase: R$ 500-1k/mês (banco de dados)
- DataRobot: Trial gratuito, depois negociar
- **Total:** R$ 10k-20k/mês
- **ROI:** Reduzir 70% turnover economiza R$ 180k/mês (custo de contratação + treinamento)

---

## **Exemplo de Recomendação**

### **Caso: João - Da Ansiedade ao Sucesso em 14 Dias**

**Dia 1 (Segunda) - Primeiro dia de João**

**7h - Check-in:**
```
App: "Bem-vindo, João! Primeiro dia, né? Nervoso?"
João: "Muito! Nunca trabalhei em fast-food"

IA detecta:
- Nivel: TOTALMENTE_NOVO
- Sentimento: MUITO_ANSIOSO
- Prioridade: MÁXIMA

Envia para GP:
"João precisa atenção extra. Sugestão: colocar com
padrinho mais paciente, evitar pico nas primeiras 4h"
```

**7:10h - GP acolhe pessoalmente:**
```
"João, calma! Todo mundo aqui começou assim.
Vou te colocar com a Ana, ela é excelente.
Hoje você só observa, sem pressão. Ok?"
```

**12h - Intervalo - Microtreinamento:**
```
App: "Como foi a manhã?"
João: "Muita coisa ao mesmo tempo, tô perdido"

App oferece: "Quer ver um vídeo de 3 min sobre
o básico do atendimento?"

João assiste: "Ordem de atendimento: 
1. Cumprimentar
2. Anotar pedido
3. Confirmar
4. Processar pagamento
5. Entregar com sorriso"
```

**17h - Check-out:**
```
App: "Primeiro dia done! Como foi?"
João: "Cansativo, mas a Ana me ajudou muito"

IA registra:
- Sentimento: CANSADO_MAS_POSITIVO
- Padrinho efetivo: Ana
- Próxima etapa: Prática observada amanhã
```

**Dia 3 (Quarta) - Primeira prática solo**

**7h - Check-in:**
```
App: "João, hoje você vai atender sozinho pela primeira vez.
Lembra dos 5 passos? [VER RESUMO]"

João: "Lembro, mas tô nervoso"

IA: "Normal! Qualquer dúvida, chama a Ana. Você consegue!"

Notifica Ana: "João está nervoso, dar suporte próximo"
```

**Durante o turno (humano conduz):**
- Ana fica perto sem interferir
- João atende 15 clientes com sucesso
- 2 erros pequenos, corrigidos gentilmente

**17h - Check-out:**
```
App: "Como foi atender sozinho?"
João: "Cometi 2 erros, mas me sinto mais confiante"

IA: Score de confiança: 6/10 (evolução positiva)
```

**Dia 7 (Segunda seguinte) - Primeira semana completa**

**Dashboard mostra:**
```
JOÃO SILVA - 7 DIAS

Evolução sentimento:
Dia 1: 😰 3/10
Dia 7: 😊 7/10

Confiança:
Início: 20%
Atual: 70%

Dúvidas resolvidas: 8
Microtreinamentos assistidos: 5
Feedback padrinho: Positivo

Probabilidade turnover 90d: 15% (BAIXA) ✅
```

**GUN recebe relatório:**
```
"João está progredindo bem. Ana é uma ótima padrinho.
Continuar acompanhamento semanal."
```

**Dia 14 (Segunda) - 2 semanas**

**7h - Check-in:**
```
App: "João, você já atendeu 180 clientes!
Como está se sentindo?"

João: "Muito melhor! Já sei a maioria das coisas"

IA: Confiança: 85%, pronto para estação intermediária
```

**GP promove:**
```
"João, você evoluiu rápido! Semana que vem vou te 
ensinar a operar o drive-thru. Bora?"
```

**Dia 90 - Fim do onboarding**

**App solicita NPS:**
```
"De 0 a 10, como foi sua experiência de treinamento?"
João: 10/10

"O que mais ajudou?"
João: "A Ana sempre perto e os vídeos no intervalo.
       Me senti acolhido desde o dia 1."
```

**Resultado:**
- ✅ João completou 90 dias (não deixou a empresa)
- ✅ NPS: 10/10
- ✅ Zero absenteísmo
- ✅ Promovido a padrinho de outro novo colaborador

**Comparativo (350 colaboradores, 90 dias):**

| Métrica | Antes | Com IA | Melhoria |
|---------|-------|--------|----------|
| Turnover 90 dias | 55% | 16.5% | **-70%** |
| Turnover geral | 120%/ano | 84%/ano | **-30%** |
| NPS onboarding | N/A | 100% (meta) | **N/A** |
| Absenteísmo 90d | 18% | <1% | **-94%** |
| Custo contratação | R$ 15k/pessoa | R$ 15k/pessoa | **Mesma** |
| **Economia anual** | - | **R$ 2.1M** | **Redução 70% turnover** |

**ROI:**
- Investimento: R$ 240k/ano (ferramentas + setup)
- Economia: R$ 2.100k/ano (menos contratações + treinamentos)
- **ROI: 8.7:1**

---

## **Roadmap de Implementação (60 dias)**

**Semana 1-2: Design e Prototipação**
- Criar wireframes do app (Figma)
- Desenvolver versão básica no Google AppSheet
- Configurar Firebase para armazenamento
- Recrutar 10 colaboradores para teste

**Semana 3-4: IA e Integração**
- Configurar Flowise com lógica de análise
- Treinar modelo com histórico de turnover
- Implementar sentiment analysis
- Criar biblioteca básica de microtreinamentos

**Semana 5-6: Piloto em 1 Loja**
- Testar com todos colaboradores da Loja 1
- Treinar GPs e padrinhos
- Coletar feedback diário
- Ajustar fluxos e interface

**Semana 7-8: Rollout Completo**
- Expandir para 8 lojas
- Treinamento de toda liderança
- Monitorar métricas semanalmente
- Criar plano de melhoria contínua

**Metas 6 meses:**
- 🎯 Turnover 90 dias: Reduzir 70%
- 🎯 Turnover geral: Reduzir 30%
- 🎯 NPS onboarding: 100%
- 🎯 Absenteísmo 90d: Zero
- 🎯 Economia: R$ 1M+ (menos contratações)

---

**Primeiros Passos:**
1. Mapear jornada atual de onboarding (entrevistar 10 novos colaboradores)
2. Identificar top 20 dúvidas recorrentes
3. Criar conta Google AppSheet + Firebase
4. Recrutar 5 padrinhos para piloto
5. Agendar workshop com GPs e GUNs para apresentar solução