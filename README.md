#  Desafio Técnico – Detecção de Falhas Mecânicas

## 1. Contexto e Objetivo

Este repositório apresenta uma solução desenvolvida para o **Desafio Técnico de Detecção de Falhas Mecânicas**, utilizando o dataset **MAFAULDA (Machinery Fault Simulator)**.  

 **Objetivo principal:** Demonstrar a aplicação de técnicas de **Ciência de Dados e Machine Learning** para identificar falhas mecânicas a partir de séries temporais multivariadas provenientes de sensores.

O problema abordado consiste na **classificação de duas condições operacionais**:

-  Operação normal do sistema  
-  Falha por desbalanceamento (*Imbalance*)

A solução foi estruturada para refletir um fluxo típico de projetos corporativos de **manutenção preditiva**, cobrindo desde o tratamento dos dados até a avaliação comparativa de modelos.

---

## 2. Base de Dados

O dataset **MAFAULDA** contém sinais de sensores coletados em um simulador de falhas mecânicas (MFS). Cada conjunto de dados representa uma condição operacional específica do equipamento.

**Principais características:**
- Séries temporais multivariadas  
- Dados oriundos de sensores mecânicos  
- Classes bem definidas, adequadas para problemas de classificação supervisionada  

 Este tipo de base é amplamente utilizado em contextos industriais para soluções de **monitoramento de condição** e **manutenção preditiva**.

---

![banner](/img/papilane.png)

## 3. Estratégia de Pré-processamento

Para preparar os dados e maximizar o desempenho dos algoritmos, foram aplicadas diferentes técnicas de pré-processamento.

### 3.1  Extração de Features Estatísticas
Transformação dos sinais temporais em *features* representativas, como:
- Média  
- Desvio padrão  
- Valores máximo e mínimo  
- Amplitude  
- RMS (*Root Mean Square*)  

**Impacto esperado:**
- Redução de ruído  
- Simplificação do modelo  
- Melhoria da capacidade de generalização  

---

### 3.2  Escalonamento com RobustScaler
Aplicação do **RobustScaler** para normalização dos dados.  

**Justificativa:**  
Sinais mecânicos frequentemente apresentam picos e outliers. O RobustScaler é menos sensível a valores extremos, garantindo maior estabilidade no treinamento.  

**Impacto esperado:**
- Treinamento mais consistente  
- Melhor convergência dos algoritmos  

---

### 3.3  Balanceamento de Classes com SMOTE
Uso da técnica **SMOTE (Synthetic Minority Over-sampling Technique)** para lidar com desbalanceamento entre classes.  

**Impacto esperado:**
- Redução de viés em favor da classe majoritária  
- Melhoria em métricas como *Recall* e *F1-score*  

---

## 4. Modelagem e Algoritmos Utilizados

Foram selecionados dois algoritmos amplamente utilizados em ambientes corporativos:

### 4.1  Random Forest
Modelo baseado em *ensemble learning*, combinando múltiplas árvores de decisão.  

**Características:**
- Robustez a ruído  
- Boa interpretabilidade  
- Baixa necessidade de ajuste fino  

**Motivação:**  
Escolha comum em projetos industriais devido à sua estabilidade e confiabilidade.  

---

### 4.2  XGBoost
Algoritmo de *gradient boosting* reconhecido por alto desempenho.  

**Características:**
- Capacidade de capturar padrões complexos  
- Excelente performance em dados estruturados  

**Motivação:**  
Adequado para cenários onde o foco é maximizar a performance preditiva.  

---

## 5. Treinamento e Validação

Boas práticas aplicadas no processo de modelagem:

- Separação estratificada em treino e teste  
- Uso de **validação cruzada estratificada (Stratified K-Fold)**  
- Construção de um **pipeline** integrando escalonamento, balanceamento e modelo  

 Essa abordagem reduz risco de *data leakage* e assegura avaliação consistente.

---

## 6. Avaliação de Desempenho

Os modelos foram avaliados com métricas relevantes para classificação binária:

- **Acurácia** → visão geral do desempenho  
- **F1-score** → equilíbrio entre precisão e recall  
- **ROC AUC** → capacidade de separação entre classes  

###  Análise Comparativa
- **Random Forest** → desempenho consistente e interpretável  
- **XGBoost** → métricas superiores (F1-score e ROC AUC), maior capacidade discriminativa  

👉 A escolha depende do contexto do negócio: interpretabilidade vs. performance.

---

## 7. Conclusão

A solução atende integralmente aos requisitos do desafio técnico e demonstra a aplicação prática de **Machine Learning em contexto industrial**.

**Principais pontos:**
- Uso de múltiplas técnicas de pré-processamento  
- Comparação estruturada entre dois algoritmos consagrados  
- Avaliação baseada em métricas relevantes para o negócio  

 O pipeline é escalável e pode ser adaptado para novos tipos de falhas, sensores adicionais ou integração em sistemas de monitoramento.

---

## 8. Tecnologias Utilizadas

-  Python  
-  Pandas & NumPy  
-  Scikit-learn  
-  Imbalanced-learn (SMOTE)  
-  XGBoost  
-  Matplotlib & Seaborn  

---

AUTOR: Carlos Henrique Rodrigues Paixão
