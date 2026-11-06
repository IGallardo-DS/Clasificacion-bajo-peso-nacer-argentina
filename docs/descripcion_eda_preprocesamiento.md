# Descripción del dataset, análisis exploratorio y preprocesamiento

## 1. Descripción general del conjunto de datos

Para el desarrollo del proyecto se utilizó el conjunto de datos **“Nacidos vivos registrados en la República Argentina entre los años 2005 y 2022”**, publicado por la Dirección de Estadísticas e Información de la Salud (DEIS).

El dataset contiene información agrupada sobre nacimientos registrados en Argentina durante el período 2005–2022. Cada fila representa una combinación de características y categorías, mientras que la variable `nacimientos_cantidad` indica la cantidad de nacimientos correspondientes a esa combinación.

El conjunto original está compuesto por 546.963 registros y 12 variables.

### 1.1. Variables del conjunto de datos

Las variables disponibles incluyen:

- Año del nacimiento.
- Código y nombre de la jurisdicción de residencia materna.
- Grupo de edad de la madre.
- Nivel de instrucción materna.
- Tipo de parto.
- Semanas de gestación.
- Intervalo de peso al nacer.
- Código y categoría de sexo.
- Cantidad de nacimientos representados por cada registro.

Para consultar las definiciones y categorías originales de las variables se utiliza el [diccionario de variables](diccionario_variables.xlsx), incluido en la carpeta `docs/` del proyecto.

## 2. Preparación y limpieza de los datos

### 2.1. Exclusión de registros sin peso al nacer especificado

Para construir la variable objetivo es necesario contar con información sobre el peso al nacer. Por este motivo, se excluyeron los registros cuya categoría de peso figura como `9.Sin especificar`.

La exclusión comprendió:

- 26.432 registros.
- 145.857 nacimientos representados.

Luego de este procedimiento, el conjunto quedó conformado por 520.531 registros, que representan 12.311.460 nacimientos.

### 2.2. Inspección de valores faltantes

Se revisó la estructura del conjunto de datos y la presencia de valores faltantes.

Se detectaron valores ausentes en la variable correspondiente al nombre de la jurisdicción de residencia materna. Al contrastarlos con el código de jurisdicción, se identificó que correspondían al código 98, definido en el diccionario como `Otro país`.

Por este motivo, se completaron esos valores con la categoría `Otro país`. También se normalizó la categoría `Sin Información` como `Lugar no especificado`, de acuerdo con la codificación utilizada en el diccionario.

### 2.3. Normalización de la variable sexo

Se revisaron las categorías de la variable `Sexo` y se normalizaron sus etiquetas para mantener una denominación uniforme.

En los datos originales se encontraron las categorías `masculino`, `femenino`, `indeterminado` y `desconocido`. Para el análisis, las categorías `indeterminado` (código 3) y `desconocido` (código 9) se unificaron bajo la denominación `Sin especificar`.

Se conservó el código 9 como identificador de la categoría resultante. Esta unificación corresponde a una decisión de normalización para el análisis y no implica que el código 9 sea inválido, ya que figura como una categoría válida en el diccionario de variables.

La variable quedó conformada por tres categorías:

- `1`: Varón.
- `2`: Mujer.
- `9`: Sin especificar.

Como consecuencia de esta normalización, se pierde la distinción original entre las categorías `indeterminado` y `desconocido`.

### 2.4. Normalización del tipo de parto

Se revisó la variable `tipo_de_parto_nombre`, que identifica si el nacimiento corresponde a un parto simple o múltiple.

Se mantuvieron las categorías correspondientes a:

- Simple.
- Múltiple.
- Sin especificar.

Es importante aclarar que esta variable no identifica la vía de nacimiento (vaginal o cesárea), sino si se trata de un nacimiento simple o múltiple.

### 2.5. Revisión de las variables categóricas restantes

Se inspeccionaron las categorías de las variables de edad materna, instrucción materna, semanas de gestación y jurisdicción de residencia.

Se verificó que sus categorías fueran consistentes con las definiciones del diccionario y se conservaron las categorías sin información cuando correspondía, sin reemplazarlas por valores inventados.

### 2.6. Validación de la cantidad de nacimientos y agregación de registros

Se verificó la variable `nacimientos_cantidad`, que representa la cantidad de nacimientos correspondiente a cada registro.

Esta variable resulta importante porque el dataset no contiene una fila por cada nacimiento individual, sino registros agrupados. Por lo tanto, se utiliza como ponderador en los análisis descriptivos y en las evaluaciones del modelo.

No se incorpora como variable predictora.

Luego de normalizar las categorías de sexo, se realizó una nueva agregación de las combinaciones que pasaron a representar la misma categoría. De esta manera, se consolidaron los registros correspondientes a combinaciones duplicadas.

