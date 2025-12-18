# 🧠 Detecção de Desbalanceamento em Máquinas Rotativas Usando Machine Learning

Este projeto apresenta um **pipeline completo de Machine Learning aplicado à detecção de desbalanceamento em máquinas rotativas**, utilizando sinais de vibração. O foco está em demonstrar **raciocínio técnico**, **decisões de engenharia** e **boas práticas** em problemas reais de **Manutenção Preditiva**.

O trabalho foi desenvolvido a partir do dataset **MAFAULDA (Machinery Fault Simulator)**, amplamente utilizado em pesquisas acadêmicas sobre diagnóstico de falhas mecânicas.

---

## 📊 Visão Geral do Dataset

Os dados consistem em sinais de vibração obtidos por sensores instalados em um simulador de falhas mecânicas, organizados em duas classes:

* **Normal (0)**
* **Desbalanceado (1)**

⚠️ Observa-se um **forte desbalanceamento entre classes**, cenário comum em aplicações industriais reais, onde eventos de falha são significativamente menos frequentes que a operação normal. Esse fator impacta diretamente a escolha das métricas de avaliação e das técnicas de balanceamento utilizadas.

---

## 1️⃣ Carregamento e Análise Exploratória dos Dados

Nesta etapa, os arquivos CSV de vibração são carregados e organizados para análise inicial, com o objetivo de compreender:

* A estrutura dos sinais
* A quantidade de amostras por classe
* O grau de desbalanceamento do dataset

São utilizadas visualizações simples para apoiar a análise exploratória e orientar as decisões das etapas seguintes.

---

## 2️⃣ Pré-processamento dos Sinais

### 🔹 Filtragem Passa-Banda

Os sinais de vibração brutos podem conter ruídos de baixa e alta frequência que não estão diretamente associados ao fenômeno físico do desbalanceamento. Para mitigar esse efeito, é aplicado um **filtro passa-banda Butterworth**, com os seguintes parâmetros:

* **Frequência de amostragem:** 51.200 Hz
* **Banda de passagem:** 1.000 – 20.000 Hz

Essa faixa foi escolhida por concentrar componentes relevantes associadas a excitações mecânicas típicas de máquinas rotativas.

### 🔹 Remoção de Tendência

Após a filtragem, é realizada a **remoção de tendência linear (detrend)**, reduzindo efeitos de offset e drift que podem influenciar negativamente a extração de características estatísticas.

Esses dois passos constituem os **principais métodos de pré-processamento exigidos pelo desafio**, com impacto direto na qualidade das features extraídas.

---

## 3️⃣ Extração de Características (Feature Engineering)

Como algoritmos clássicos de Machine Learning não operam diretamente sobre séries temporais longas, os sinais são transformados em um **vetor compacto de características representativas**. Para cada amostra, são extraídas **13 features**, agrupadas em três categorias:

### 📐 Estatísticas no Domínio do Tempo

* Média
* Desvio padrão
* Valor máximo
* Valor mínimo
* RMS (*Root Mean Square*)
* Pico-a-pico

Essas métricas capturam variações de amplitude e energia do sinal.

### 📊 Estatísticas Avançadas

* **Curtose:** indica impulsividade e presença de picos anômalos
* **Assimetria (Skewness):** mede desvios na distribuição estatística do sinal

Essas características são úteis para identificar alterações no comportamento vibracional causadas por desbalanceamento.

### 📡 Características no Domínio da Frequência

* Frequência dominante do espectro
* Energia espectral em três bandas:

  * Baixa (0–5 kHz)
  * Média (5–15 kHz)
  * Alta (15–25 kHz)
* Razão entre energias espectrais

A análise espectral é fundamental, pois o desbalanceamento tende a gerar **picos específicos de frequência**, tornando essas features altamente discriminativas.

---

## 4️⃣ Preparação dos Dados para Modelagem

Antes do treinamento dos modelos, são aplicadas etapas essenciais de preparação:

### 🔹 Normalização

As features são escaladas utilizando `StandardScaler`, garantindo média zero e variância unitária. Essa etapa é fundamental para evitar que variáveis com maior escala dominem o processo de aprendizado.

### 🔹 Balanceamento com SMOTE

Devido ao forte desbalanceamento do dataset, é aplicado o **SMOTE (Synthetic Minority Over-sampling Technique)** apenas no conjunto de treino, gerando amostras sintéticas da classe minoritária.

Essa estratégia reduz viés do modelo e melhora métricas como **recall** e **F1-score**, mais adequadas que a acurácia em cenários desbalanceados.

---

## 5️⃣ Treinamento dos Modelos de Machine Learning

Dois algoritmos supervisionados foram avaliados:

### 🌲 Random Forest Classifier

* Robusto a ruído
* Capaz de capturar relações não lineares
* Permite análise de importância das features

### ⚙️ XGBoost (XGBClassifier)

* Bom desempenho em datasets desbalanceados
* Alta capacidade de generalização
* Permite ajuste fino de hiperparâmetros

Os modelos são treinados utilizando divisão treino/teste e avaliados com **validação cruzada**, garantindo maior robustez estatística.

---

## 6️⃣ Avaliação e Visualização dos Resultados

O desempenho dos modelos é avaliado utilizando métricas adequadas para o problema:

* Acurácia
* F1-score
* ROC-AUC
* Relatório de classificação
* Matriz de confusão

Além disso, são geradas visualizações como:

* Curvas ROC
* Matrizes de confusão
* Comparação de métricas entre modelos

Essas análises permitem compreender não apenas **qual modelo apresenta melhor desempenho**, mas também **como e por que** esse desempenho é obtido.

---

## 7️⃣ Inferência em Novos Dados

O projeto inclui uma função dedicada à **previsão em novos sinais de vibração**, aplicando automaticamente:

1. Pré-processamento do sinal
2. Extração de features
3. Normalização
4. Predição do estado da máquina

Essa etapa demonstra a **viabilidade prática do modelo** em cenários reais, como sistemas de monitoramento contínuo e manutenção preditiva.

---

## 🧠 Conclusões

Os resultados indicam que a combinação de **processamento de sinais aliado a modelos clássicos de Machine Learning** é eficaz para a detecção de desbalanceamento em máquinas rotativas.

### Principais aprendizados:

* Features espectrais são altamente discriminativas
* O balanceamento de dados é crítico para desempenho confiável
* O Random Forest apresentou excelente interpretabilidade
* O XGBoost obteve melhor desempenho global

Este projeto demonstra uma abordagem **robusta, explicável e aplicável industrialmente**, alinhada aos princípios da **Manutenção Preditiva e da Indústria 4.0**.
