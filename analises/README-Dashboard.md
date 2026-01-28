# Dashboard AI Canvas - StartSe AI Journey

## 📋 Descrição

Dashboard interativo para visualização e análise dos AI Canvas do programa StartSe AI Journey. Permite pesquisar, filtrar, visualizar PDFs e acessar análises técnicas de forma intuitiva.

## 🚀 Como Usar

### 1. Gerar/Atualizar Dados

Execute o script Python para escanear a pasta e gerar o arquivo JSON com os dados:

```bash
python generate-canvas-data.py
```

Isso irá:
- Escanear todas as pastas em `D:\AI Canvas`
- Identificar os PDFs de AI Canvas
- Verificar quais têm análises técnicas
- Gerar o arquivo `canvas-data.json`

### 2. Abrir o Dashboard

Abra o arquivo `dashboard-completo.html` no navegador:
- Clique duas vezes no arquivo, ou
- Arraste para o navegador, ou
- Abra via navegador (Ctrl+O)

## ✨ Funcionalidades

### 🔍 Pesquisa
- Campo de busca por nome
- Pesquisa em tempo real
- Case insensitive

### 🎯 Filtros
- **Todos**: Mostra todos os canvas
- **Analisados**: Apenas com análise técnica completa (verde)
- **Pendentes**: Aguardando análise (laranja)

### 📊 Estatísticas
- Total de canvas
- Quantidade analisada
- Quantidade pendente
- Barra de progresso visual

### 📄 Visualização
- **Ver PDF**: Abre o AI Canvas em PDF
- **Ver Análise**: Mostra a análise técnica formatada (quando disponível)
- Interface com modal para melhor visualização

### 🎨 Interface
- Design moderno com efeitos glass morphism
- Gradiente animado de fundo
- Indicadores visuais de status:
  - ✅ Verde = Analisado
  - ⏳ Laranja = Pendente
- Responsivo e otimizado

## 📁 Estrutura de Arquivos

```
D:\AI Canvas\
├── dashboard-completo.html          # Dashboard principal
├── generate-canvas-data.py          # Script para gerar dados
├── canvas-data.json                 # Dados gerados (JSON)
├── Adriana Araujo/
│   ├── Adriana Araujo - ai-canvas.pdf
│   └── Análise Técnica - Adriana Araujo.md
├── [Outras pastas de pessoas...]
```

## 🔄 Atualização dos Dados

Sempre que:
- Adicionar novos AI Canvas
- Criar novas análises técnicas
- Modificar a estrutura de pastas

Execute novamente:
```bash
python generate-canvas-data.py
```

E recarregue o dashboard no navegador (F5).

## 🛠️ Requisitos

- Python 3.x (para gerar os dados)
- Navegador web moderno (Chrome, Firefox, Edge, Safari)
- Estrutura de pastas conforme padrão:
  - `[Nome da Pessoa]/`
    - `[Nome da Pessoa] - ai-canvas.pdf`
    - `Análise Técnica - [Nome da Pessoa].md` (opcional)

## 💡 Dicas de Uso

1. **Primeira vez**: Execute o script Python antes de abrir o HTML
2. **Pesquisa rápida**: Use o campo de busca para encontrar pessoas específicas
3. **Filtros**: Use os botões de filtro para ver apenas analisados ou pendentes
4. **PDFs**: Clique em "Abrir PDF" para visualizar no leitor padrão do sistema
5. **Análises**: Clique em "Ver Análise" para ler a análise técnica formatada

## 🎯 Melhorias Implementadas

✅ Leitura automática dos arquivos via FileSystem
✅ Campo de pesquisa em tempo real
✅ Filtros por status (Todos/Analisados/Pendentes)
✅ Visualização de PDFs
✅ Visualização de análises técnicas formatadas (Markdown)
✅ Indicadores visuais de status
✅ Interface responsiva e moderna
✅ Contadores e estatísticas em tempo real
✅ Barra de progresso animada
✅ Modais para melhor experiência

## 📝 Notas

- O dashboard carrega os dados do arquivo `canvas-data.json`
- As análises são renderizadas com suporte completo a Markdown
- Os PDFs são abertos no visualizador padrão do sistema
- A interface é totalmente responsiva e funciona em diferentes tamanhos de tela

## 🆘 Troubleshooting

**Problema**: Dashboard não carrega dados
**Solução**: Execute `python generate-canvas-data.py` para gerar o JSON

**Problema**: PDF não abre
**Solução**: Verifique se o caminho do arquivo está correto e se você tem um leitor de PDF instalado

**Problema**: Análise não aparece
**Solução**: Verifique se o arquivo `.md` existe na pasta da pessoa e se o nome está correto

---

🎓 **StartSe AI Journey** | Dashboard AI Canvas | 2025
