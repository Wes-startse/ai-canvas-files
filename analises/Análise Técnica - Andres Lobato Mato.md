# **Análise Técnica - AI Canvas**

**Projeto:** Orçamentos Acero  
**Autor(a):** Andres Lobato Mato  
**E-mail:** andres@cavicon.com.br

---

## **Resumo do Problema**

**Ferramentas já utilizadas:** BRERP (SQL/API), Grafico (sistema de produção - SQL/Google Sheets), Google Maps, Excel, Power BI

**Micro-problemas identificados:**

1. **Cálculo manual de bitola média**: Análise demorada dos materiais para precificar
2. **Consulta manual de distâncias**: Uso do Google Maps item por item
3. **Verificação manual de capacidades**: Checar produção e logística em sistemas diferentes
4. **Processo de orçamento lento**: Gargalo que atrasa resposta ao cliente
5. **Cadastros duplicados**: Retrabalho ao inserir cliente no sistema manualmente

---

## **Sugestão de Implementação**

### **Solução 1: Agente de Precificação Inteligente**
**Ferramentas:** Flowise + BRERP API + Google Sheets (Grafico)

**Arquitetura:**

**Entrada (via chat ou API):**
```
Cliente: ABC Construções
Produtos: 500kg aço CA-50 10mm, 300kg CA-60 8mm
Endereço entrega: Rua X, 123 - Cidade Y
```

**Flowise processa em 3 agentes paralelos:**

**Agente 1: Cálculo de Bitola Média**
- Modelo: **GPT-4o-mini** (operações matemáticas simples)
- **Componentes:**
  - Calculator Tool (nativo do Flowise)
  - Custom Function para fórmula de bitola média
  
```javascript
// Fórmula ponderada
bitola_media = (500*10 + 300*8) / (500+300)
bitola_media = 9.25mm
```

**Agente 2: Distância e Rota**
- **Custom Tool:** Google Maps Distance Matrix API
- **Componentes Flowise:**
  - HTTP Request Node
  - JSON Parser para extrair distância/tempo
  
```json
{
  "origem": "Fábrica Cavicon",
  "destino": "Rua X, 123 - Cidade Y",
  "distancia_km": 47,
  "tempo_estimado": "52 min"
}
```

**Agente 3: Verificação de Capacidade**
- Consulta SQL ao Grafico (produção) via API
- Consulta tabela de capacidades logísticas
- **Modelo:** Claude 3.5 Sonnet (melhor para análise de dados estruturados)

```sql
SELECT 
  data_disponivel_producao,
  capacidade_restante_kg
FROM producao
WHERE bitola_media BETWEEN 9 AND 10
  AND data >= CURRENT_DATE
ORDER BY data
LIMIT 1
```

**Output consolidado:**
```json
{
  "preco_venda": 4850.00,
  "data_producao": "2025-02-03",
  "data_entrega": "2025-02-05",
  "rota": "Via BR-101, 47km",
  "confianca": 95
}
```

---

### **Solução 2: Automação Completa com Make**
**Ferramentas:** Make + Flowise + BRERP + Google Maps API

**Fluxo Make (orçamento completo em 2 minutos):**

**1. Trigger:** Email/Form com solicitação de orçamento
```
Parse de: Cliente, produtos, quantidade, endereço
```

**2. Verificação Cliente (HTTP BRERP API):**
```
Se cliente existe → Busca ID
Se não existe → Cria cadastro automático
```

**3. Chamada Flowise (cálculos):**
```
POST /api/v1/prediction/{flowiseId}
Body: {cliente, produtos, endereco}
Retorno: {bitola_media, preco_base}
```

**4. Google Maps API:**
```
GET Distance Matrix
Adiciona custo frete baseado em km
```

**5. Consulta Capacidades (parallel):**
```
→ Query SQL Grafico (produção)
→ Query BRERP (logística)
→ Consolidação de datas
```

**6. Geração de Orçamento (GPT-4):**
```
Prompt: "Gere orçamento profissional para {cliente}
com produtos {lista}, preço {valor}, 
entrega em {data}"
```

**7. Salvar no BRERP:**
```
POST /api/orcamentos
Body: orçamento completo
Status: "Aguardando aprovação"
```

**8. Envio automático:**
```
Email ao cliente com PDF do orçamento
Notificação Slack para vendedor
```

---

### **Solução 3: GPT Customizado para Vendedores**
**Ferramentas:** ChatGPT Custom GPT + APIs externas

