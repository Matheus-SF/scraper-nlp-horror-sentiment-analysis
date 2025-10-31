# 🎬 Análise de Sentimentos em Filmes de Terror com Auxílio de Web Scraping

<p align="center">
  <img src="assets/banner.png" alt="Banner do Projeto" width="800">
</p>

> Análise de sentimentos e ranqueamento de 100 filmes de terror de 2025 usando NLP e Web Scraping do IMDb

[![Python](https://img.shields.io/badge/Python-3.8+-blue.svg)](https://www.python.org/)
[![Selenium](https://img.shields.io/badge/Selenium-4.0+-green.svg)](https://www.selenium.dev/)
[![VADER](https://img.shields.io/badge/VADER-Sentiment-orange.svg)](https://github.com/cjhutto/vaderSentiment)
[![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-orange.svg)](https://jupyter.org/)

## Objetivo

Analisar **100 filmes de terror lançados em 2025** através de web scraping do IMDb e criar um ranking baseado em análise de sentimentos e emoções específicas do gênero (medo, suspense, atmosfera, gore, jump scares).

A lógica aplicada nesse projeto pode ser utilizada em diversos outras frentes, como analisar o sentimento geral dos clientes em relação a produtos de uma loja online por exemplo.

## Contexto

Este projeto nasceu da minha paixão por filmes de terror e pelo desejo de aplicar técnicas de Data Science em um domínio divertido e desafiador. Com a chegada de Halloween e o lançamento de dezenas de novos filmes de terror em 2025, me surgiu a pergunta: **"Quais são os filmes REALMENTE mais assustadores de 2025, segundo quem já assistiu?"**

Diferente das notas tradicionais do IMDb que medem qualidade geral, neste projeto foquei em **medir o medo**  analisando centenas de reviews para identificar quais filmes realmente deixam as pessoas assustadas ou ao menos incomodadas.

---

## 💼 O Que Este Projeto Demonstra

Este projeto é uma **demonstração prática** das minhas habilidades em:

### Web Scraping 
- Coleta de dados de sites com conteúdo dinâmico (JavaScript)
- Automação de interações complexas (cliques, scroll, espera de elementos)
- Tratamento de diferentes estruturas HTML e edge cases

### Processamento de Linguagem Natural (NLP)
- Análise de sentimentos em textos reais de usuários
- Customização de modelos pré-treinados para domínios específicos
- Extração de features semânticas (emoções, temas)

### Engenharia de Features
- Criação de métricas compostas e ponderadas
- Normalização e tratamento de diferentes escalas
- Balanceamento entre qualidade e quantidade de dados

### Resolução de Problemas
- Identificação e correção de vieses nos dados
- Debugging de código complexo (Selenium + HTML dinâmico)
- Iteração e refinamento de metodologia

### Análise de Dados
- Manipulação de DataFrames com Pandas
- Agregações, filtros e transformações complexas
- Exportação e documentação de resultados

## Destaques Técnicos

- 🕷️ **Web Scraping com Selenium**: Coleta automatizada enfrentando scroll infinito e botões dinâmicos do IMDb
- 🧠 **NLP Customizado**: Léxico VADER ajustado para o contexto de terror (palavras como "scary" são positivas!)
- 📊 **Feature Engineering**: Sistema de pontuação ponderado com fator de confiança
- 🎭 **Análise Multi-dimensional**: 5 categorias de emoções + sentimento geral

## 🏆 Top 10 Filmes Mais Assustadores (2025)

| # | Filme | Score Terror | Nota IMDb | Reviews Utilizadas |
|---|-------|--------------|-----------|---------|
| 1 | Faça Ela Voltar | 6.70 | 7.2 | 20 |
| 2 | Rosario | 6.02 | 7.7 | 20 |
| 3 | A Meia-Irmã Feia | 6.0 | 7.0 | 20 |
| 4 | A Hora do Mal | 5.94 | 7.5 | 20 |
| 5 | Animais Perigosos | 5.80 | 6.4 | 20 |

[📄 Ver ranking completo →](Resultados (Results)\ranking_final.xlsx)

## 🛠️ Tecnologias

| Categoria | Ferramentas |
|-----------|-------------|
| **Web Scraping** | Selenium, BeautifulSoup4, Requests |
| **NLP** | VADER Sentiment, TextBlob |
| **Data Science** | Pandas, NumPy |
| **Ambiente** | Jupyter Notebook |

### Execução
Execute todas as células do notebook sequencialmente. O processo completo leva ~30-40 minutos devido ao web scraping.

## 🔬 Metodologia

### 1️⃣ Coleta de Dados (Web Scraping)
```python
# Desafio: IMDb usa scroll infinito + JavaScript
# Solução: Selenium clicando em botões "Ver tudo"

driver.get(url_reviews)
botao = driver.find_element(By.XPATH, "//button[.//span[text()='Ver tudo']]")
driver.execute_script("arguments[0].click();", botao)
```

**Dados coletados por filme:**
- Título e Nota IMDb
- 20 reviews de usuários (Importante: Alguns filmes não possuíam o número mínimo de Reviews)
- URL da página

### 2️⃣ Pré-processamento

- Remoção de conteúdo não-review (créditos, trailers)
- Filtros por tamanho mínimo (50 caracteres)

### 3️⃣ Análise de Sentimentos

**Problema:** VADER padrão tratava "scary" como negativo

**Solução:** Customização do léxico para contexto de terror
```python
# Palavras de terror são POSITIVAS no contexto do gênero
palavras_terror_positivas = {
    'scary': 2.5,
    'terrifying': 3.0,
    'horrifying': 3.0,
    'creepy': 2.5,
    'disturbing': 2.5,
    'suspenseful': 2.5,
    # ... +20 palavras
}

analyzer.lexicon.update(palavras_terror_positivas)
```

### 4️⃣ Classificação de Emoções

Cada review é analisada para 5 categorias, onde busco as seguintes palavras chaves:

| Emoção | Peso | Palavras-chave |
|--------|------|----------------|
| **1. Medo Intenso** | 4 | terrifying, horrifying, disturbing |
| **2. Suspense** | 2 | suspenseful, tense, gripping |
| **3. Atmosfera** | 2 | atmospheric, eerie, haunting |
| **4. Gore** | 1 | bloody, gore, graphic |
| **5. Jump Scares** | 1 | jump scare, shocking |

### 5️⃣ Score Final

Depois de obter as classificações de emoções, montei um score do "Terror Definitivo", sempre normalizados em relação ao número de reviews.

**Fórmula:**
```python
# Score de emoções (0-10)
score_emocoes = (
    (medo_intenso / num_reviews) * 4 +
    (suspense / num_reviews) * 2 +
    (atmosfera / num_reviews) * 2 +
    (gore / num_reviews) * 1 +
    (jump_scares / num_reviews) * 1
)

# Sentimento geral normalizado (0-10)
sentimento_norm = (sentimento_medio + 1) / 2 * 10

# Combinar: 50% emoções + 50% sentimento
score_bruto = (score_emocoes * 0.5) + (sentimento_norm * 0.5)

# Fator de confiança (penaliza filmes com poucas reviews)
fator_confianca = min(num_reviews / 20, 1.0)

# Score final
score_terror = score_bruto * fator_confianca
```

**Por que esse design?**
- ✅ Emoções específicas têm mais peso que sentimento genérico
- ✅ Filmes com poucas reviews são penalizados

## 💡 Desafios e Soluções

### 1. Scraping de Conteúdo Dinâmico
**Problema:** IMDb carrega reviews via JavaScript, `requests` pegavam apenas as reviews visíveis, não chegando a 20.

**Solução:** 
- Selenium + cliques automáticos no botão "Ver tudo"
- Scroll para garantir que botão apareça
- `execute_script` para evitar elementos sobrepondo

### 2. Léxico Inadequado para Terror
**Problema:** VADER trata "scary" como sentimento negativo

**Solução:**
- Customização do léxico com 25+ palavras
- Pesos calibrados manualmente (scary=2.5, terrifying=3.0)

### 3. Bias de Quantidade
**Problema:** Filmes com 3 reviews excelentes superavam filmes com 50 reviews boas

**Solução:**
- Fator de confiança: `min(num_reviews/20, 1.0)`
- Penaliza proporcionalmente até atingir 20 reviews

### 4. URLs Inconsistentes
**Problema:** Alguns links tinham `/pt/` outros não

**Solução:**
```python
url_limpa = url.replace('/pt/title/', '/title/')
```

## 📊 Insights do Projeto

### Descobertas
- 📈 Média de reviews por filme: **~15**
- 🎯 Sentimento médio: **0.65** (positivo)
- 🏆 Score máximo alcançado: **6.0/10**
- 📉 Poucos filmes superam 5.0 (critério rigoroso!)

### Correlações Interessantes
- Nota IMDb ≠ Score Terror (filmes "artísticos" vs "assustadores")
- Mais reviews = score mais estável
- Gore tem menos impacto que atmosfera

## 🎓 Habilidades Demonstradas

| Área | Competências |
|------|--------------|
| **Web Scraping** | Selenium, tratamento de JavaScript
| **NLP** | Análise de sentimentos, customização de léxicos |
| **Python** | Pandas, list comprehensions, lambda functions |
| **Feature Engineering** | Normalização, ponderação, fator de confiança |
| **Problem Solving** | Debug de scraping, ajuste de parâmetros |


## 📁 Estrutura do Notebook
```
1. Configuração e Imports
2. Web Scraping
   2.1 Coleta de Títulos e Metadados
   2.2 Coleta de Reviews
3. Análise de Sentimentos
   3.1 Customização do Léxico VADER
   3.2 Processamento de Reviews
4. Classificação de Emoções
5. Cálculo do Score Final
6. Ranking e Visualização
7. Exportação de Resultados
```

## 📫 Contato

**[Matheus Santana Ferreira]**
- 💼 LinkedIn: [https://www.linkedin.com/in/matheus-santana-ferreira-97256b235/](https://www.linkedin.com/in/matheus-santana-ferreira-97256b235/)
- 📧 Email: matheus.k.santanaferreira@gmail.com
- Currículo: Meu currículo\Curriculo - Matheus_Santana_2025.pdf


---

🤝 **Percebeu alguma falha no meu método?** Críticas e Opiniões são sempre bem vindas!