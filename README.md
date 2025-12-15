# 📋 Relatório Técnico Detalhado – Detecção de Falhas Mecânicas (MAFAULDA)

**Autor:** Carlos Henrique Rodrigues P.

---

## 📌 Introdução

Este relatório foi elaborado para **explicar de forma clara, detalhada e justificada** todas as decisões técnicas implementadas no notebook do *Desafio Técnico – Detecção de Falhas Mecânicas*.

O objetivo do trabalho é classificar automaticamente sinais de vibração do dataset **MAFAULDA**, distinguindo entre:

* **Classe 0 – Operação Normal**
* **Classe 1 – Desbalanceamento Mecânico**

Todo o pipeline foi desenvolvido com foco em **clareza, reprodutibilidade e alinhamento com práticas industriais**.

---

## 🎯 1. Contexto do Problema e Objetivo

### 1.1 Dataset MAFAULDA

O dataset MAFAULDA é composto por sinais de vibração obtidos a partir de um **simulador de falhas mecânicas (MFS – Machinery Fault Simulator)**.

**Características principais:**

* Sinais experimentais reais (não sintéticos)
* Ambiente controlado de laboratório
* Arquivos CSV independentes
* Frequência de amostragem: **1000 Hz**
* Múltiplos canais por arquivo (foi utilizado **apenas um canal**, propositalmente)

Essa escolha torna o problema mais realista, pois em aplicações industriais muitas vezes **nem todos os sensores estão disponíveis**.

### 1.2 Objetivo do Projeto

Desenvolver um classificador binário capaz de identificar automaticamente o estado mecânico do sistema a partir de sinais de vibração, seguindo três princípios:

1. Pipeline simples e interpretável
2. Decisões técnicas justificadas fisicamente
3. Avaliação justa e reproduzível

---

## 🧩 2. Estratégia Global da Solução

O pipeline segue a abordagem clássica de *Machine Learning* aplicada a sinais temporais:

```
Carregamento → Pré-processamento → Janelamento → Extração de Features → Modelagem → Avaliação
```

**Filosofia adotada:**

* Começar simples e evoluir com base nos dados
* Priorizar métodos explicáveis (importante em contexto industrial)
* Evitar soluções excessivamente complexas para um dataset limitado

---

## 🔍 3. Carregamento e Análise Inicial dos Dados

Os arquivos CSV são carregados separadamente para cada classe (Normal e Desbalanceamento). Cada arquivo representa uma execução independente do sistema.

Uma análise inicial revelou:

* Diferença clara de **amplitude** entre classes
* Presença de **offset DC** (valor médio diferente de zero)
* Ruído de baixa frequência não relacionado à falha

Essas observações guiaram diretamente as escolhas de pré-processamento.

---

## 🔧 4. Pré-processamento dos Sinais

O pré-processamento foi tratado como uma etapa **crítica**, pois sinais de vibração reais são naturalmente ruidosos.

### 4.1 Remoção da Componente DC

```python
from scipy.signal import detrend

def remover_dc(sinal):
    return detrend(sinal, type='constant')
```

**Motivação:**

* Sensores reais frequentemente apresentam deslocamento do zero
* O offset DC não carrega informação sobre falhas
* Pode distorcer métricas como RMS e PSD

A função `detrend` é numericamente estável e amplamente utilizada em análise de vibração.

---

### 4.2 Filtragem Passa-Banda (5–200 Hz)

```python
from scipy.signal import butter, filtfilt

def filtro_butter(sinal, fs, low=5, high=200, ordem=4):
    nyq = fs / 2
    b, a = butter(ordem, [low/nyq, high/nyq], btype='band')
    return filtfilt(b, a, sinal)
```

**Justificativa técnica:**

* Frequências abaixo de 5 Hz estão associadas a movimentos estruturais e instalação
* Frequências acima de 200 Hz apresentaram apenas ruído
* O desbalanceamento mecânico se manifesta principalmente em baixas e médias frequências

O filtro Butterworth foi escolhido por possuir resposta plana na banda passante.

---

### 4.3 Normalização – RobustScaler

```python
from sklearn.preprocessing import RobustScaler
```

**Por que RobustScaler?**

* Utiliza mediana e intervalo interquartil
* Muito menos sensível a outliers
* Ideal para sinais experimentais com picos ocasionais

Essa escolha é particularmente importante porque falhas mecânicas podem gerar **valores extremos reais**, que não devem ser removidos.

---

## 🪟 5. Janelamento dos Sinais

Como cada arquivo possui milhares de amostras, foi aplicada segmentação em janelas deslizantes:

```python
def dividir_janelas(sinal, tamanho=2048, overlap=0.5):
    passo = int(tamanho * (1 - overlap))
    return [sinal[i:i+tamanho] for i in range(0, len(sinal)-tamanho, passo)]
```

**Parâmetros escolhidos:**

* Janela: 2048 amostras (~2 segundos)
* Overlap: 50%

**Benefícios:**

* Aumento significativo do número de amostras
* Preservação da continuidade temporal
* Melhor generalização dos modelos

---

## 🧮 6. Extração de Features

Foram escolhidas features **simples, interpretáveis e fisicamente significativas**.

### 6.1 Features no Domínio do Tempo

* **RMS:** energia do sinal
* **Curtose:** sensibilidade a impactos
* **Skewness:** assimetria da vibração

Essas métricas são amplamente utilizadas em manutenção preditiva.

### 6.2 Features no Domínio da Frequência

A densidade espectral de potência foi calculada usando o método de Welch:

```python
from scipy.signal import welch
```

Features extraídas:

* Frequência dominante
* Potência total

Apesar de calculadas, essas features mostraram menor poder discriminativo.

---

## ⚖️ 7. Tratamento do Desbalanceamento das Classes

Após o janelamento, ainda havia desbalanceamento entre as classes.

Foi utilizado **SMOTE apenas no conjunto de treino**:

```python
from imblearn.over_sampling import SMOTE
```

Essa abordagem evita *data leakage* e melhora a capacidade de generalização.

---

## 🤖 8. Modelagem de Machine Learning

### 8.1 Random Forest

* Robusto a ruído
* Interpretável
* Excelente baseline industrial

### 8.2 XGBoost

* Gradient boosting
* Regularização nativa
* Alta performance em dados tabulares

Ambos foram treinados sob as mesmas condições para garantir comparação justa.

---

## 📊 9. Avaliação dos Modelos

### Métricas utilizadas:

* **Acurácia** (visão geral)
* **F1-score** (principal métrica)
* **ROC-AUC** (capacidade discriminativa)

A validação cruzada estratificada foi utilizada durante o desenvolvimento.

---

## 📈 10. Resultados Obtidos

* Random Forest apresentou melhor equilíbrio entre precisão e recall
* F1-score em torno de **0.74**, considerado adequado dada a dificuldade do problema
* ROC-AUC próximo de **0.55**, indicando separação limitada entre classes

Isso reforça que o problema é **intrinsecamente desafiador** com features simples.

---

## 📝 11. Conclusões 

Este projeto demonstra:

* Capacidade de estruturar um pipeline completo de ML
* Tomada de decisão baseada em dados e domínio físico
* Preocupação com boas práticas (evitar data leakage, validação justa)
* Clareza na comunicação técnica

A solução não busca apenas maximizar métricas, mas **resolver o problema de forma correta, explicável e reproduzível**, como exigido em ambientes industriais reais.

---

## 🚀 12. Próximos Passos Sugeridos

* Inclusão de múltiplos canais
* Features tempo-frequência (wavelets)
* Modelos deep learning (CNN 1D)
* Explainable AI (SHAP)
* Pipeline em tempo real


