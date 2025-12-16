# 📌 Detecção de Falhas Mecânicas – MAFAULDA

Este projeto foi desenvolvido como **desafio técnico** para classificação de falhas mecânicas utilizando o banco de dados **MAFAULDA (Machinery Fault Simulator)**, com foco na detecção de **desbalanceamento** a partir de sinais de vibração.

---

## 1. Objetivo do Projeto

O objetivo é construir um pipeline completo de **Processamento de Sinais + Machine Learning** capaz de distinguir entre:

- **Classe 0 – Operação Normal**
- **Classe 1 – Desbalanceamento Mecânico**

utilizando séries temporais multivariadas de vibração adquiridas em alta frequência (51.2 kHz).

---

## 2. Descrição do Dataset (MAFAULDA)

- Fonte: MAFAULDA – Machinery Fault Simulator
- Tipo de dados: sinais de vibração (CSV)
- Frequência de amostragem: **51.200 Hz**
- Falhas utilizadas neste trabalho:
  - Operação normal
  - Desbalanceamento com massas de 25g, 30g e 35g

### Distribuição inicial dos arquivos
- Normal: 49 arquivos
- Desbalanceamento: 139 arquivos

Essa distribuição reflete um **cenário realista de desbalanceamento de classes**, comum em aplicações industriais.

---

## 3. Estratégia Geral do Pipeline

O pipeline foi estruturado da seguinte forma:

1. Carregamento dos sinais
2. Pré-processamento dos sinais
3. Segmentação em janelas
4. Extração de features (tempo + frequência)
5. Balanceamento das classes
6. Divisão treino/validação por **grupo (arquivo)**
7. Treinamento de modelos
8. Avaliação com métricas robustas

Essa abordagem garante **reprodutibilidade**, **controle de vazamento de dados** e **robustez estatística**.

---

## 4. Pré-processamento dos Sinais

### 4.1 Remoção de tendência (Detrending)

A função `detrend` foi utilizada para remover componentes DC e tendências de baixa frequência que não carregam informação discriminativa para falhas mecânicas.

**Justificativa:**
> Tendências artificiais podem distorcer métricas estatísticas e espectrais, prejudicando a análise de vibração.

### 4.2 Normalização (Z-score)

Após o detrending, os sinais foram normalizados:

\[ x_{norm} = \frac{x - \mu}{\sigma} \]

**Justificativa:**
> Normalização garante comparabilidade entre sinais adquiridos em condições levemente diferentes e melhora a estabilidade do treinamento dos modelos.

---

## 5. Segmentação em Janelas

Os sinais contínuos foram divididos em janelas:

- Tamanho da janela: **2048 amostras**
- Overlap: **50%**

**Justificativa técnica:**
- Janelas curtas permitem capturar fenômenos locais
- Overlap aumenta a quantidade de amostras sem perder continuidade temporal
- 2048 pontos oferecem bom compromisso entre resolução temporal e espectral

---

## 6. Extração de Features

Foram extraídas **features híbridas**:

### 6.1 Domínio do Tempo

- RMS (energia do sinal)
- Curtose (impulsividade)
- Assimetria (skewness)

**Justificativa:**
> Falhas mecânicas alteram a distribuição estatística do sinal, especialmente em termos de energia e impulsividade.

### 6.2 Domínio da Frequência (FFT)

- Média da magnitude espectral
- Desvio padrão espectral
- Valor máximo
- Valor mínimo
- Frequência de pico

**Justificativa:**
> O desbalanceamento mecânico se manifesta principalmente na **frequência fundamental de rotação (~50 Hz)** e seus harmônicos, tornando o domínio da frequência altamente informativo.

---

## 7. Criação do Dataset Final

Cada janela gera um vetor de features, resultando em um dataset com:

- ~31.000 amostras
- 8 features
- Rótulo binário (normal / falha)

Além disso, cada amostra mantém o identificador do arquivo de origem (`arquivo_id`) para controle de vazamento de dados.

---

## 8. Balanceamento das Classes

Foi aplicada **subamostragem da classe majoritária**, criando um dataset balanceado.

**Justificativa:**
> Evita viés do classificador para a classe dominante e melhora métricas como recall e F1-score.

> ⚠️ Observação metodológica: em aplicações reais, o balanceamento deve ser aplicado **apenas no conjunto de treino** para evitar viés estatístico.

---

## 9. Divisão Treino / Validação

Foi utilizado **GroupShuffleSplit**, garantindo que:

- Janelas do mesmo arquivo não apareçam simultaneamente em treino e validação

**Justificativa crítica:**
> Essa abordagem evita *data leakage*, um erro comum em trabalhos com sinais segmentados.

---

## 10. Modelos Utilizados

### 10.1 Random Forest

Parâmetros principais:
- 500 árvores
- Profundidade máxima: 10
- Pesos balanceados

**Justificativa:**
> Random Forest é robusto a ruído, não linearidades e outliers, sendo uma escolha sólida para dados industriais.

### 10.2 XGBoost

Parâmetros principais:
- 300 estimadores
- Learning rate: 0.05
- Subsample e colsample: 0.8

**Justificativa:**
> Utilizado como modelo de comparação por sua capacidade de capturar interações complexas entre features espectrais.

---

## 11. Resultados Obtidos

### 11.1 Métricas

| Modelo | Acurácia | F1 (Falha) | AUC |
|------|--------|------------|-----|
| Random Forest | ~0.61 | ~0.66 | ~0.64 |
| XGBoost | ~0.60 | ~0.66 | ~0.62 |

### 11.2 Interpretação

Apesar das métricas moderadas, os resultados são **consistentes com a complexidade do problema**:

- Desbalanceamento gera assinaturas espectrais sutis
- Classes possuem sobreposição significativa
- Dataset é ruidoso e realista

> Em contextos industriais, modelos com AUC ~0.65 já são úteis como **sistemas de alerta precoce**.

---

## 12. Avaliação Visual

- Curvas ROC para comparação dos modelos
- Matrizes de confusão normalizadas

Essas visualizações permitem compreender melhor o trade-off entre falsos positivos e falsos negativos.

---

## 13. Possíveis Melhorias Futuras

- Envelope espectral (Hilbert)
- Bandas específicas de frequência
- Features cepstrais
- Modelos baseados em CNN 1D
- Validação cruzada por grupo (GroupKFold)

---

## 14. Conclusão

Este projeto apresenta um pipeline completo, bem fundamentado e alinhado às boas práticas de **Machine Learning aplicado a manutenção preditiva**, com especial atenção à:

- Engenharia de atributos
- Controle de vazamento de dados
- Avaliação justa dos modelos



---

📌 **Autor:** Carlos Henrique Rodrigues Paixão
