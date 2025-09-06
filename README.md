Customer Churn Analysis

Goal: This project investigates customer churn in an e-commerce dataset, applying exploratory data analysis (EDA), outlier/anomaly detection, feature engineering, and predictive modeling to identify which factors drive churn and to build a reproducible pipeline for churn risk scoring.
-----------------------
Motivation

Customer churn directly impacts business sustainability: acquiring a new customer can cost 5–7x more than retaining an existing one.
By understanding the drivers of churn, companies can take proactive actions — targeted offers, loyalty programs, or personalized engagement — to reduce churn and increase lifetime value.
------------------------
Dataset

File: data/raw/E_Commerce_Dataset.xlsx (sheet: "E Comm")

Records: 5,630 customers

Target: Churn (1 = churned, 0 = retained)

Examples of features:

Numeric: Tenure, HourSpendOnApp, OrderCount, CashbackAmount, SatisfactionScore, DaySinceLastOrder, CouponUsed, OrderAmountHikeFromlastYear

Categorical: Gender, MaritalStatus, PreferredPaymentMode, PreferredLoginDevice, CityTier, PreferedOrderCat, Complain
----------------------
Exploratory Data Analysis (EDA)

Target distribution: ~16% churn rate → dataset is moderately imbalanced.

Numeric features:

Strong skew in OrderCount and CouponUsed (few customers with very high values).

CashbackAmount shows clusters around promotion thresholds.

Categorical features:

Payment method and preferred order category show clear churn differences.

Customers who complained are more likely to churn.

Outliers:

Univariate IQR flagged outliers in most numeric variables.

Multivariate IsolationForest (~1% contamination) highlighted unusual profiles.

EDA notebooks and interactive Plotly visualizations are available in notebooks/01_eda.ipynb.
------------------------------------------
Feature Engineering

Created domain-inspired features to enrich the dataset:

orders_per_month = OrderCount / (Tenure + 1)

engagement_rate = HourSpendOnApp / (Tenure + 1)

coupon_rate = CouponUsed / (OrderCount + 1)

cashback_per_order = CashbackAmount / (OrderCount + 1)

high_growth_flag = 1{OrderAmountHikeFromlastYear ≥ 18}

Bands:

Recency: 0–2, 3–7, 8–14, 15+ days

Tenure: 0–3, 4–12, 13–24, 25+ months

All preprocessing is packaged in a scikit-learn Pipeline:

Numeric → Median imputation + RobustScaler

Categorical → Constant imputation + OneHotEncoder

Saved as artifacts/feature_pipeline.joblib for reproducibility.

Implemented in notebooks/02_feature_engineering.ipynb.
--------------------------------------------------------
Modeling & Results

Baseline: Logistic Regression

Tree-based models: Random Forest, XGBoost, LightGBM

Validation: Stratified train/test split, with ROC-AUC and PR-AUC as main metrics
---------------------------------------------------------
Performance (example results)
Model	ROC-AUC	PR-AUC	F1 Score
Logistic Regression	0.78	0.41	0.52
Random Forest	0.85	0.52	0.61
LightGBM	0.87	0.55	0.64

LightGBM performed best, balancing interpretability and predictive power.

Feature importance & SHAP analysis revealed key churn drivers:

Low Tenure (newer customers churn more)

High Recency (long time since last order)

Complaints filed

Low SatisfactionScore

See detailed evaluation in notebooks/03_modeling.ipynb and 04_model_evaluation.ipynb.
------------------------------------------------
Interpretability & Insights

SHAP values confirm that recency, complaints, and satisfaction dominate churn risk.

Customers with frequent coupon usage and short tenure show mixed risk — some are price-sensitive, others highly engaged.

Actionable strategies:

Early engagement campaigns for new customers

Fast complaint resolution workflows

Reward programs targeting high-recency but previously loyal users

Insights documented in notebooks/05_interpretability.ipynb.
-------------------------------------------------------
Project Structure
data/
  raw/          # original Excel (not versioned)
  processed/    # feature matrices
notebooks/      # step-by-step workflow
src/            # reusable Python modules
plots/          # interactive HTML charts (local only)
artifacts/      # saved pipelines & models
---------------------------------
Setup & Reproducibility
Conda
conda env create -f environment.yml
conda activate churn_env
jupyter lab

Requirements (pip alternative)
pip install -r requirements.txt
---------------------------------------
Key Takeaways

Churn rate ≈ 16%: manageable imbalance.

