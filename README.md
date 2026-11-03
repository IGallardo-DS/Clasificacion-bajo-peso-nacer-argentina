# Clasificación del bajo peso al nacer en Argentina

## Descripción del proyecto

El bajo peso al nacer es un indicador relevante para el análisis de las condiciones de salud al nacimiento. Este proyecto aborda la clasificación retrospectiva de registros de nacimientos de Argentina según la condición de bajo peso al nacer, utilizando datos publicados por la Dirección de Estadísticas e Información de Salud (DEIS), correspondientes al período 2005–2022.

A partir de características maternas y del nacimiento, se busca identificar patrones y grupos poblacionales que históricamente presentan distintas proporciones de bajo peso, y evaluar la capacidad de un algoritmo de aprendizaje automático para clasificar los registros según esta condición.

El proyecto también busca analizar si los patrones identificados en períodos anteriores se mantienen en registros de años posteriores, mediante una división temporal de los datos.

## Objetivo general

Analizar y clasificar retrospectivamente los registros de nacimientos de Argentina correspondientes al período 2005–2022 según la condición de bajo peso al nacer, utilizando características maternas y del nacimiento, con el propósito de identificar patrones descriptivos y evaluar el desempeño de un modelo de aprendizaje automático sobre registros de períodos posteriores.

## Definición de la variable objetivo

Se considera bajo peso al nacer cuando el peso registrado es inferior a 2.500 gramos.

- **1 – Bajo peso:** peso al nacer inferior a 2.500 gramos.
- **0 – Sin bajo peso:** peso al nacer igual o superior a 2.500 gramos.

## Fuente de datos

Los datos utilizados provienen de los registros de nacidos vivos publicados por la Dirección de Estadísticas e Información de la Salud (DEIS) de Argentina.

- **Fuente:** [Nacidos vivos registrados en la República Argentina entre los años 2005 y 2022](https://datos.gob.ar/)
- **Período analizado:** 2005–2022.
- **Formato original:** archivo CSV comprimido en ZIP.

La documentación de las variables se encuentra en el archivo `docs/diccionario_variables.xlsx`.

## Estructura del proyecto

```text
clasificacion-bajo-peso-nacer-argentina/
│
├── data/
│   ├── raw/          # Datos originales
│   ├── interim/      # Datos intermedios
│   └── processed/    # Datos procesados
│
├── docs/
│   ├── diccionario_variables.xlsx
│   └── descripcion_eda_preprocesamiento.md
│
├── notebooks/
│   ├── 01_eda_preprocesamiento.ipynb
│   └── 02_modelado.ipynb
│
├── models/           # Modelos entrenados
├── reports/          # Informes y visualizaciones
│
└── README.md
```

## Documentación

- [Descripción del dataset, análisis exploratorio y preprocesamiento](https://github.com/IGallardo-DS/Clasificacion-bajo-peso-nacer-argentina/blob/main/docs/descripcion_eda_preprocesamiento.md)
- [Diccionario de variables](https://github.com/IGallardo-DS/Clasificacion-bajo-peso-nacer-argentina/blob/main/docs/diccionario_variables.xlsx)

## Preparación de los datos

Durante la preparación se realizaron las siguientes tareas:

1. Carga e inspección inicial del conjunto de datos.
2. Exclusión de los registros cuyo peso al nacer figura como `Sin especificar`, ya que no permiten determinar la clase objetivo.
3. Normalización de las categorías de las variables.
4. Revisión de valores faltantes y consistencia de los registros.
5. Creación de la variable objetivo `bajo_peso`.
6. Exploración de la distribución de la variable objetivo y de su relación con las demás variables.

Los registros excluidos por no tener un peso especificado fueron 26.432, que representan 145.857 nacimientos.

Luego de esta exclusión, el conjunto quedó conformado por 520.531 registros, que representan 12.311.460 nacimientos.

La variable `nacimientos_cantidad` indica la cantidad de nacimientos representados por cada registro y se conserva como ponderador para los análisis y las evaluaciones correspondientes. No se utiliza como variable predictora.

## Análisis exploratorio

El análisis exploratorio contempla la distribución del bajo peso al nacer y su comportamiento según distintas características disponibles en los registros:

- Año de nacimiento.
- Jurisdicción de residencia materna.
- Grupo de edad de la madre.
- Nivel de instrucción materna.
- Tipo de parto (simple o múltiple).
- Semanas de gestación.
- Sexo del nacido vivo.

Los resultados se analizan de forma descriptiva, sin atribuir relaciones causales a las diferencias observadas.

## Preparación para el modelado

Para la clasificación se consideran como variables predictoras:

- `jurisdicion_residencia_nombre`
- `edad_madre_grupo`
- `instruccion_madre`
- `tipo_de_parto_nombre`
- `semana_gestacion`
- `Sexo`

La variable `intervalo_peso_al_nacer` no se utiliza como predictor porque es la variable de la que se deriva directamente la clase objetivo. Tampoco se utiliza `nacimientos_cantidad` como predictor, ya que funciona como ponderador.

Las variables categóricas se transforman mediante One-Hot Encoding. El preprocesador se ajusta exclusivamente con los datos de entrenamiento y luego se aplica al conjunto de prueba, evitando utilizar información de este último durante el ajuste.

## División de los datos

Se utiliza una división temporal para evaluar el comportamiento del modelo sobre años posteriores a los utilizados para entrenarlo:

| **Conjunto**  | **Período** |
| ------------- | ----------- |
| Entrenamiento | 2005–2018   |
| Prueba        | 2019–2022   |

Esta separación permite reservar los registros de 2019–2022 para la evaluación final.

## Modelo de clasificación

Se utiliza **Random Forest**, un algoritmo de aprendizaje automático basado en un conjunto de árboles de decisión. El modelo combina las predicciones de múltiples árboles para obtener una clasificación final.

La selección de sus hiperparámetros se realiza mediante una búsqueda dentro del conjunto de entrenamiento, utilizando validación interna. De esta manera, los registros correspondientes a 2019–2022 permanecen reservados para la evaluación final.

El desempeño se analiza mediante métricas de clasificación adecuadas para una variable objetivo desbalanceada, considerando también la cantidad de nacimientos representada por cada registro mediante `nacimientos_cantidad`.

## Alcance y limitaciones

El proyecto tiene un enfoque retrospectivo y utiliza características registradas al momento del nacimiento. En particular, las semanas de gestación representan la duración de la gestación al momento del nacimiento, por lo que no se plantea una predicción prenatal.

Los resultados permiten identificar asociaciones y patrones presentes en los registros, pero no establecer relaciones causales ni validar una herramienta de diagnóstico o intervención clínica.

Además, los datos se encuentran agrupados y cada fila representa una cantidad determinada de nacimientos, indicada en la variable `nacimientos_cantidad`. Esta variable se conserva como ponderador para considerar la cantidad de nacimientos representada por cada registro, pero no se utiliza como variable predictora.

Por estas características, los resultados deben interpretarse dentro del alcance de una clasificación retrospectiva de registros y no como una herramienta de alerta o intervención sanitaria.

## Tecnologías utilizadas

- Python
- Pandas
- NumPy
- Matplotlib
- Seaborn
- Scikit-learn
- Jupyter Notebook

## Organización del trabajo

El proyecto se desarrolla en dos etapas principales:

1. **Análisis exploratorio y preprocesamiento:** inspección, limpieza, análisis descriptivo, selección de variables, división temporal y transformación de los datos.
2. **Modelado y evaluación:** búsqueda de hiperparámetros, entrenamiento del modelo Random Forest y evaluación de su desempeño sobre los registros del período 2019–2022.