**Como criar:**
1. Ir em ChatGPT → Create Custom GPT
2. **Instruções:**
```
Você é assistente de orçamentos da Cavicon.

Ao receber pedido, execute:
1. Calcule bitola média ponderada
2. Consulte API Google Maps (distância)
3. Consulte API BRERP (capacidades)
4. Calcule preço = (base_por_kg * quantidade) + frete
5. Retorne JSON formatado
```

3. **Actions (conectar APIs):**
   - Google Maps Distance Matrix
   - BRERP API (GET clientes, POST orçamentos)
   - Grafico API (produção)

4. **Knowledge:** Upload de tabelas de preços e capacidades

**Vantagem:** Vendedores conversam naturalmente:
```
Vendedor: "Preciso orçar 800kg de CA-50 12mm 
           para a Construtora Silva em Curitiba"
           
GPT: "Analisando...
      Bitola média: 12mm
      Distância: 38km
      Produção disponível: 05/02
      Entrega: 06/02
      Preço: R$ 5.240,00
      
      Deseja gerar o orçamento?"
```

---

### **Solução 4: Dashboard Preditivo no Power BI**
**Ferramentas:** Power BI + Python (DAX + ML)

**Como implementar:**
1. Conectar Power BI ao BRERP e Grafico
2. Criar medidas DAX para calcular:
   - Taxa de conversão por faixa de preço
   - Tempo médio de resposta
   - Capacidade utilizada vs disponível

3. **Python Script no Power BI:**
```python
import pandas as pd
from sklearn.ensemble import RandomForestRegressor

# Treinar modelo de precificação otimizada
# Baseado em histórico de vendas
model = RandomForestRegressor()
model.fit(historico[['bitola', 'kg', 'distancia']], 
          historico['preco_vendido'])

# Prever melhor preço para maximizar conversão
```

4. Visual: Recomendação de preço competitivo

---

### **Pontos de Atenção**

**Integração:**
- **BRERP API:** Validar se tem endpoints de orçamentos e clientes
- **Grafico:** Se for apenas Google Sheets, usar API do Sheets (mais fácil)
- **Google Maps:** Requer API Key ($200 de crédito grátis/mês, depois $5/1000 requests)
- **Rate Limits:** BRERP pode ter limite de chamadas, implementar cache

**Dados:**
- **Tabela de preços atualizada:** Garantir que base de custos está correta
- **Histórico de 6 meses:** Para treinar modelo de precificação ótima
- **Cadastro de rotas:** Principais rotas de entrega pré-mapeadas
- **Capacidades dinâmicas:** Atualizar diariamente produção e logística

**Lógica de Negócio:**
- **Margem variável:** Preço pode variar por urgência, volume, cliente recorrente
- **Contingência:** Se produção cheia, sugerir data alternativa ou terceirização
- **Validação humana:** Orçamentos >R$50k passam por gerente antes de enviar
- **Ajuste manual:** Permitir vendedor modificar preço (com justificativa)

**Custos:**
- Make: $9-29/mês
- Google Maps API: ~$20-50/mês (depende do volume)
- OpenAI: ~$30-80/mês
- **ROI esperado:** 10:1 (economiza 80h/mês de vendedores)

---

## **Exemplo de Recomendação**

### **Caso: Orçamento para Construtora XYZ em 2 minutos**

**Solicitação recebida (9:15h via email):**
```
De: joao@construtoraxyz.com
Para: vendas@cavicon.com.br
Assunto: Orçamento urgente

Bom dia, preciso orçar:
- 1.200kg de aço CA-50 bitola 12mm
- 800kg de aço CA-60 bitola 8mm
Entrega em: Av. Paulista, 1000 - São Paulo/SP
Prazo: quanto antes possível
```

**9:15h - Make recebe email (webhook)**

**9:16h - Processamento paralelo**

**Thread 1: Verificação Cliente**
```
HTTP Request → BRERP API
GET /api/clientes?email=joao@construtoraxyz.com

Resultado: Cliente existe (ID: 4523)
Histórico: 3 compras anteriores, ticket médio R$ 8.500
```

**Thread 2: Cálculo Técnico (Flowise)**
```
Bitola Média = (1200*12 + 800*8) / 2000 = 10.4mm
Peso total: 2.000kg
Categoria: Pedido grande (>1 ton)
```

**Thread 3: Logística (Google Maps)**
```
Origem: Fábrica Cavicon (Guarulhos/SP)
Destino: Av. Paulista, 1000 - SP
Distância: 23km
Tempo: 35 min (horário pico)
Custo frete: R$ 280 (base + km)
```

