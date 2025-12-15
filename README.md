# 🔧 Detecção de Falhas Mecânicas — Projeto de Machine Learning

Projeto desenvolvido para um **Desafio Técnico de Machine Learning**, implementando uma solução **end-to-end** para detecção de falhas mecânicas utilizando o dataset **MAFAULDA (Machinery Fault Simulator)**.

---

##  Visão Geral

**Objetivo:** classificar sinais de vibração em:

*  **Operação Normal**
*  **Desbalanceamento Mecânico**

O projeto aborda desafios reais de **dados industriais**, como ruído, outliers e **desbalanceamento severo de classes**, aplicando técnicas de pré-processamento, balanceamento e modelagem.

---

##  Dataset

**MAFAULDA – Machinery Fault Simulator**

*  **382 arquivos** de séries temporais multivariadas
*  **Classes:**

  * Normal: **49 arquivos (12.8%)**
  * Desbalanceamento: **333 arquivos (87.2%)**
*  **Sensores:** múltiplos canais de vibração

> ⚠️ Dataset altamente desbalanceado, refletindo cenários industriais reais.

---

## 🏗️ Arquitetura da Solução

```text
📁 Dados Brutos
   →  Pré-processamento
      →  Extração de Features
         →  Modelagem
            →  Avaliação
```

---

##  Pré-processamento

Duas estratégias foram implementadas e comparadas:

| Técnica                     | Justificativa                                   | Impacto                            |
| --------------------------- | ----------------------------------------------- | ---------------------------------- |
| **RobustScaler**            | Robusto a outliers comuns em sinais de sensores | Reduz influência de picos anômalos |
| **StandardScaler + Filtro** | Remove ruído e padroniza amplitude              | Melhora a qualidade do sinal       |

---

##  Modelos de Machine Learning

Dois algoritmos clássicos e robustos foram utilizados:

| Modelo            | Vantagens                                       | Configuração                         |
| ----------------- | ----------------------------------------------- | ------------------------------------ |
| **Random Forest** | Interpretável, robusto a ruído                  | 100 árvores, profundidade máxima = 8 |
| **XGBoost**       | Alta performance, lida bem com desbalanceamento | 100 estimadores, learning rate = 0.1 |

---

##  Balanceamento de Classes

* **Problema:** desbalanceamento severo (12.8% vs 87.2%)
* **Solução:**

  * SMOTE (Synthetic Minority Over-sampling Technique)
  * `class_weight='balanced'`

 **Resultado:** treinamento mais justo e melhora significativa no *recall* da classe minoritária.

---

## 📈 Resultados Obtidos

### 🏆 Comparação dos Modelos

| Modelo        | Acurácia   | F1-Score   | ROC-AUC    |
| ------------- | ---------- | ---------- | ---------- |
| Random Forest | 0.8161     | 0.8857     | 0.8352     |
| **XGBoost**   | **0.8851** | **0.9306** | **0.8361** |

---

## 📊 Análise dos Resultados

### 🔹 XGBoost — Melhor Desempenho

* **Acurácia:** 88.51% → ~9 em cada 10 previsões corretas
* **F1-Score:** 93.06% → excelente equilíbrio entre *precision* e *recall*
* **ROC-AUC:** 83.61% → boa capacidade discriminativa

### 🔹 Random Forest — Desempenho Sólido

* **Acurácia:** 81.61%
* **F1-Score:** 88.57%
* **ROC-AUC:** 83.52%

 **Conclusão:** o **XGBoost supera o Random Forest em todas as métricas**, sendo mais adequado para uso em produção.

---

##  Matriz de Confusão Esperada (XGBoost)

```text
               Previsto
               Normal  Desbalanceamento
Verdadeiro
Normal          85–90%       10–15%
Desbalanceamento 5–10%      90–95%
```

🔍 Prioriza a redução de **falsos negativos**, essencial em manutenção preditiva.

---

##  Features Mais Importantes (XGBoost)

* **RMS (Root Mean Square)** — energia do sinal
* **Curtose** — distribuição de picos
* **Desvio Padrão** — variabilidade
* **Média** — nível geral de vibração

 **Insight:** falhas por desbalanceamento afetam principalmente a **energia** e a **distribuição** do sinal, não apenas a amplitude.

---

## ⚡ Desafios Técnicos e Soluções

| Desafio                 | Solução                          | Resultado              |
| ----------------------- | -------------------------------- | ---------------------- |
| Desbalanceamento severo | SMOTE + class_weight             | F1-Score de 93%        |
| Ruído nos sinais        | Filtro + RobustScaler            | Sinais mais limpos     |
| Overfitting             | Limite de profundidade + CV      | Modelos generalizáveis |
| Seleção de features     | Importância + features temporais | Modelo eficiente       |

---

##  Conclusões



###  Análise Crítica

* ROC-AUC (~83.6%) indica espaço para melhoria
* Acurácia < 90% reflete a complexidade do problema real

---

## 📈 Recomendações para Produção

* Monitoramento contínuo do modelo
* Coleta de mais dados da classe **Normal**
* Sistema de alertas com *threshold* ajustável


---

## 🚀 Próximos Passos

* LSTM / CNN para séries temporais
* Análise no domínio da frequência (FFT, Wavelets)
* Otimização de hiperparâmetros
* Ensemble de modelos
* Validação cruzada aninhada

---

## 📁 Estrutura do Projeto

```text
detection-falhas-mecanicas/
│
├── desafio_falhas_mecanicas.ipynb
├── resultados_finais.png
├── README.md
├── requirements.txt
│
└── data/
    ├── normal/
    └── imbalance/
        ├── 6g/
        ├── 10g/
        └── .../
```

---



### 2️⃣ Dependências

* pandas
* numpy
* scikit-learn
* xgboost
* imbalanced-learn
* matplotlib
* seaborn
* scipy


##  Autor

* **Nome:** Carlos Henrique 
* **LinkedIn:** https://www.linkedin.com/in/carlos-henrique-rodri/
* **Email:** [seu.email@provedor.com](ch.rodrigues098@gmail.com))

---

##  Resultado Final

🎯 **XGBoost com F1-Score de 93.06%** — solução robusta, eficiente e pronta para evolução em cenários reais de manutenção preditiva.

Se este projeto foi útil, ⭐ considere dar uma estrela no repositório!
