# Projeto: Predição de Alta Adoção de Telemedicina por Perfil Regional e Demográfico

## Visão geral
Este projeto analisa o uso de telemedicina a partir do dataset **TMEDTREND_PUBLIC_250827.csv**, que contém informações agregadas sobre beneficiários do Medicare/Medicaid nos Estados Unidos. O conjunto de dados inclui atributos temporais (ano e trimestre), regionais (estado/descrição geográfica e classificação rural/urbana RUCA) e demográficos (faixa etária, sexo, raça e status de elegibilidade), além de métricas de adoção de telehealth.

O trabalho tem dois objetivos principais:
1. Obter insights sobre padrões de uso de telemedicina por região e grupos demográficos.
2. Verificar se é possível prever quais unidades (estado/região × trimestre × perfil populacional) pertencem ao grupo de **alta adoção** de telemedicina.

Todo o desenvolvimento, explicações didáticas, gráficos e interpretações estão documentados no Jupyter Notebook do projeto.

---

## Dataset
Arquivo utilizado:
- `TMEDTREND_PUBLIC_250827.csv`

[Link do arquivo para download]("https://data.cms.gov/summary-statistics-on-use-and-payments/medicare-medicaid-service-type-reports/medicare-telehealth-trends")

Principais colunas:
- Categóricas: `Year`, `quarter`, `Bene_Geo_Desc`, `Bene_RUCA_Desc`, `Bene_Age_Desc`, `Bene_Sex_Desc`, `Bene_Race_Desc`, `Bene_Mdcd_Mdcr_Enrl_Stus`, `Bene_Mdcr_Entlmt_Stus`
- Numéricas: `Total_Bene_TH_Elig`, `Total_PartB_Enrl`, `Total_Bene_Telehealth`, `Pct_Telehealth`

Cada registro representa um grupo populacional agregado por ano, trimestre, região e características demográficas.

---

## Metodologia

### 1. Análise exploratória de dados (EDA)
Foram realizadas análises exploratórias para entender o comportamento do indicador `Pct_Telehealth` e suas variações por grupos:
- Distribuição de `Pct_Telehealth`
- Tendência temporal por ano e trimestre
- Comparações por RUCA (urbano/rural), faixa etária, sexo e raça
- Correlação entre variáveis numéricas
- EDA direcionada após criação do target binário

Principais insights encontrados:
- Pico de adoção em 2020, com queda e estabilização nos anos posteriores.
- Regiões urbanas apresentam maior probabilidade de alta adoção do que regiões rurais.
- Grupos etários 0–64 têm probabilidade muito maior de alta adoção do que populações acima de 65 anos.
- Diferenças de adoção aparecem entre sexos e grupos raciais.
- Estados específicos apresentam padrões consistentes de maior adoção.

---

### 2. Formulação do problema de Machine Learning
Foi formulado um problema de **classificação binária supervisionada**.

Target definido:
- `High_Adoption = 1` (Alta adoção) para unidades no top 25% de `Pct_Telehealth` dentro de cada trimestre (ano + trimestre).
- `High_Adoption = 0` (Baixa adoção) para as demais unidades.

Esse enquadramento permite prever se um grupo tende a estar entre os de maior adoção de telemedicina em seu contexto temporal.

---

### 3. Pré-processamento
Etapas aplicadas:
- Remoção de registros sem `Pct_Telehealth`.
- Remoção de linhas com `quarter = "Overall"`.
- Criação do target `High_Adoption`.
- Separação treino/teste (80/20) com `stratify=y`.
- One-Hot Encoding para variáveis categóricas.
- StandardScaler para variáveis numéricas (aplicado apenas a modelos lineares).
- Remoção de colunas que causariam vazamento (`Pct_Telehealth` e `p75_trim`).

---

### 4. Modelos treinados
Foram testados dois modelos:

1. **Regressão Logística (baseline)**
- Modelo inicial para referência de desempenho.

2. **Random Forest (modelo final)**
- Modelo baseado em múltiplas árvores, capaz de capturar relações não lineares.

---

### 5. Avaliação
Métricas usadas:
- Accuracy
- Precision
- Recall
- F1-score
- ROC-AUC
- Matriz de confusão
- Curva ROC

Resultados principais:
- A Regressão Logística já apresentou desempenho alto.
- O Random Forest superou o baseline, com maior accuracy, precision e ROC-AUC.
- O Random Forest foi selecionado como modelo final.

---

### 6. Interpretação (Feature Importance)
Foi extraída a importância das variáveis do Random Forest, destacando:
- Forte peso de variáveis de escala populacional (`Total_Bene_TH_Elig`, `Total_Bene_Telehealth`, `Total_PartB_Enrl`).
- Faixa etária 0–64 como um dos maiores fatores demográficos.
- Relevância de status clínico/benefício e dupla elegibilidade.
- Estados específicos influenciando a probabilidade de alta adoção.
- Influência temporal via `Year`.

---

## Estrutura do projeto
- `README.md`
- `TMEDTREND_PUBLIC_250827.csv`
- `notebook.ipynb` (arquivo principal com todo o desenvolvimento)

---

## Como executar
1. Abra o Jupyter Notebook do projeto.
2. Certifique-se de que o arquivo CSV está no mesmo diretório ou ajuste o caminho no notebook.
3. Execute as células em ordem para reproduzir as análises e a modelagem.

Dependências principais:
- pandas
- numpy
- matplotlib
- scikit-learn

---

## Integrantes

ERICK MOLINA - RM 553852

FELIPE CASTRO SALAZAR - RM 553464

MARCELO VIEIRA DE MELO - RM 552953

RAYARA AMARO FIGUEIREDO - RM 552635

VICTOR RODRIGUES - RM 554158

---

## Conclusão
O projeto demonstrou que é possível prever com alta precisão se uma unidade regional/demográfica terá alta adoção de telemedicina. Além do desempenho preditivo, o modelo final forneceu insights interpretáveis sobre os fatores de maior impacto, atendendo ao objetivo proposto de análise e predição de adoção de telehealth.
