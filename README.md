# Trade&Ahead: segmentación de acciones de la NYSE

Segmenté 266 acciones de la New York Stock Exchange en 6 perfiles de inversión con K-Means y clustering jerárquico, para apoyar portafolios diversificados según el nivel de riesgo.

## Problema de negocio

Trade&Ahead es una consultora financiera que ofrece estrategias de inversión personalizadas. Entregó datos de precio e indicadores financieros de compañías listadas en la NYSE, con tres encargos: analizar los datos, agrupar las acciones según esos atributos y describir las características de cada grupo. El objetivo es armar portafolios diversificados, identificando acciones con perfiles similares y baja correlación, para reducir el riesgo cuando el mercado baja.

## Datos

Archivo: [`stock_data.csv`](stock_data.csv). **340 compañías** y **15 columnas**, sin valores nulos.

Variables: `Ticker Symbol`, `Security` (nombre de la compañía), `GICS Sector`, `GICS Sub Industry`, `Current Price`, `Price Change` (cambio porcentual en 13 semanas), `Volatility`, `ROE`, `Cash Ratio`, `Net Cash Flow`, `Net Income`, `Earnings Per Share`, `Estimated Shares Outstanding`, `P/E Ratio` y `P/B Ratio`.

Antes del clustering se trataron outliers con un IQR restringido (percentiles 10 y 90, límites a 1,5 × IQR) en las columnas numéricas. El conjunto pasó de **340 a 266** observaciones. Luego se estandarizaron las **11 variables numéricas** con `StandardScaler`.

## Enfoque

No se usó PCA: el flujo es limpieza de outliers, escalado z-score y clustering.

1. **K-Means** (`random_state=1`), evaluando *k* de 1 a 14 con el método del codo (distorsión) y de 2 a 14 con el silhouette score. Se eligió **k = 6** (silhouette 0,1322 en ese *k*; la distorsión baja de 2,489 en *k* = 5 a 2,367 en *k* = 6, y luego apenas a 2,348 en *k* = 7).
2. **Clustering jerárquico aglomerativo**, comparando los enlaces *single*, *complete*, *average*, *centroid*, *ward* y *weighted* (distancia euclidiana), con dendrogramas y correlación cofenética. Los enlaces *average* y *centroid* tuvieron la correlación cofenética más alta, pero agruparon casi todas las acciones en un solo clúster; por eso se eligió el enlace **complete** con **6 clústeres**.
3. Se compararon ambas particiones y se eligió **K-Means**, porque entrega grupos más equilibrados y más útiles para recomendar estrategias (el segmento mayoritario pasa de 189 acciones en el modelo jerárquico a 101 en K-Means). Ambos identifican un grupo en crisis (13 vs. 12 compañías).

## Resultados clave

Perfil del modelo final (K-Means, 6 clústeres, 266 acciones):

| Clúster | n | Lectura | Price Change | Volatility | ROE | P/E | Ingreso neto medio |
|---------|---|---------|--------------|------------|-----|-----|--------------------|
| 0 | 101 | Compañías medianas, estables y de bajo crecimiento | 2,44 | 1,39 | 18,62 | 21,18 | 723 M |
| 1 | 25 | Líderes: alta rentabilidad y estabilidad | 5,58 | 1,32 | 47,24 | 14,96 | 5.069 M |
| 2 | 61 | Conservadoras, alta liquidez | 6,35 | 1,43 | 16,77 | 23,70 | 813 M |
| 3 | 41 | Precio alto y menor rentabilidad (posible sobrevaloración) | 6,94 | 1,29 | 19,41 | 32,03 | 1.081 M |
| 4 | 25 | Flujo de caja muy negativo y el ROE más bajo del conjunto | 10,82 | 1,64 | 11,96 | 24,69 | 2.012 M |
| 5 | 13 | En crisis: pérdidas, alta volatilidad y P/E elevado | −13,71 | 2,66 | 18,15 | 86,67 | −978 M |

