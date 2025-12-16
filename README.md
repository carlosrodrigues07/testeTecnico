### 🛠️ Detecção de Falhas Mecânicas com Aprendizado de Máquina

Desafio Técnico – MAFAULDA / MFS

#### 1. Visão Geral do Projeto

Este projeto tem como objetivo desenvolver um pipeline completo de detecção automática de falhas mecânicas a partir de sinais de sensores, utilizando técnicas de processamento de sinais, extração de características e aprendizado de máquina supervisionado.

O estudo foi realizado com o banco de dados MAFAULDA, que contém séries temporais multivariadas adquiridas a partir do Machinery Fault Simulator (MFS). As condições analisadas neste trabalho foram:

- Operação Normal

- Desbalanceamento (Imbalance)

O foco principal do projeto é avaliar a capacidade de diferentes modelos em detectar falhas mecânicas, priorizando métricas relevantes para manutenção preditiva.

2. Estrutura do Pipeline

O pipeline foi organizado nas seguintes etapas:

1. Aquisição e organização dos dados

2. Pré-processamento dos sinais

3. Extração de características no domínio do tempo e da frequência

4. Balanceamento e divisão dos dados

5. Treinamento dos modelos

6. Avaliação e comparação de desempenho

7. Visualização e interpretação dos resultados

#### 3. Aquisição e Organização dos Dados

Os dados foram carregados a partir de arquivos CSV contendo sinais temporais provenientes de sensores do MFS.

Decisões tomadas:

Cada arquivo representa uma condição específica de operação.

Foi mantido o identificador do arquivo (```arquivo_id``) para rastreabilidade das janelas extraídas.

As classes foram codificadas como:

- ```0``` → Operação Normal

- ```1``` → Falha por Desbalanceamento

#### 4. Pré-processamento dos Dados

O pré-processamento foi realizado antes da extração das características, garantindo sinais mais estáveis e informativos.

Etapas aplicadas:

1. Conversão para valores numéricos

- Remoção de valores ausentes ou inválidos.

2. Remoção do componente DC

- Elimina deslocamentos do sinal que não carregam informação física relevante.

3. Filtragem

- Aplicação de filtros para reduzir ruídos de alta frequência.

4. Segmentação em janelas deslizantes

   - O sinal foi dividido em janelas com tamanho fixo, permitindo:

       - Aumento do número de amostras
 
     - Captura de comportamentos locais do sinal

Justificativa:

A divisão em janelas é uma prática comum em análise de vibração, pois falhas mecânicas se manifestam de forma localizada no tempo.

#### 5. Extração de Características

Para cada janela de sinal, foram extraídas características nos domínios:

5.1 Domínio do Tempo

- RMS (Root Mean Square)

- Curtose

- Assimetria (Skewness)

Essas métricas capturam:

- Energia do sinal

- Presença de impulsos

- Assimetria associada a falhas mecânicas

5.2 Domínio da Frequência (FFT)

A Transformada Rápida de Fourier (FFT) foi aplicada a cada janela.

Características extraídas:

- Média da FFT

- Desvio padrão da FFT

- Valor máximo e mínimo da FFT

- Frequência do pico espectral

Justificativa:

Falhas como desbalanceamento tendem a introduzir padrões específicos no espectro de frequência, tornando a FFT uma ferramenta fundamental.

#### 6. Construção do Dataset

- Cada linha do dataset representa uma janela de sinal.

- O dataset final possui milhares de janelas, aumentando a capacidade de generalização dos modelos.

- Foram verificados valores ausentes após a extração das features, garantindo um dataset consistente.

#### 7. Balanceamento e Divisão dos Dados
Balanceamento:

As classes foram balanceadas selecionando o mesmo número de janelas para cada condição, evitando viés do modelo.

Divisão:

- 70% Treinamento

- 30% Validação

Normalização:

- Aplicado StandardScaler

- Essencial para modelos sensíveis à escala das features

### 8. Modelos Avaliados

Foram selecionados dois modelos clássicos e amplamente utilizados em problemas industriais:

8.1 Random Forest

- Modelo baseado em múltiplas árvores de decisão

- Robusto a ruídos

- Capaz de capturar relações não lineares

- Fornece interpretabilidade via importância das features

8.2 XGBoost

- Algoritmo de Gradient Boosting otimizado

- Excelente desempenho em dados tabulares

- Capaz de modelar padrões complexos

Justificativa da escolha:

Esses modelos são frequentemente utilizados em aplicações industriais por combinarem bom desempenho, robustez e baixa necessidade de ajuste fino.

#### 9. Métricas de Avaliação

Como o objetivo é detectar falhas mecânicas, a avaliação foi orientada para métricas além da acurácia:

- Precisão

- Recall

- F1-score

- AUC-ROC

- Matriz de Confusão Normalizada

Observação importante:

Em manutenção preditiva, recall da classe de falha é especialmente crítico, pois falhas não detectadas podem gerar custos elevados ou riscos operacionais.

#### 10. Resultados Obtidos
Desempenho Geral

- Random Forest

    - Melhor equilíbrio entre precisão e recall

    - Melhor interpretabilidade

- XGBoost

    - Capacidade semelhante de separação das classes

    - Levemente superior em AUC em alguns cenários

As curvas ROC indicam uma capacidade moderada de separação, consistente com a complexidade do problema e o conjunto de features utilizado.

#### 11. Visualização dos Resultados

Foram geradas as seguintes visualizações:

- Curva ROC comparativa entre os modelos

- Matriz de confusão normalizada

- Importância das características (Random Forest)

Essas visualizações permitem:

- Identificar erros críticos

- Avaliar sensibilidade à falha

- Interpretar o comportamento do modelo

#### 12. Limitações do Estudo

- As features foram extraídas de janelas independentes, o que pode introduzir correlação entre amostras.

- O modelo não considera explicitamente a rotação da máquina ou ordens harmônicas específicas.

- O dataset foi limitado a duas condições operacionais.

#### 13. Trabalhos Futuros

- Extração de features por bandas específicas de frequência

- Uso de estatísticas móveis (rolling features)

- Classificação por arquivo completo, não apenas por janelas

- Avaliação de modelos baseados em Deep Learning (CNNs 1D)

#### 14. Conclusão

Este trabalho apresentou um pipeline completo e estruturado para detecção de falhas mecânicas utilizando dados de vibração. Os resultados demonstram que técnicas clássicas de aprendizado de máquina, aliadas a um pré-processamento adequado e extração de características no domínio da frequência, são capazes de identificar padrões associados a falhas mecânicas de forma consistente.