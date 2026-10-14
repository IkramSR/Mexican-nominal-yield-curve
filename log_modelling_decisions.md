# Log of Modelling Decisions - Junior Statistician Exercise
**Candidata:** Ikram Saucedo

Las decisiones tomadas durante el análisis así como alternativas descartadas y justificaciones teóricas en cada paso se enlistan a continuación:

## 1. Preparación de datos y análisis exploratorio

*   **Decisión:** Se transformó dataset a formato ancho usando `df.pivot()` con la fecha parseada como índice `datetime`.
    *   *Justificación:* `pivot` causaría error si existen registros duplicados por fecha/instrumento, a diferencia de `pivot_table` que promediaría errores silenciosamente. Permite tener los 9 plazos como variables continuas.

*   **Decisión:** Imputación de datos faltantes utilizando **Forward Fill** (`ffill`).
    *   *Justificación:* Es un supuesto estandar que un bono no subastado se mantiene en su último valor conocido, esta decisión se basó en prácticas comunmente aceptadas.
    *   *Alternativas descartadas:* 
        1. *Eliminar filas con NaNs:* Se habría eliminado aproximadamente el 83% de los datos.
        2. *Interpolación temporal (lineal/spline):* Se rechazó por introducir Sesgo de Anticipación, usando información del futuro para imputar el pasado.
    *   *Incertidumbre / Limitación:* Se detectó que el bono a 10 años tuvo rachas de hasta 4 meses sin información. Se asumió estabilidad durante ese periodo. Adicionalmente, se usó un `bfill` residual al inicio de la serie para los bonos de 20 y 30 años para poder desarrollar el algoritmo PCA.


## 2. Pregunta 1: Estructura de la Curva

*   **Decisión:** Uso de **Análisis de Componentes Principales (PCA)** sobre los datos estandarizados.
    *   *Justificación:* PCA es una técnica de análisis multivariado que demuestra empíricamente que la dimensión efectiva del espacio de curvas de rendimiento es muy baja, típicamente 3 componentes principales (nivel, pendiente y curvatura) explican más del 98% de la varianza total además de que no es necesario tomar en cuenta supuestos previos. Se estandarizó la matriz para evitar que los plazos con mayor volatilidad nominal dominaran la extracción de los componentes.


## 3. Pregunta 2: Baseline y Pronóstico

*   **Decisión:** Se seleccionó **Caminata Aleatoria (Random Walk)** como baseline.
    *   *Alternativas descartadas:* Promedio histórico o regresiones simples por cada tasa.
    *   *Justificación:* Las tasas de interés cuentan con mucha inercia, es raro encontrar que den saltos abruptos de un mes a otro. Una de las suposiciones presentes en finanzas es que el mercado ya ajustó los precios de hoy con la información disponible. Por lo tanto, el mejor pronóstico (el más simple) es asumir que la tasa en el futuro será igual a la tasa de hoy. Adicional, usar simplemente el promedio histórico no daría una representación apegada a los datos actuales.
    
*   **Decisión:** Pronosticar el nivel exacto de la tasa en lugar de únicamente el cambio mensual.
    *   *Justificación:* Facilita la comparabilidad del RMSE y la evaluación de la curva completa reconstruida ya que al pronosticar el nivel exacto la métrica de error queda en las mismas unidades (porcentajes) que las tasas reales, haciendo mucho más intuitiva la comparación del modelo Estructurado contra el Baseline.


## 4. Pregunta 3: Pronóstico Estructural
*   **Decisión:** Reducción de dimensionalidad mediante el modelo paramétrico de **Nelson-Siegel** en lugar de usar PCA y pronóstico mediante un modelo autorregresivo simple **AR(1)** en lugar de VAR.
    *   *Alternativas descartadas:* Proyectar los autovectores del PCA directamente mediante un modelo Vectores Autorregresivos (VAR).
    *   *Justificación:* 
        1) *Nelson-Siegel en lugar de PCA:* Después de revisar la literatura (Diebold & Li, 2005), proyectar sobre PCA empírico no garantiza una curva suave y continua a futuro, y no permite interpolar para plazos no observados a diferencia de Nelson-Siegel que sí muestra coherencia macroeconómica. Se fijó el hiperparámetro $\lambda = 0.0609$ ya que es el estándar para optimizar la curvatura a 30 meses.
        2) *AR(1) en lugar de VAR:* Se optó por pronosticar los 3 factores ($\beta_1, \beta_2, \beta_3$) de forma independiente usando procesos AR(1). Los modelos VAR en horizontes largos con muestras relativamente pequeñas como la nuestra de 240 observaciones, tienden a sobreajustarse y emular el ruido de los datos en lugar de pronóstocos reales. 
