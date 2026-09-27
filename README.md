# 🎓 Previsão de Evasão Escolar com SVM

Modelo de classificação para sinalizar escolas com risco de abandono escolar no Ensino Fundamental, a partir de dados públicos do Censo Escolar do INEP.

---

## 📌 Objetivo

Prever o risco de abandono escolar das escolas brasileiras a partir de características observadas no Censo Escolar de 2025, tratando o problema como uma **classificação binária**: a escola teve (ou não) uma taxa de abandono relevante no Ensino Fundamental.

A ideia inicial era tratar o abandono como uma taxa contínua (regressão), mas o problema foi reformulado como classificação binária, já que a distribuição da taxa é fortemente concentrada em 0% e o objetivo prático é sinalizar risco, não estimar o percentual exato.

---

## 🗂️ Fontes de Dados

- [Microdados do Censo Escolar 2025](https://www.gov.br/inep/pt-br/acesso-a-informacao/dados-abertos/microdados/censo-escolar) — características das escolas
- [Indicadores de Taxa de Rendimento Escolar 2025](https://www.gov.br/inep/pt-br/acesso-a-informacao/dados-abertos/indicadores-educacionais/taxas-de-rendimento-escolar) — taxas de abandono, aprovação e reprovação (`3_CAT_FUN`)
- [Pasta com arquivos utilizados (datasets)](https://drive.google.com/drive/folders/1eq5qc70Tz7kmm-NT4DrFt7iE9otFYSXv?usp=drive_link) — contém a pasta com a estrutura necessária especificada ao carregar os dados

As duas bases são cruzadas pela chave `CO_ENTIDADE` (código identificador da escola).

---

## 🎯 Definição do Target

```
y = 0  →  taxa de abandono ≤ 2%
y = 1  →  taxa de abandono > 2%
```

Taxas muito baixas (1 ou 2 alunos isolados) podem refletir casos pontuais ou pequenas imprecisões de registro, não necessariamente um padrão relevante de evasão, por isso o limiar de 2%, em vez de qualquer valor acima de 0%.

Com esse critério, a classe positiva (houve abandono relevante) representa **~7%** dos dados, um cenário fortemente desbalanceado.

---

## 🧩 Features Utilizadas

| Coluna | Descrição |
|---|---|
| `CO_REGIAO` | Região geográfica |
| `CO_REDE` | Rede de ensino (pública/privada) |
| `TP_DEPENDENCIA` | Dependência administrativa (federal/estadual/municipal/privada) |
| `TP_LOCALIZACAO` | Localização (urbana/rural) |
| `TP_LOCALIZACAO_DIFERENCIADA` | Localização diferenciada (assentamento, terra indígena, quilombola, etc.) |
| `IN_EXAME_SELECAO` | Escola aplica exame de seleção para ingresso |
| `TP_PROPOSTA_PEDAGOGICA` | Projeto pedagógico atualizado nos últimos 12 meses |
| `IN_ORGAO_CONSELHO_ESCOLAR` | Escola possui Conselho Escolar |

> Indicadores de infraestrutura (água, energia, esgoto, banheiro, refeitório) foram testados e descartados: mostraram-se redundantes com `CO_REDE`/`TP_DEPENDENCIA` e não trouxeram ganho de desempenho.

Todas as variáveis são categóricas e foram transformadas via **One-Hot Encoding**.

---

## 🤖 Modelagem

**Algoritmo:** SVM Linear (`LinearSVC`), com `class_weight='balanced'` para compensar o desbalanceamento das classes.

**Processo:**
1. Modelo base (SVM linear simples) → identifica o problema de desbalanceamento
2. Ajuste com `class_weight='balanced'`
3. Comparação com baseline (`DummyClassifier`)
4. Otimização do hiperparâmetro `C` via `GridSearchCV` (validação cruzada, 5 folds, otimizando F1)

**Split:** 80% treino / 20% teste, com estratificação pela classe alvo.

---

## 📊 Resultados

| Modelo | Acurácia | Recall (classe 1) | Precisão (classe 1) |
|---|---|---|---|
| Baseline (Dummy) | 0.93 | 0.00 | — |
| SVM final (C=0.01) | 0.61 | 0.79 | 0.13 |

**Por que o modelo com menor acurácia é o melhor aqui:** o baseline só acerta tanto porque a maioria das escolas realmente não tem abandono relevante, mas ele nunca identifica nenhum caso de risco. O SVM sacrifica acurácia geral para identificar 79% das escolas com abandono relevante, o que é o objetivo real do projeto: **triagem de risco**, não acurácia bruta.

O trade-off é a baixa precisão (13%), bastante falso positivo. Em um cenário de triagem, isso é aceitável: o custo de investigar uma escola que acaba não estando em risco tende a ser menor do que deixar uma escola em risco real passar despercebida.

---

## 🛠️ Tecnologias

- Python
- pandas / numpy
- scikit-learn (`LinearSVC`, `GridSearchCV`, `ColumnTransformer`, `OneHotEncoder`)
- matplotlib
- Google Colab + Google Drive (armazenamento e execução)

---

## ▶️ Como Executar

1. Abra o notebook no Google Colab
2. Monte o Google Drive quando solicitado (`drive.mount`)
3. Ajuste os caminhos dos arquivos de dados (Censo Escolar e Taxas de Rendimento) para a sua estrutura de pastas
4. Execute todas as células em ordem (`Ambiente de execução > Executar tudo`)

---

## 📈 Próximos Passos

- Testar outras features que capturem dimensões ainda não exploradas (ex: porte da escola)
- Avaliar modelos não-lineares para comparação (ex: Random Forest)
- Ajustar o limiar de decisão do SVM para explorar outros pontos do trade-off precisão/recall
