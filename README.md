# Limpeza e Preparação de Dados — Clientes de Loja Online

Este projeto documenta a realização de um **pipeline de limpeza e preparação de dados (Data Cleaning)** utilizando **Python + Pandas**, aplicado a um dataset fictício de clientes de uma loja online.

O objetivo é demonstrar domínio em:

- Inspeção e diagnóstico de qualidade de dados
- Tratamento de valores nulos e ausentes
- Detecção e remoção de duplicatas
- Detecção e tratamento de outliers (regra do IQR)
- Normalização de variáveis numéricas (MinMaxScaler)
- Encoding de variáveis categóricas (One-Hot Encoding)
- Manipulação e extração de features a partir de datas

---

## Contexto

Cada linha do dataset representa um cliente com histórico de compras.

O dataset contém variáveis quantitativas, qualitativas e temporais para permitir limpeza completa e preparação para modelagem.

## Dataset

O dataset utilizado (`dados_clientes.csv`) contém as seguintes colunas:

### Variáveis

- **idade** (quantitativa): idade do cliente
- **renda_anual** (quantitativa): renda anual do cliente (R$)
- **valor_compra** (quantitativa): valor gasto na compra (R$)
- **genero** (qualitativa nominal): gênero do cliente
- **categoria_produto** (qualitativa nominal): categoria do produto comprado
- **regiao** (qualitativa nominal): região do cliente
- **data_compra** (temporal): data em que a compra foi realizada

---

## Etapas do Data Cleaning

### 1) Leitura e inspeção inicial

Foram realizadas as etapas iniciais para diagnosticar a qualidade do dataset:

- `head()` para inspeção das primeiras linhas
- `info()` para verificar tipos e valores ausentes
- `describe()` para estatísticas descritivas iniciais

---

### 2) Tratamento de valores nulos e ausentes

Identificação da quantidade e porcentagem de valores nulos por coluna:

```python
df.isnull().sum()
porcentagem_df = (df.isnull().sum() / len(df)) * 100
```

Estratégia de preenchimento adotada:

- **idade** e **renda_anual**: preenchidas com a **mediana** (robustez a outliers)
- **valor_compra**: preenchida com a **mediana**
- **data_compra**: preenchida com a **moda** (valor mais frequente)

---

### 3) Tratamento de duplicatas

Verificação e remoção de registros duplicados:

```python
df.duplicated().sum()
```

---

### 4) Detecção e tratamento de Outliers (Regra do IQR)

Visualização via boxplot e aplicação da regra do **IQR** (Intervalo Interquartil) para `valor_compra`:

- Q1 (25%) e Q3 (75%)
- IQR = Q3 - Q1
- Limites: Q1 - 1.5×IQR e Q3 + 1.5×IQR

```python
limite_inferior = Q1 - 1.5 * IQR
limite_superior = Q3 + 1.5 * IQR
outliers = df[(df['valor_compra'] < limite_inferior) | (df['valor_compra'] > limite_superior)]
```

---

### 5) Normalização de variáveis numéricas

Aplicação do **MinMaxScaler** para escalar as variáveis numéricas ao intervalo [0, 1]:

```python
from sklearn.preprocessing import MinMaxScaler
scaler = MinMaxScaler()
df[['idade','renda_anual','valor_compra']] = scaler.fit_transform(
    df[['idade','renda_anual','valor_compra']]
)
```

Colunas normalizadas: `idade`, `renda_anual`, `valor_compra`.

---

### 6) Encoding de variáveis categóricas

Aplicação de **One-Hot Encoding** com `pd.get_dummies` para converter variáveis categóricas em colunas binárias:

```python
df = pd.get_dummies(
    df,
    columns=['genero','categoria_produto','regiao'],
    drop_first=True
)
```

O parâmetro `drop_first=True` evita a multicolinearidade entre as colunas geradas.

---

### 7) Manipulação e extração de features de datas

Conversão da coluna `data_compra` para o tipo `datetime` e extração de features temporais:

```python
df["data_compra"] = pd.to_datetime(df["data_compra"].astype(str).str.replace("/", "-"), errors="coerce")
df["ano_compra"]      = df["data_compra"].dt.year
df["mes_compra"]      = df["data_compra"].dt.month
df["dia_compra"]      = df["data_compra"].dt.day
df["dia_semana"]      = df["data_compra"].dt.day_name()
```

Features geradas: `ano_compra`, `mes_compra`, `dia_compra`, `dia_semana`.

---

## Principais Resultados

- Dataset limpo, sem valores nulos e sem duplicatas após o pipeline.
- Variáveis numéricas normalizadas e prontas para uso em modelos de Machine Learning.
- Variáveis categóricas convertidas em representação binária.
- Coluna de data enriquecida com features temporais para análises sazonais e de comportamento.

---

## Como executar

### 1) Criar e ativar ambiente virtual

```bash
python3 -m venv .venv
source .venv/bin/activate
```

### 2) Instalar dependências

```bash
pip install pandas matplotlib scikit-learn jupyter
```

### 3) Rodar o notebook

```bash
jupyter notebook rocketseat-dataclean-eda.ipynb
```

---

## Tecnologias

- Python 3
- Pandas
- Matplotlib
- Scikit-learn
- Jupyter Notebook

---

## Autor

Projeto desenvolvido como parte do aprendizado em análise de dados por Gabriel Gonçalves