Como resultado, el conjunto quedó conformado por **520.422 registros**, manteniendo los **12.311.460 nacimientos** representados.

## 3. Análisis exploratorio de datos

### 3.1. Construcción de la variable objetivo

Se construyó la variable binaria `bajo_peso` a partir del intervalo de peso al nacer.

Se estableció la siguiente clasificación:

- `1`: bajo peso al nacer, cuando el peso es inferior a 2.500 gramos.
- `0`: sin bajo peso al nacer, cuando el peso es igual o superior a 2.500 gramos.

Los registros sin peso especificado fueron excluidos previamente, por lo que no se asignó una clase a partir de información desconocida.

### 3.2. Distribución de la variable objetivo

Luego de construir la variable objetivo, se analizó su distribución considerando la cantidad de nacimientos representada por cada registro.

Los resultados fueron:

| Categoría | Nacimientos | Porcentaje |
|---|---:|---:|
| Sin bajo peso | 11.413.544 | 92,71 % |
| Bajo peso | 897.916 | 7,29 % |
| **Total** | **12.311.460** | **100 %** |

Se observa que la proporción de nacimientos con bajo peso es considerablemente menor que la correspondiente a los nacimientos sin bajo peso. Esta diferencia se tuvo en cuenta durante el desarrollo y la evaluación del modelo de clasificación.

### 3.3. Evolución anual del bajo peso al nacer

Se calculó el porcentaje de nacimientos con bajo peso para cada año del período analizado.

Los valores se mantuvieron en general alrededor del 7,2 %. En 2022 se observó el porcentaje más elevado del período, con un 7,89 %, mientras que en 2019 se registró un 7,62 %.

Estos resultados describen la evolución de la proporción de bajo peso en los registros analizados, sin establecer relaciones causales con otros acontecimientos o factores.

### 3.4. Distribución según jurisdicción

Se analizó el porcentaje de nacimientos con bajo peso según la jurisdicción de residencia materna.

Entre las jurisdicciones argentinas, se observaron porcentajes relativamente más elevados en Chaco, Tucumán y Santa Fe. Por otro lado, Chubut, Santa Cruz y Tierra del Fuego presentaron porcentajes relativamente menores.

Las categorías `Otro país` y `Lugar no especificado` se mantuvieron identificadas por separado, ya que no corresponden a jurisdicciones provinciales comparables con el resto.

### 3.5. Distribución según edad materna

Se analizó la proporción de bajo peso en los distintos grupos de edad materna.

Los porcentajes más elevados se observaron en los grupos de 45 años o más, menores de 15 años y de 40 a 44 años. En cambio, los porcentajes más bajos correspondieron a los grupos de 20 a 24 y de 25 a 29 años.

Estas diferencias representan asociaciones descriptivas dentro de los registros y no permiten establecer relaciones causales.

### 3.6. Distribución según nivel de instrucción materna

Se comparó el porcentaje de bajo peso entre las categorías de instrucción materna.

El porcentaje más elevado se observó en la categoría `Sin instrucción`, mientras que el más bajo correspondió a `Secundario completo`.

No se identificó una variación estrictamente progresiva entre los distintos niveles educativos, por lo que los resultados se describen según las categorías observadas sin establecer una tendencia general.

### 3.7. Distribución según tipo de parto

Se observó una diferencia marcada entre los nacimientos simples y múltiples.

El porcentaje de bajo peso fue de 58,33 % para los nacimientos múltiples y de 6,32 % para los simples. La categoría `Sin especificar` presentó un 10,65 %.

Esta diferencia muestra una asociación descriptiva importante entre el tipo de parto y la condición de bajo peso al nacer.

### 3.8. Distribución según semanas de gestación

Se analizó la proporción de bajo peso según los intervalos de semanas de gestación.

Los porcentajes más elevados se observaron en los intervalos de menor duración gestacional. Por ejemplo, las categorías de 22 a 23 semanas y de 24 a 27 semanas presentaron porcentajes superiores al 99 %.

En los intervalos de 37 a 41 semanas y de 42 semanas o más, los porcentajes fueron considerablemente menores.

Debe tenerse en cuenta que las semanas de gestación corresponden a la duración registrada al momento del nacimiento. Por lo tanto, esta variable puede utilizarse en una clasificación retrospectiva, pero no representa información necesariamente disponible durante una consulta prenatal.

### 3.9. Distribución según sexo

Se comparó el porcentaje de bajo peso entre las categorías de sexo.

Se observó un porcentaje de bajo peso de 7,75 % para la categoría `Mujer` y de 6,86 % para `Varón`. La categoría `Sin especificar` presentó un 7,81 %.

Las diferencias se describen de manera comparativa, sin atribuirles una explicación causal.

## 4. Preparación para el modelado

### 4.1. Selección de variables

Para la etapa de clasificación se seleccionaron las siguientes variables predictoras:

