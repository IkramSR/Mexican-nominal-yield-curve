# Análisis y Pronóstico de la Curva de Rendimientos Mexicana 

El proyecto explora la estructura latente de las tasas de interés en México y evalúa el poder predictivo de modelos estructurales frente a la inercia del mercado.

## Estructura del Repositorio

* **`mexican_yield_curve.ipynb`**: Jupyter Notebook con el código completo. Incluye el *data wrangling*, el Análisis de Componentes Principales (PCA) y  pronóstico comparando un *Random Walk* vs. el modelo paramétrico de Nelson-Siegel + AR(1).
* **`Brief_Ikram_Saucedo_Analisis_de_la_Curva.pdf`**: Resume los hallazgos empíricos, la comparativa de errores (RMSE) a 3 y 12 meses, y el impacto de negocio del modelo.
* **`log_modelling_decisions.md`**: Bitácora técnica, justifica las decisiones críticas tomadas durante la prueba (ej. método de imputación de nulos) y documenta las alternativas descartadas.
* **`presentacion_ikram_Saucedo.pdf`**: Diapositivas de apoyo visual

> **Nota:** El archivo original de datos (`banxico_auction_rates_monthly.csv`) fue omitido del repositorio de forma intencional.