En síntesis: **25 compañías líderes** (ROE medio 47,24 e ingreso neto de 5.069 millones) frente a **13 compañías en crisis** (cambio de precio −13,71, volatilidad 2,66, P/E 86,67 e ingreso neto negativo).

## Recomendaciones de negocio

- Priorizar el clúster de alto desempeño (K-Means 1) para inversiones estables y rentables.
- Evitar el clúster en crisis (K-Means 5) por riesgo financiero severo.
- En el segmento promedio (K-Means 0), buscar oportunidades de crecimiento y productos de mediano plazo.
- Usar el grupo de alta liquidez (K-Means 2) como opción conservadora para clientes más aversos al riesgo.
- Seguir actualizando y validando los modelos con datos nuevos.

## Herramientas

Python, pandas, NumPy, scikit-learn (`KMeans`, `AgglomerativeClustering`, `StandardScaler`, `silhouette_score`), SciPy (`linkage`, `dendrogram`, `cophenet`), Yellowbrick, Matplotlib y Seaborn.

## Estructura del repositorio

```text
.
├── README.md
├── requirements.txt
├── .gitignore
├── stock_data.csv
└── USL_Project.ipynb
```

## Cómo ejecutarlo

```bash
python -m venv .venv
source .venv/bin/activate   # en Windows: .venv\Scripts\activate
pip install -r requirements.txt
```

Abre `USL_Project.ipynb` en Jupyter, VS Code o Google Colab. El notebook ya incluye las salidas ejecutadas (tablas y gráficas), así que se puede revisar el análisis sin volver a correrlo.

**Ruta de los datos.** El notebook se escribió en Google Colab y lee el CSV con una ruta fija de Drive; no la modifiqué, para no alterar el código entregado. La línea aparece **dos veces** (el archivo se vuelve a leer antes del preprocesamiento):

```python
df = pd.read_csv('/content/drive/MyDrive/GREAT LEARNING/6. UNSUPERVISED LEARNING/Proyect/stock_data.csv')
```

Para ejecutarlo en local, cambia esas dos llamadas a:

```python
df = pd.read_csv('./stock_data.csv')
```

Si lo corres en Colab, sube `stock_data.csv` y actualiza la ruta, o coloca el archivo en la ruta original de Drive después de montar el disco.

## Crédito

Project completed as part of the Post-Graduate Program in Data Science and Business Analytics, The University of Texas at Austin / Great Learning (2025).

---

# Trade&Ahead: NYSE stock segmentation

I clustered 266 New York Stock Exchange stocks into 6 investment profiles with K-Means and hierarchical clustering, to support diversified portfolios by risk level.

## Business problem

Trade&Ahead is a financial consultancy that offers personalized investment strategies. It provided price and financial-ratio data for NYSE-listed companies, with three requests: analyze the data, group the stocks by those attributes, and describe each group. The business goal is to build diversified portfolios by identifying stocks with similar profiles and low correlation, reducing downside risk when the market falls.

## Data

File: [`stock_data.csv`](stock_data.csv). **340 companies** and **15 columns**, with no missing values.

Columns: `Ticker Symbol`, `Security` (company name), `GICS Sector`, `GICS Sub Industry`, `Current Price`, `Price Change` (13-week percent change), `Volatility`, `ROE`, `Cash Ratio`, `Net Cash Flow`, `Net Income`, `Earnings Per Share`, `Estimated Shares Outstanding`, `P/E Ratio`, and `P/B Ratio`.

Outliers were treated with a restricted IQR rule (10th and 90th percentiles, bounds at 1.5 × IQR) on all numeric columns. The dataset went from **340 to 266** rows. The **11 numeric features** were then standardized with `StandardScaler`.

## Approach

This notebook does not use PCA. The pipeline is outlier treatment, z-score scaling, and clustering.

