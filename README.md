# 📚 Predição de Abandono Escolar — Censo Escolar 2025

Projeto de Machine Learning desenvolvido com os **microdados do Censo Escolar 2025**, com o objetivo de classificar escolas quanto à ocorrência de abandono no Ensino Fundamental.

## 🎯 Objetivo

Identificar se uma escola apresentou ou não taxa de abandono:

- `0` → sem abandono
- `1` → com abandono

## 📊 Dados

Foram utilizadas informações do Censo Escolar 2025 e das Taxas de Rendimento Escolar do INEP.

Principais variáveis utilizadas:

- Região
- Rede de ensino
- Dependência administrativa
- Localização
- Localização diferenciada

A taxa de abandono do Ensino Fundamental foi utilizada para definir a variável-alvo.

## 🔎 Etapas

- Tratamento e integração dos dados
- Análise exploratória (EDA)
- Preparação das variáveis
- One-Hot Encoding
- Treino e teste
- Classificação com Linear SVM
- Avaliação do modelo

## 🤖 Modelo

Foi utilizado um **Linear SVM (`LinearSVC`)** com `class_weight='balanced'` para lidar com o desbalanceamento entre as classes.

As principais métricas utilizadas foram:

- Accuracy
- Precision
- Recall
- F1-score
- Matriz de confusão

## 📌 Resultado

O modelo apresentou aproximadamente:

**Accuracy:** 62,6%  
**Recall — classe abandono:** 68%  
**F1-score — classe abandono:** 47%

A análise mostrou a importância de considerar o desbalanceamento das classes, já que um modelo de referência atingiu 76% de acurácia simplesmente classificando todas as escolas como pertencentes à classe majoritária.

## 🛠️ Tecnologias

Python • Pandas • NumPy • Matplotlib • Scikit-learn • Google Colab

## 📚 Fonte

Dados provenientes dos **Microdados do Censo Escolar 2025** e das **Taxas de Rendimento Escolar 2025**, disponibilizados pelo INEP.