- `jurisdicion_residencia_nombre`
- `edad_madre_grupo`
- `instruccion_madre`
- `tipo_de_parto_nombre`
- `semana_gestacion`
- `Sexo`

La variable `intervalo_peso_al_nacer` no se incorpora como predictora porque de ella se deriva directamente la variable objetivo. Incluirla produciría fuga de información (*data leakage*).

La variable `nacimientos_cantidad` se conserva como ponderador y no como predictor. Por su parte, `anio` se utiliza para realizar la separación temporal de los datos.

### 4.2. Separación temporal de entrenamiento y prueba

Se realizó una división temporal del conjunto de datos:

| Conjunto | Período | Registros |
|---|---|---:|
| Entrenamiento | 2005–2018 | 419.765 |
| Prueba | 2019–2022 | 100.657 |
| **Total** | **2005–2022** | **520.422** |

Esta separación permite utilizar los años más recientes como conjunto de prueba y reservarlos para la evaluación final del modelo.

No existe superposición de años entre ambos conjuntos.

### 4.3. Separación de predictores, objetivo y ponderadores

Se organizaron los datos en componentes independientes:

- Variables predictoras (`X`).
- Variable objetivo (`y`).
- Ponderadores (`nacimientos_cantidad`).

Se comprobó que los componentes mantuvieran la correspondencia entre sus registros tanto en entrenamiento como en prueba.

### 4.4. Identificación de variables categóricas y numéricas

Las seis variables predictoras seleccionadas son categóricas. Se identificaron sus categorías para definir el procedimiento de transformación que se utilizaría posteriormente.

### 4.5. Configuración del preprocesamiento

Se configuró un procedimiento de transformación mediante One-Hot Encoding, utilizando `OneHotEncoder` y `ColumnTransformer` de Scikit-learn.

Esta transformación permite representar las categorías mediante columnas binarias, de manera que puedan ser utilizadas por los algoritmos de clasificación.

Se estableció `handle_unknown="ignore"` para permitir el procesamiento de categorías que pudieran aparecer en el conjunto de prueba y que no hayan sido observadas durante el entrenamiento.

### 4.6. Ajuste y transformación de los datos

El preprocesador se ajustó exclusivamente con las variables del conjunto de entrenamiento. Posteriormente, se aplicó la transformación al conjunto de prueba utilizando el mismo preprocesador.

Como resultado, se generaron 57 columnas transformadas para ambos conjuntos:

| Conjunto | Registros | Variables transformadas |
|---|---:|---:|
| Entrenamiento | 419.765 | 57 |
| Prueba | 100.657 | 57 |

Este procedimiento evita que la información del conjunto de prueba intervenga en el ajuste de las transformaciones.

### 4.7. Validación de las variables transformadas

Se inspeccionaron los nombres de las columnas generadas para verificar que las categorías de las variables originales estuvieran representadas correctamente.

Se comprobó que el preprocesamiento generara 57 columnas y que ambos conjuntos conservaran la misma estructura.

### 4.8. Validación de la estructura de los conjuntos

Se verificó que las variables predictoras transformadas, las variables objetivo y los ponderadores tuvieran la misma cantidad de registros.

El conjunto de entrenamiento contiene 419.765 registros en cada componente, mientras que el conjunto de prueba contiene 100.657.

Esta comprobación permite confirmar que se mantiene la correspondencia entre las variables predictoras, la clase objetivo y la cantidad de nacimientos representada por cada registro.

### 4.9. Exportación de los datos intermedios

Finalmente, los conjuntos transformados, las variables objetivo y los ponderadores se exportaron en archivos CSV agrupados dentro de un archivo ZIP.

También se guardó el preprocesador ajustado para reutilizarlo en la siguiente etapa del proyecto, dedicada al entrenamiento y evaluación del modelo de clasificación.

Los archivos generados son:

- `datos_intermedios_modelado.zip`: contiene los conjuntos de entrenamiento y prueba, sus respectivas variables objetivo, ponderadores y años.
- `preprocesador.joblib`: contiene el preprocesador ajustado con los datos de entrenamiento.

Estos archivos permiten reutilizar las transformaciones realizadas sin volver a ajustarlas sobre el conjunto de prueba.

## 5. Consideraciones finales

El análisis exploratorio permitió describir la distribución del bajo peso al nacer y observar diferencias entre las categorías de las variables disponibles.

La preparación de los datos permitió construir la variable objetivo, normalizar las categorías, consolidar registros duplicados, seleccionar las variables predictoras y generar los conjuntos de entrenamiento y prueba mediante una separación temporal.

El resultado de esta etapa es un conjunto de datos preparado para el entrenamiento y la evaluación del modelo de clasificación, manteniendo la separación entre los datos utilizados para el entrenamiento y aquellos reservados para la evaluación final.
