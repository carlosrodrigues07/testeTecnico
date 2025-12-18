# 🧠 Detecção de Desbalanceamento em Máquinas Rotativas Usando Machine Learning

Este notebook apresenta um **pipeline completo de Machine Learning aplicado à detecção de desbalanceamento em máquinas rotativas**, utilizando sinais de vibração. O foco é demonstrar **raciocínio técnico**, **decisões de engenharia** e **boas práticas** em problemas reais de Manutenção Preditiva.

---

## 1️⃣ Carregamento e Análise Exploratória dos Dados

Nesta etapa, os dados de vibração são carregados a partir de arquivos CSV, organizados em duas classes:

* **Normal (0)**
* **Desbalanceado (1)**

O objetivo inicial é compreender:

* A estrutura dos dados
* A quantidade de amostras por classe
* O grau de desbalanceamento do dataset

⚠️ Observa-se um **forte desequilíbrio entre classes**, cenário comum em aplicações industriais reais, onde falhas são menos frequentes que a operação normal. Esse fator impacta diretamente a escolha de métricas e técnicas de balanceamento utilizadas posteriormente.

---

## 2️⃣ Pré-processamento dos Sinais

### 🔹 Filtragem Passa-Banda

Os sinais de vibração brutos podem conter ruídos de baixa e alta frequência que não estão relacionados ao fenômeno físico do desbalanceamento. Para mitigar esse efeito, é aplicado um **filtro passa-banda Butterworth** com os seguintes parâmetros:

* Frequência de amostragem: **51.200 Hz**
* Banda de passagem: **1.000 – 20.000 Hz**

Essa faixa foi escolhida por concentrar componentes relevantes associadas a excitações mecânicas típicas de máquinas rotativas.

### 🔹 Remoção de Tendência

Após a filtragem, é realizada a **remoção de tendência linear** do sinal, reduzindo efeitos de offset e drift que podem influenciar negativamente a extração de características estatísticas.

---

## 3️⃣ Extração de Características (Feature Engineering)

Como algoritmos tradicionais de ML não operam diretamente sobre séries temporais longas, os sinais são transformados em um **vetor de características representativas**. Para cada amostra, são extraídas **13 features**, divididas em três grupos:

### 📐 Estatísticas no Domínio do Tempo

* Média
* Desvio padrão
* Valor máximo
* Valor mínimo
* RMS (*Root Mean Square*)
* Pico-a-pico

Essas métricas capturam variações de amplitude e energia do sinal.

### 📊 Estatísticas Avançadas

* **Curtose**: indica impulsividade e presença de picos anômalos
* **Assimetria (Skewness)**: mede desvios na distribuição do sinal

Essas características são úteis para identificar alterações no comportamento vibracional causadas por desbalanceamento.

### 📡 Características no Domínio da Frequência

* Frequência dominante do espectro
* Energia espectral em três bandas:

  * Baixa (0–5 kHz)
  * Média (5–15 kHz)
  * Alta (15–25 kHz)
* Razão entre energias

A análise espectral é fundamental, pois o desbalanceamento tende a gerar **picos específicos de frequência**, tornando essas features altamente discriminativas.

---

## 4️⃣ Preparação dos Dados para Modelagem

Antes do treinamento dos modelos, os dados passam por etapas essenciais:

### 🔹 Normalização

As features são escaladas utilizando `StandardScaler`, garantindo que todas tenham média zero e variância unitária — especialmente importante para algoritmos sensíveis à escala, como SVM.

### 🔹 Balanceamento com SMOTE

Devido ao forte desbalanceamento do dataset, é aplicado o **SMOTE (Synthetic Minority Over-sampling Technique)**, que gera amostras sintéticas da classe minoritária.

Essa abordagem evita viés do modelo em favor da classe majoritária e melhora métricas como **recall** e **F1-score**, mais adequadas que a acurácia em cenários desbalanceados.

---

## 5️⃣ Treinamento dos Modelos de Machine Learning

Dois algoritmos supervisionados foram avaliados:

### 🌲 Random Forest Classifier

* Robusto a ruído
* Capaz de capturar relações não lineares
* Permite análise de importância das features

### ⚙️ XGBoost (XGBClassifier)

* Robusto para dados desbalanceados
* Alta performance em classificação e regressão
* Permite ajuste fino de hiperparâmetros para melhor generalização

Os modelos são treinados utilizando divisão treino/teste e avaliados com validação cruzada para maior robustez.


---

## 6️⃣ Avaliação e Visualização dos Resultados

O desempenho dos modelos é avaliado utilizando métricas apropriadas para datasets desbalanceados:

* Acurácia
* F1-score
* ROC-AUC
* Relatório de classificação
* Matriz de confusão

Além disso, são geradas visualizações como:

* Curvas ROC
* Matrizes de confusão
* Comparação de métricas entre modelos

Essas análises permitem compreender não apenas **qual modelo performa melhor**, mas também **como e por quê**.

---

## 7️⃣ Função para Previsão em Novos Dados

Por fim, o notebook inclui uma função dedicada à **inferência em novos sinais de vibração**, aplicando automaticamente:

1. Pré-processamento do sinal
2. Extração de features
3. Normalização
4. Predição do estado da máquina

Essa etapa demonstra a **viabilidade prática do modelo em cenários reais**, como sistemas de monitoramento contínuo.

---

## 🧠 Conclusões

Os resultados indicam que a combinação de **processamento de sinais + Machine Learning clássico** é eficaz para detecção de desbalanceamento.

### Principais aprendizados:

* Features espectrais são altamente discriminativas
* Balanceamento de dados é crítico para desempenho confiável
* Random Forest apresentou excelente interpretabilidade

Este projeto demonstra uma abordagem **robusta, explicável e aplicável industrialmente**, alinhada aos princípios da **Manutenção Preditiva e Indústria 4.0**.
