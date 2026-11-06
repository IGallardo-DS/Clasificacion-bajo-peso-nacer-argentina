# Clasificación retrospectiva de registros de nacimientos según la condición de bajo peso al nacer en Argentina

Proyecto de aprendizaje automático desarrollado a partir de registros históricos de nacidos vivos de Argentina correspondientes al período 2005–2022, publicados por la Dirección de Estadísticas e Información de Salud (DEIS).

El objetivo es desarrollar y evaluar un modelo capaz de clasificar registros de nacimientos según la presencia de bajo peso al nacer, utilizando características maternas y del nacimiento.

## Resumen

El presente proyecto tuvo como objetivo desarrollar y evaluar un modelo de aprendizaje automático capaz de clasificar registros de nacimientos en Argentina según la presencia de bajo peso al nacer, definido como un peso inferior a 2.500 gramos. Para ello, se utilizaron registros históricos de nacidos vivos correspondientes al período 2005–2022, provenientes de la Dirección de Estadísticas e Información de Salud (DEIS). El trabajo incluyó un análisis exploratorio de los datos y la selección de características maternas y del nacimiento para la etapa de modelado. Se implementó un modelo Random Forest y se compararon configuraciones con y sin balanceo de clases, utilizando una validación temporal para seleccionar el modelo definitivo. Su desempeño se evaluó sobre registros de los años 2019–2022, ponderando los resultados según la cantidad de nacimientos representada por cada registro. El modelo balanceado obtuvo un Recall de 0,70 y un F1-score de 0,59 para la clase de bajo peso, con una Precision de 0,52. Durante el período de prueba, el Recall se mantuvo relativamente estable, mientras que la Precision y el F1-score mostraron una mejora gradual. En conjunto, los resultados indican que el modelo permite identificar una proporción importante de los registros con bajo peso, aunque presenta limitaciones en la precisión de sus clasificaciones. Por ello, se plantea como una herramienta de análisis retrospectivo y no como un instrumento de diagnóstico o intervención clínica.

## Abstract

This study aimed to develop and evaluate a machine learning model for classifying birth records in Argentina according to low birth weight (LBW), defined as a birth weight below 2,500 grams. Historical live birth records from 2005 to 2022, obtained from the Dirección de Estadísticas e Información de Salud (DEIS), were analyzed. An exploratory data analysis was conducted, and maternal and birth-related features were selected for model development. A Random Forest classifier was implemented, and configurations with and without class balancing were compared. Temporal validation was employed to select the final model, whose performance was subsequently evaluated using records from 2019 to 2022. Evaluation metrics were weighted according to the number of births represented by each record. For the LBW class, the balanced model achieved a Recall of 0.70, an F1-score of 0.59, and a Precision of 0.52. Recall remained relatively stable throughout the test period, while Precision and F1-score showed a gradual increase. Overall, the findings indicate that the model was able to identify a substantial proportion of LBW records, although its classification precision remained limited. Therefore, the proposed model is intended as a tool for retrospective data analysis rather than for clinical diagnosis or intervention.

## Objetivos

- Analizar los registros históricos de nacidos vivos de Argentina entre 2005 y 2022.
- Realizar un análisis exploratorio para conocer la distribución de las variables y su relación descriptiva con el bajo peso al nacer.
- Preparar los datos para su utilización en un modelo de aprendizaje automático.
- Desarrollar un clasificador Random Forest y comparar configuraciones con y sin balanceo de clases.
- Seleccionar los hiperparámetros mediante validación temporal.
- Evaluar el desempeño del modelo sobre un período posterior al utilizado durante el entrenamiento.

## Datos utilizados

La fuente principal es la base de datos de nacidos vivos registrados en la República Argentina entre 2005 y 2022, publicada por la Dirección de Estadísticas e Información de Salud (DEIS).

Los datos se encuentran agrupados por combinaciones de características, por lo que cada fila representa un conjunto de nacimientos y no un nacimiento individual.

La variable `nacimientos_cantidad` indica cuántos nacimientos representa cada registro. Se utilizó como ponderación durante el entrenamiento y la evaluación, de modo que las métricas reflejen la cantidad de nacimientos y no solamente la cantidad de filas.

### Preparación de los datos

Durante el preprocesamiento se realizaron, entre otras, las siguientes tareas:

- Eliminación de registros sin información válida sobre el peso al nacer.
- Normalización de categorías y códigos de variables.
- Agregación de registros duplicados después de normalizar las categorías.
- Creación de la variable objetivo `bajo_peso`, definida como 1 para pesos inferiores a 2.500 gramos y 0 para pesos iguales o superiores a ese valor.
- Selección de variables predictoras maternas y del nacimiento.
- Separación temporal de los datos y codificación de variables categóricas.

Luego de la limpieza y agregación, el conjunto quedó conformado por **520.422 registros**, que representan **12.311.460 nacimientos**.

La descripción detallada del conjunto de datos, las variables y las etapas de preprocesamiento se encuentra en [`docs/descripcion_eda_preprocesamiento.md`](docs/descripcion_eda_preprocesamiento.md).

## Metodología

### Análisis exploratorio y preprocesamiento

Se realizó un análisis exploratorio para estudiar la distribución de los nacimientos, la frecuencia de bajo peso y el comportamiento de las variables seleccionadas. Las comparaciones descriptivas se ponderaron según la cantidad de nacimientos representada por cada registro.

