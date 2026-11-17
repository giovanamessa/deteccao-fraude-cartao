# 💳 Detecção de Fraudes em Transações de Cartão de Crédito

## 📌 Sobre o projeto

Este projeto foi desenvolvido com o objetivo de criar e avaliar modelos de Machine Learning capazes de identificar fraudes em transações de cartão de crédito.

O principal desafio deste problema é o forte desbalanceamento das classes: as transações fraudulentas representam uma parcela muito pequena do total. Por esse motivo, uma acurácia elevada não significa necessariamente que o modelo seja eficiente na identificação de fraudes.

Um modelo que classificasse praticamente todas as transações como normais poderia apresentar uma acurácia superior a 99% e, ainda assim, deixar de identificar as transações fraudulentas.

Por isso, a avaliação deste projeto priorizou principalmente as métricas **recall, precisão e F1\-score da classe de fraude**, além de ROC AUC e PR AUC.

---

## 📊 Base de dados

Foi utilizado o dataset de transações de cartão de crédito disponibilizado para o desafio:

[https://storage.googleapis.com/download.tensorflow.org/data/creditcard.csv](https://storage.googleapis.com/download.tensorflow.org/data/creditcard.csv)

A base é carregada diretamente no notebook por meio do pandas, portanto o arquivo CSV não é armazenado neste repositório.

### Principais variáveis

- Time: momento em que a transação ocorreu;
- Amount: valor da transação;
- Class: variável alvo, sendo 0 uma transação normal e 1 uma fraude;
- V1 a V28: variáveis anonimizadas e transformadas utilizando PCA.

---

## 🔎 Análise do problema

A análise inicial demonstrou um forte desbalanceamento entre as classes, com uma quantidade muito maior de transações normais do que fraudulentas.

Nesse cenário, a acurácia isoladamente não é uma boa medida de desempenho.

As principais métricas utilizadas foram:

- **Recall:** indica quantas das fraudes reais o modelo conseguiu identificar;
- **Precisão:** indica quantas das transações classificadas como fraude realmente eram fraudulentas;
- **F1\-score:** representa o equilíbrio entre precisão e recall;
- **ROC AUC:** avalia a capacidade geral do modelo de separar as classes;
- **PR AUC:** avalia a relação entre precisão e recall e é especialmente relevante em problemas desbalanceados.

---

## ⚙️ Preparação dos dados

Foram realizadas as seguintes etapas:

1. Carregamento do dataset diretamente pela URL;
2. Análise da estrutura e distribuição das classes;
3. Verificação de valores ausentes e duplicidades;
4. Transformação logarítmica de Amount utilizando np.log1p;
5. Separação das variáveis preditoras e da variável alvo Class;
6. Divisão entre treino e teste utilizando stratify, preservando a proporção de fraudes;
7. Padronização das variáveis utilizando StandardScaler dentro de um Pipeline, evitando vazamento de informações entre treino e teste;
8. Utilização de pesos balanceados para aumentar a importância da classe minoritária.

---

## 🤖 Modelos avaliados

Foram treinadas e comparadas diferentes abordagens:

- Regressão Logística;
- Regressão Logística com ajuste de limiar;
- Regressão Logística com GridSearchCV;
- Regressão Logística com Oversampling;
- Regressão Logística com Undersampling;
- Random Forest;
- XGBoost.

---

## 📈 Resultados obtidos

| Modelo | Limiar | Precisão | Recall | F1\-score | ROC AUC | PR AUC |
| --- | --- | --- | --- | --- | --- | --- |
| Regressão Logística | 0,50 | 6,03% | **91,84%** | 11,31% | 97,11% | 71,13% |
| Regressão Logística \+ Oversampling | 0,50 | 6,03% | **91,84%** | 11,31% | 97,09% | 71,14% |
| Regressão Logística \+ GridSearch | 0,50 | 6,02% | **91,84%** | 11,31% | 97,12% | 71,13% |
| Regressão Logística \+ Undersampling | 0,50 | 5,23% | **91,84%** | 9,89% | 97,45% | 45,56% |
| Regressão Logística ajustada | 0,30 | 2,62% | **91,84%** | 5,10% | 97,11% | 71,13% |
| XGBoost | 0,50 | 55,48% | 87,76% | 67,98% | 98,10% | 84,92% |
| Random Forest | 0,50 | **96,10%** | 75,51% | **84,57%** | 95,68% | **85,77%** |

---

## 🏆 Comparação dos modelos

A **Regressão Logística apresentou o maior recall, de 91,84%**, demonstrando alta capacidade de identificar as fraudes existentes.

Por outro lado, sua precisão foi de apenas **6,03%**, indicando uma quantidade elevada de falsos positivos.

O **XGBoost apresentou um resultado mais equilibrado**, alcançando:

- Recall: **87,76%**
- Precisão: **55,48%**
- F1\-score: **67,98%**
- PR AUC: **84,92%**

Apesar de apresentar um recall aproximadamente quatro pontos percentuais menor que o da Regressão Logística, o XGBoost aumentou significativamente a precisão e o F1\-score.

Já o **Random Forest apresentou o maior F1\-score, de 84,57%, e a maior precisão, de 96,10%**, além do maior PR AUC, de **85,77%**. Entretanto, seu recall foi menor, chegando a **75,51%**.

Os resultados demonstram que a escolha do modelo depende do objetivo do negócio. Se a prioridade máxima for identificar o maior número possível de fraudes, a Regressão Logística apresenta vantagem pelo recall. Caso seja necessário equilibrar a identificação das fraudes com a redução de falsos positivos, XGBoost e Random Forest apresentam alternativas mais interessantes.

---

## 🎯 Ajuste do limiar de decisão

Também foi avaliada a redução do limiar da Regressão Logística de 0,50 para 0,30.

O objetivo era verificar se um limiar menor permitiria identificar uma quantidade maior de fraudes.

Entretanto, neste experimento, o recall permaneceu em **91,84%**, enquanto a precisão caiu de **6,03% para 2,62%** e o F1\-score caiu de **11,31% para 5,10%**.

Dessa forma, os resultados obtidos não justificaram a utilização do limiar de 0,30. Para a Regressão Logística avaliada neste projeto, o limiar padrão de **0,50 apresentou melhor equilíbrio**.

---

## 🔄 Tratamento do desbalanceamento

Além do uso de class\_weight='balanced', foram avaliadas estratégias de:

- **Oversampling**, aumentando a representação da classe de fraude;
- **Undersampling**, reduzindo a quantidade de exemplos da classe majoritária.

O Oversampling manteve recall e F1 praticamente iguais aos da Regressão Logística original.

O Undersampling também manteve o recall em 91,84%, porém apresentou redução da precisão, do F1\-score e principalmente do PR AUC.

Os resultados reforçam a importância de testar diferentes estratégias de balanceamento em vez de assumir previamente que determinada técnica produzirá um modelo melhor.

---

## 🔧 Otimização com GridSearchCV

Também foi utilizado o GridSearchCV para avaliar diferentes hiperparâmetros da Regressão Logística.

A métrica utilizada para direcionar a seleção foi o **recall**, uma vez que o objetivo principal do problema é reduzir a quantidade de fraudes que passam sem identificação.

O modelo otimizado manteve recall de **91,84%**, resultado semelhante ao modelo baseline.

---

## 🧠 Explicabilidade com SHAP

Além de avaliar o desempenho dos modelos, o projeto utilizou **SHAP (SHapley Additive exPlanations)** para analisar a influência das variáveis nas previsões.

A análise permite compreender quais características contribuíram para aumentar ou reduzir a probabilidade de uma transação ser classificada como fraude.

Foram utilizadas:

- análise global das variáveis mais relevantes;
- análise individual de uma transação;
- visualizações SHAP para interpretação das decisões do modelo.

Como as variáveis V1 a V28 foram anonimizadas utilizando PCA, é possível avaliar sua influência estatística sobre o modelo, mas sua interpretação direta em termos de negócio é limitada.

---

## 🚀 Evoluções realizadas no projeto

Além do fluxo principal proposto no desafio, foram incorporadas algumas análises adicionais:

- Comparação entre Oversampling e Undersampling;
- Utilização de PR AUC para avaliar os modelos em um cenário desbalanceado;
- Comparação automática das métricas dos modelos;
- Análise de diferentes limiares de decisão;
- GridSearchCV orientado pelo recall;
- Análise de importância das variáveis;
- Explicabilidade global e individual utilizando SHAP;
- Exportação da comparação dos modelos para CSV.

---

## 💡 Principais aprendizados

Este projeto mostrou que **uma alta acurácia não é suficiente para determinar a qualidade de um modelo de detecção de fraude**.

Em problemas altamente desbalanceados, é necessário analisar principalmente a capacidade do modelo de encontrar a classe minoritária.

Também foi possível observar na prática o trade\-off entre **recall e precisão**:

- aumentar a sensibilidade pode capturar mais fraudes, mas também aumentar os falsos positivos;
- modelos mais precisos reduzem falsos alertas, mas podem deixar uma quantidade maior de fraudes passar.

Portanto, a definição do melhor modelo deve considerar não apenas uma métrica isolada, mas também o impacto de cada tipo de erro para o negócio.

---

## 🛠️ Tecnologias utilizadas

- Python
- Google Colab
- pandas
- NumPy
- scikit\-learn
- imbalanced\-learn
- XGBoost
- SHAP
- Matplotlib
- Seaborn

---

## 📂 Arquivo principal

O desenvolvimento completo, incluindo códigos, tabelas, curvas, treinamento dos modelos e análises, está disponível no notebook:

DIO\_deteccao\_fraude\_cartao.ipynb

---

## ▶️ Como executar o projeto

1. Abra o arquivo DIO\_deteccao\_fraude\_cartao.ipynb no Google Colab;
2. Execute as células na ordem apresentada;
3. O dataset será carregado automaticamente pela URL;
4. Aguarde o treinamento e avaliação dos modelos;
5. Analise as matrizes de confusão, curvas ROC, curvas Precisão\-Recall e visualizações SHAP.

O dataset não precisa ser baixado nem armazenado no repositório.

