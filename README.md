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
