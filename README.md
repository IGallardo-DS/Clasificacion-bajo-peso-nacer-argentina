# Clasificación del bajo peso al nacer en Argentina

## Descripción del proyecto

El bajo peso al nacer es un indicador relevante para el análisis de la salud materno-infantil. Su estudio permite conocer cómo se presenta esta condición en la población, identificar diferencias entre distintos grupos y observar su evolución a lo largo del tiempo.

En este contexto, el presente proyecto propone analizar los registros de nacidos vivos de Argentina correspondientes al período 2005–2022, con el objetivo de estudiar la distribución del bajo peso al nacer e identificar patrones asociados a características maternas, del nacimiento y de la jurisdicción de residencia.

La utilidad de este análisis radica en aportar información que permita comprender mejor cómo se distribuye esta condición en los registros nacionales, reconocer grupos y períodos con distintas proporciones de bajo peso y generar una base de conocimiento que pueda servir como punto de partida para futuros estudios sobre la temática.

Para ello, se desarrolla un proceso de exploración, limpieza y preparación de los datos, seguido de la comparación de distintos modelos de clasificación. Estos modelos buscan distinguir retrospectivamente los registros según la condición de bajo peso al nacer, definida de la siguiente manera:

- `1`: bajo peso al nacer (menos de 2.500 g).
- `0`: sin bajo peso al nacer (2.500 g o más).

El proyecto utiliza información registrada al momento del nacimiento, por lo que su alcance es retrospectivo. En consecuencia, los resultados permiten analizar y clasificar registros existentes, pero no constituyen una herramienta de predicción prenatal ni de diagnóstico clínico.

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
│   └── entrega_2_descripcion_eda_preprocesamiento.md
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

- [Descripción del dataset, análisis exploratorio y preprocesamiento](docs/descripcion_eda_preprocesamiento.md)
- [Diccionario de variables](docs/diccionario_variables.xlsx)

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

Se utiliza una división temporal para evaluar el comportamiento de los modelos sobre años posteriores a los utilizados para entrenarlos:

| Conjunto | Período |
|---|---|
| Entrenamiento | 2005–2018 |
| Prueba | 2019–2022 |

Esta separación permite reservar los registros de 2019–2022 para la evaluación final.

## Modelos previstos

Se propone comparar los siguientes algoritmos de clasificación:

- Regresión Logística.
- Árbol de Decisión.
- Random Forest.

La comparación se realizará utilizando métricas de clasificación adecuadas y teniendo en cuenta la cantidad de nacimientos representada por cada registro.

## Alcance y limitaciones

El proyecto analiza registros de nacimientos y no información individual recopilada durante controles prenatales. Algunas variables, como las semanas de gestación, se conocen al momento del nacimiento y no necesariamente están disponibles antes de este.

Por este motivo, los resultados corresponden a una clasificación retrospectiva de registros y no deben interpretarse como una herramienta de alerta o predicción prenatal.

Además, los registros representan grupos de nacimientos y no necesariamente nacimientos individuales. Por esta razón, se utiliza `nacimientos_cantidad` para considerar la cantidad de nacimientos representada por cada fila.

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
2. **Modelado y evaluación:** entrenamiento, comparación y evaluación de los modelos de clasificación.