### División temporal

Para evaluar el comportamiento del modelo sobre datos posteriores a los utilizados durante el entrenamiento, se realizó una separación cronológica:

| Conjunto | Período | Registros |
|---|---:|---:|
| Entrenamiento | 2005–2018 | 419.765 |
| Prueba | 2019–2022 | 100.657 |
| Total | 2005–2022 | 520.422 |

El conjunto de prueba se mantuvo separado durante la selección de hiperparámetros.

### Modelo y selección

Se implementó un clasificador **Random Forest**. Se compararon configuraciones con y sin balanceo de clases y se seleccionó la alternativa balanceada.

La búsqueda de hiperparámetros se realizó mediante validación cruzada temporal sobre los datos de entrenamiento, utilizando el F1-score como métrica principal.

### Evaluación

El modelo definitivo se evaluó sobre el período 2019–2022. Las métricas se ponderaron utilizando `nacimientos_cantidad`.

Se consideraron Precision, Recall y F1-score para analizar el desempeño en la clase de bajo peso al nacer.

## Resultados

Para la clase **Bajo peso**, el modelo obtuvo los siguientes resultados sobre el conjunto de prueba:

| Métrica | Resultado |
|---|---:|
| Precision | 0,52 |
| Recall | 0,70 |
| F1-score | 0,59 |

El Recall indica que el modelo identificó aproximadamente el 70 % de los nacimientos clasificados con bajo peso en el conjunto de prueba. Sin embargo, la Precision de 0,52 evidencia que una proporción considerable de las predicciones positivas no correspondió a casos de bajo peso.

Al analizar los resultados por año, el Recall se mantuvo relativamente estable, mientras que la Precision y el F1-score mostraron una mejora gradual entre 2019 y 2022.

Estos resultados deben interpretarse dentro del alcance retrospectivo del proyecto y teniendo en cuenta las limitaciones de los datos y del modelo.

## Alcance y limitaciones

Este proyecto aborda una clasificación retrospectiva de registros históricos. No busca realizar predicciones prenatales, establecer diagnósticos ni proponer intervenciones clínicas.

Es importante considerar que:

- Los datos están agrupados y cada fila representa una cantidad variable de nacimientos.
- La variable `semana_gestacion` se registra al momento del nacimiento. Por lo tanto, su utilización no implica que el modelo pueda realizar estimaciones durante una consulta prenatal.
- Las relaciones observadas entre las variables son descriptivas y no permiten establecer causalidad.
- El desempeño obtenido presenta limitaciones, especialmente en la Precision de la clase de bajo peso.
- La prueba con registros sintéticos se utiliza únicamente para comprobar el funcionamiento del flujo de transformación y clasificación; no constituye una evaluación adicional del rendimiento.

## Estructura del repositorio

```text
clasificacion-bajo-peso-nacer-argentina/
│
├── data/
│   ├── raw/
│   │   └── nacimientos_argentina_2005_2022.zip
│   └── interim/
│       ├── datos_intermedios_modelado.zip
│       └── preprocesador.joblib
│
├── docs/
│   ├── diccionario_variables.xlsx
│   └── descripcion_eda_preprocesamiento.md
│
├── notebooks/
│   ├── 01_eda_preprocesamiento.ipynb
│   └── 02_modelado.ipynb
│
├── references/
│   └── referencias.md
│
├── reports/
│   └── resultados_modelo/
│       ├── matriz_confusion.png
│       ├── metricas_por_anio.png
│       └── resumen_resultados.md
│
├── environment.yml
├── requirements.txt
└── README.md
```

## Reproducibilidad

El entorno de trabajo se configuró utilizando **Conda**. Para recrearlo, se debe clonar el repositorio y ejecutar:

```bash
conda env create -f environment.yml
conda activate clasificacion-bajo-peso
```

Luego, se puede registrar el entorno como kernel de Jupyter:

```bash
python -m ipykernel install --user --name clasificacion-bajo-peso --display-name "Python (clasificacion-bajo-peso)"
```

También se incluye `requirements.txt` para instalar las dependencias mediante pip en un entorno Python compatible:

```bash
pip install -r requirements.txt
```

### Orden de ejecución

Los notebooks deben ejecutarse en el siguiente orden:

1. `01_eda_preprocesamiento.ipynb`: análisis exploratorio, limpieza, preparación de variables, división temporal y generación de los archivos intermedios.
2. `02_modelado.ipynb`: comparación de configuraciones, búsqueda de hiperparámetros, entrenamiento, evaluación y prueba demostrativa con datos sintéticos.

## Herramientas y tecnologías

- Python
- Pandas
- NumPy
- Matplotlib
- Seaborn
- Scikit-learn
- Joblib
- Jupyter Notebook
- Conda

## Referencias

Las fuentes de datos, la documentación de las variables y la definición de bajo peso al nacer se encuentran detalladas en [`references/referencias.md`](references/referencias.md).

## Presentación del proyecto

El video de presentación se incorporará una vez finalizada la grabación.

**Enlace al video:** `PENDIENTE`

## Licencia

El código de este proyecto se distribuye bajo la licencia MIT. La licencia del conjunto de datos original se mantiene de acuerdo con las condiciones establecidas por su fuente de publicación.