**Thread 4: Capacidades (SQL)**
```sql
-- Consulta Grafico
SELECT * FROM producao
WHERE capacidade_disponivel >= 2000
  AND bitola_range BETWEEN 8 AND 12
  AND data >= '2025-01-27'
LIMIT 1

Resultado:
data_producao: 2025-01-29 (quarta-feira)
capacidade_livre: 3.500kg
```

```sql
-- Consulta BRERP (logística)
SELECT * FROM rotas_entrega
WHERE regiao = 'São Paulo Centro'
  AND data >= '2025-01-29'

Resultado:
proxima_rota: 2025-01-30 (quinta-feira, 14h)
espacos_disponiveis: 5
```

**9:17h - Precificação Inteligente (GPT-4)**

```
Análise do modelo:
- Cliente recorrente (3 compras) → desconto 5%
- Pedido grande (>1ton) → desconto volume 3%
- Urgência alta → sem desconto adicional
- Bitola média (10.4mm) → preço médio

Cálculo:
Base: R$ 7,80/kg × 2.000kg = R$ 15.600
Desconto cliente: -5% = R$ 780
Desconto volume: -3% = R$ 468
Subtotal materiais: R$ 14.352
Frete: R$ 280
TOTAL: R$ 14.632

Margem: 28% (saudável)
```

**9:18h - Geração de Orçamento (GPT-4)**

```markdown
ORÇAMENTO #5789

Cliente: Construtora XYZ
Contato: João Silva
Data: 27/01/2025

MATERIAIS:
- 1.200kg Aço CA-50 12mm: R$ 9.360,00
- 800kg Aço CA-60 8mm: R$ 4.992,00
Subtotal: R$ 14.352,00

LOGÍSTICA:
- Frete (23km): R$ 280,00

TOTAL: R$ 14.632,00

PRAZOS:
- Produção: 29/01/2025
- Entrega: 30/01/2025 (14h)

Condições: 30 dias
Validade: 5 dias úteis

[ACEITAR ORÇAMENTO] [NEGOCIAR]
```

**9:18h - Ações Finais**

✅ Salvo no BRERP (Status: Aguardando)
✅ PDF gerado automaticamente
✅ Email enviado para joao@construtoraxyz.com
✅ Notificação Slack: "Novo orçamento #5789 enviado"
✅ Task criada no CRM para follow-up em 24h

**Resultado:**
- ⏱️ **Tempo total: 3 minutos** (vs 35 minutos manual)
- 💰 **Precificação otimizada** (margem saudável + competitivo)
- 📅 **Datas precisas** (integrado com produção real)
- 🎯 **Cliente respondido rapidamente** (vantagem competitiva)

**Comparativo mensal (200 orçamentos):**

| Métrica | Antes | Depois | Ganho |
|---------|-------|--------|-------|
| Tempo/orçamento | 35 min | 3 min | **91% mais rápido** |
| Erros de precificação | 8% | 1% | **87% menos erros** |
| Taxa de conversão | 22% | 35% | **+59% vendas** |
| Horas time vendas | 117h | 10h | **107h economizadas** |

**ROI mensal:**
- Economia tempo: R$ 18k (107h × R$ 170/h)
- Aumento vendas: +26 contratos = R$ 380k
- Investimento ferramentas: R$ 150
- **ROI: 250:1**

---

## **Roadmap de Implementação (30 dias)**

**Semana 1: Setup Básico**
- Mapear APIs do BRERP e Grafico
- Criar conta Make + Google Maps API
- Configurar Flowise com agentes de cálculo

**Semana 2: Prototipação**
- Criar fluxo Make básico (sem salvamento)
- Testar cálculos com 10 casos reais
- Validar precificação com gerente comercial

**Semana 3: Integração Completa**
- Conectar com BRERP (criar orçamentos via API)
- Implementar envio automático de emails
- Adicionar notificações Slack

**Semana 4: Produção e Treinamento**
- Ativar para 50% dos orçamentos (piloto)
- Treinar vendedores no novo fluxo
- Monitorar taxa de erro e ajustar

**Metas 60 dias:**
- 🎯 95% dos orçamentos automatizados
- 🎯 Tempo médio <5 minutos
- 🎯 Taxa de erro <2%
- 🎯 Aumento de 40% em taxa de conversão

---

**Primeiros Passos:**
1. Documentar API do BRERP (endpoints disponíveis)
2. Exportar tabela de preços e capacidades
3. Criar conta Google Maps API
4. Fazer teste manual de 5 orçamentos para validar lógica