Clear drivers: tenure, recency, complaints, satisfaction.

Tree-based models significantly outperform logistic regression.

SHAP provides transparency: actionable insights for retention strategies.

Pipeline ensures full reproducibility: same preprocessing for train & inference.
------------------------------------
Next Steps

Add time-based validation (simulate deployment).

Test uplift modeling for personalized retention campaigns.

Deploy pipeline as an API (FastAPI / Flask) for real-time scoring.

Automate retraining with fresh data.
-----------------------
📜 License

MIT License – see LICENSE
.

🔗 Author: Luiz Otávio Marangão


































---------------------------------------------------------------------------------------------
# Churn Analysis

This project analyzes customer churn to identify patterns and predict customer attrition.

## Table of Contents

- [Overview](#overview)
- [Installation](#installation)
- [Usage](#usage)
- [Project Structure](#project-structure)
- [Contributing](#contributing)
- [License](#license)

## Overview

Churn analysis helps businesses understand why customers leave and how to improve retention. This repository contains code and resources for data preprocessing, exploratory analysis, modeling, and evaluation.

## Installation

1. Clone the repository:
   ```bash
   git clone https://github.com/yourusername/churn_analysis.git
   cd churn_analysis
   ```
2. Install dependencies:
   ```bash
   pip install -r requirements.txt
   ```

## Usage

1. Prepare your dataset in the `data/` directory.
2. Run the analysis scripts:
   ```bash
   python src/analyze.py
   ```
3. Review results in the `output/` directory.

## Project Structure

```
churn_analysis/
├── data/
├── src/
├── output/
├── requirements.txt
└── README.md
```

## Contributing

Contributions are welcome! Please open issues or submit pull requests.

## License

This project is licensed under the MIT License.

---

✅ Etapas já implementadas
Carregamento dos dados

Leitura da planilha “E Comm” e remoção de colunas vazias.

Visão geral inicial

Exibição do shape e primeiras linhas do DataFrame.

Mapa de duplicatas e valores faltantes (check de duplicated() e isnull()).

Análise univariada

Estatísticas descritivas (mean, median, std, etc.) para colunas numéricas.

Distribuições e contagens para variáveis categóricas.

Análise bivariada

Categóricas vs. Churn: histograms groupados por status de churn.

Numéricas vs. Churn: boxplots/violin plots para cada feature numérica.

Matriz de correlação

Cálculo e visualização (heatmap ou gráfico interativo).

Insights sobre multicolinearidade e features correlacionadas ao target.

Detecção de outliers & anomalias

IQR univariado (flag de outliers em cada variável).

Isolation Forest multivariado (flag e escore de anomalia).

Gráficos interativos salvos em HTML.

🛠️ Recomendações para completar a análise
Distribuição do target (Churn)

Gráfico de barras mostrando proporção de clientes que churned vs. retained (avaliação de balanceamento).

Mapa de missing values

Heatmap ou tabela de contagem de NaN por coluna, para identificar padrões de ausência de dados.

Verificação de tipos e conversões

Confirmação de que datas, categorias e numéricos têm os tipos corretos; conversão de colunas quando necessário.

Resumo estatístico mais rico

Adição de métricas de skewness, kurtosis e percentis (describe + extras) para detectar enviesamentos.

Distribuições contínuas

KDE plots (ou histograma + curva) para features muito enviesadas (ex.: OrderCount, CashbackAmount).

Interações simples

Pairplot reduzido (2–3 features mais relevantes) para registrar relações não lineares.

Análise de tempo

Se existir componente temporal (datas de pedido, tenure em dias), uma série temporal ou histograma sobre “recency” pode ajudar.

Consumo de memória

Revisar uso de memória do DataFrame e otimizar dtypes para deploy/pipeline.

---

O que já foi feito

Exploração e pré‑processamento dos dados

Carregamento e análise exploratória de application_train.csv e application_test.csv.

Estudo das variáveis, identificação de colunas com alto percentual de ausências e documentação de todos os campos.

Criação de um pipeline de pré‑processamento que:

Imputa ausentes (numéricos com mediana, categóricos com “Unknown”).

Escala numéricos com RobustScaler e codifica categorias com OneHotEncoder e TargetEncoder.

Garante ausência de vazamento de informação ao ser aplicado dentro da validação cruzada.

Persistência do objeto preprocessor.joblib e lista de colunas resultantes (train_columns.csv).

Desenvolvimento de modelos

Baseline com Logistic Regression L2 treinado em 5 folds estratificados; desempenho AUC ≈ 0,746 e KS ≈ 0,365.

Experimento com Elastic Net descartado por piora nas métricas.

Migração para LightGBM com ajuste via Optuna; melhoria nas métricas (AUC ≈ 0,761, KS ≈ 0,391, P@20 ≈ 0,212).

Diagnóstico de calibração e aplicação de regressão isotônica nos 5 folds para calibrar as probabilidades (Brier caiu de 0,169 para 0,064 e a reliability curve se aproximou da diagonal
screenshot
).

Definição de um limiar de decisão por KS máximo e criação de bandas de risco (Very Low, Low, …, Very High) com base nas quantis das PDs calibradas; tudo salvo em thresholds_bands.json.

Interpretabilidade

Cálculo de valores SHAP para o modelo LightGBM; geração de gráficos de importância global dos 20 principais atributos
screenshot
.

Preparação de estrutura para explicações locais (gráficos waterfall) em Streamlit.

Persistência de artefatos

Armazenamento de todos os componentes necessários para scoring: preprocessor.joblib, lgbm_model.joblib, isotonic.joblib, feature_names_logreg.csv, thresholds_bands.json, imagens de SHAP e da curva de calibração.

Scripts, API e Dashboard

scripts/preprocess.py implementa funções para carregar artefatos e pontuar novos dados com atribuição de bandas de risco.

api.py expõe um serviço FastAPI com /score_one e /score_batch, retornando PD bruta, PD calibrada, banda de risco e decisão aprovar/declinar, além de um endpoint de health check.

streamlit_app.py oferece um painel interativo no qual é possível carregar arquivos CSV para pontuação em lote ou inserir manualmente dados de um cliente e visualizar a explicação SHAP local (cascata).

🛠️ O que pode melhorar para um projeto profissional

Organização de pacotes: extrair as funções de scoring duplicadas no api.py e no streamlit_app.py para um módulo utilitário único (por ex. scripts/preprocess.score_df), evitando divergências na lógica.

Script de treinamento: o arquivo train_model.py está vazio; seria interessante migrar as etapas de treinamento dos notebooks para um script parametrizado para facilitar re‑treinos e CI/CD.

Documentação:

Completar o README.md com uma descrição clara do problema, fluxo de modelagem, instruções de setup (como instalar dependências, rodar notebooks, API e Streamlit) e exemplos de uso.

Adicionar comentários e docstrings nos scripts principais (preprocess.py, api.py, streamlit_app.py) para melhorar a legibilidade.

Validação extra:

Adicionar um conjunto de testes unitários simples para verificar se a pipeline está carregando e se o API retorna os campos esperados.

Considerar métricas adicionais (Precision@k, Recall@k) e algum tipo de análise de fairness se houver requisitos legais.

Controle de versão de dados: explicitar onde os dados brutos ficam armazenados (pasta data/raw) e não versioná‑los no GitHub; usar .gitignore para arquivos grandes e sensíveis.

Ambiente de dependências: garantir que requirements.txt esteja atualizado e possua versões testadas, além de opcionalmente fornecer um environment.yml para quem preferir Conda.

Automação de deploy: criar scripts para subir a API e o Streamlit em um serviço (Heroku, Render, etc.) ou mesmo Dockerizar a aplicação para facilitar a execução em outras máquinas.

📦 Etapas para finalizar e subir ao GitHub

Limpar o repositório:

Remover dados brutos sensíveis ou grandes; manter somente um “sample” ou instruções para download.

Eliminar arquivos temporários ou redundantes do Jupyter.

Completar train_model.py (ou renomeá‑lo) com o pipeline completo de treinamento e salvamento de artefatos.

Refatorar o código para centralizar a lógica de pontuação e calibração em um único módulo e importar essa função no API e no Streamlit.

Atualizar o README.md com descrição, instruções de instalação (pip install -r requirements.txt), como rodar notebooks, como executar a API (uvicorn app.api:app --reload) e como iniciar o painel (streamlit run app/streamlit_app.py).

Adicionar testes básicos e um pytest.ini se desejar ir além.

Fazer commit estruturado: criar repositório no GitHub, adicionar todos os scripts e notebooks relevantes, documentar no histórico as principais etapas.

Publicar: push para GitHub, configurar repositório como portfólio público e, se desejar, adicionar badges de build, documentação e link para demo online.

Com essas melhorias e passos finais, seu projeto de credit‑risk estará pronto para ser apresentado como parte de seu portfólio profissional.