1. **K-Means** (`random_state=1`), evaluating *k* from 1 to 14 with the elbow method (average distortion) and from 2 to 14 with the silhouette score. **k = 6** was selected (silhouette 0.1322 at that *k*; distortion drops from 2.489 at *k* = 5 to 2.367 at *k* = 6, then only to 2.348 at *k* = 7).
2. **Agglomerative hierarchical clustering**, comparing *single*, *complete*, *average*, *centroid*, *ward*, and *weighted* linkages (Euclidean distance), with dendrograms and cophenetic correlation. *Average* and *centroid* had the highest cophenetic correlation but placed almost every stock in one cluster, so **complete** linkage with **6 clusters** was used.
3. Both 6-cluster solutions were compared, and **K-Means** was preferred because it produced more balanced, actionable groups (the largest segment falls from 189 stocks in the hierarchical model to 101 in K-Means). Both methods flag a distressed group (13 vs. 12 companies).

## Key results

Profile of the final model (K-Means, 6 clusters, 266 stocks):

| Cluster | n | Reading | Price Change | Volatility | ROE | P/E | Mean net income |
|---------|---|---------|--------------|------------|-----|-----|-----------------|
| 0 | 101 | Mid-sized, stable, low-growth companies | 2.44 | 1.39 | 18.62 | 21.18 | 723 M |
| 1 | 25 | Leaders: highly profitable and stable | 5.58 | 1.32 | 47.24 | 14.96 | 5,069 M |
| 2 | 61 | Conservative, high liquidity | 6.35 | 1.43 | 16.77 | 23.70 | 813 M |
| 3 | 41 | High price and lower profitability (possible overvaluation) | 6.94 | 1.29 | 19.41 | 32.03 | 1,081 M |
| 4 | 25 | Very negative cash flow and the lowest ROE in the set | 10.82 | 1.64 | 11.96 | 24.69 | 2,012 M |
| 5 | 13 | Distressed: losses, high volatility, and elevated P/E | −13.71 | 2.66 | 18.15 | 86.67 | −978 M |

In short: **25 leading companies** (mean ROE 47.24 and net income of 5,069 million) versus **13 distressed companies** (price change −13.71, volatility 2.66, P/E 86.67, and negative net income).

## Business recommendations

- Prioritize the high-performing cluster (K-Means 1) for stable, profitable investments.
- Avoid the distressed cluster (K-Means 5) because of severe financial risk.
- In the average segment (K-Means 0), look for growth opportunities and medium-term products.
- Use the high-liquidity group (K-Means 2) as a conservative option for more risk-averse clients.
- Keep updating and validating the models with new data.

## Tools

Python, pandas, NumPy, scikit-learn (`KMeans`, `AgglomerativeClustering`, `StandardScaler`, `silhouette_score`), SciPy (`linkage`, `dendrogram`, `cophenet`), Yellowbrick, Matplotlib, and Seaborn.

## Repository structure

```text
.
├── README.md
├── requirements.txt
├── .gitignore
├── stock_data.csv
└── USL_Project.ipynb
```

## How to run

```bash
python -m venv .venv
source .venv/bin/activate   # Windows: .venv\Scripts\activate
pip install -r requirements.txt
```

Open `USL_Project.ipynb` in Jupyter, VS Code, or Google Colab. The notebook already contains executed outputs (tables and plots), so the analysis can be reviewed without re-running it.

**Data path.** The notebook was written in Google Colab and reads the CSV from a hardcoded Google Drive path; I left that path unchanged so the delivered code stays intact. The line appears **twice** (the file is re-read before preprocessing):

```python
df = pd.read_csv('/content/drive/MyDrive/GREAT LEARNING/6. UNSUPERVISED LEARNING/Proyect/stock_data.csv')
```

To run it locally, change those two calls to:

```python
df = pd.read_csv('./stock_data.csv')
```

If you run it in Colab, upload `stock_data.csv` and update the path, or place the file at the original Drive path after mounting Drive.

## Credit

Project completed as part of the Post-Graduate Program in Data Science and Business Analytics, The University of Texas at Austin / Great Learning (2025).
