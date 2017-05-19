---
layout: page
title: DataFrame
---

DataFrame Dask se ven y se sienten como marcos de datos de pandas, pero operan en conjuntos de datos más grandes que la memoria utilizando múltiples subprocesos. Dask.dataframe no implementa la interfaz completa de pandas.

El módulo dask.dataframe implementa un DataFrame paralelo bloqueado que imita un subconjunto de las pandas DataFrame. Un dask DataFrame se compone de varios en-memoria pandas DataFrames separados a lo largo del índice. Una operación en un dask DataFrame desencadena muchas operaciones pandas en los pandas constituyentes DataFrames de una manera que es consciente de paralelismo potencial y restricciones de memoria.

### Dask.dataframe copies the pandas API

Debido a que la API dask.dataframe es un subconjunto de la API pandas, debe ser familiar para los usuarios pandas. Hay algunas ligeras alteraciones debido a la naturaleza paralela de dask.


### Programación con hilos

Por defecto, dask.dataframe utiliza el planificador multihilo. Esto expone un cierto paralelismo cuando los pandas o las operaciones numpy subyacentes liberan el GIL. Generalmente los pandas tienen más GIL que NumPy, por lo que las aceleraciones multi-core no son tan pronunciadas para dask.dataframe como lo son para dask.array. Esto está cambiando y el equipo de desarrollo de pandas está trabajando activamente en la liberación de la GIL.

### ¿Qué no funciona?

Dask.dataframe sólo cubre una parte pequeña pero bien utilizada de la API de pandas. Esta limitación es por dos razones:

El API de pandas es enorme
Algunas operaciones son realmente difíciles de hacer en paralelo (por ejemplo, ordenar)

Además, algunas operaciones importantes como set_index funcionan, pero son más lentas que en pandas porque pueden escribir en disco.

### Particiones

Internamente un marco de datos dask se divide en muchas particiones, cada partición es un marco de datos pandas. Estos marcos de datos se dividen verticalmente a lo largo del índice. Cuando nuestro índice está clasificado y conocemos los valores de las divisiones de nuestras particiones entonces podemos ser inteligentes y eficientes.

Por ejemplo, si tenemos un índice de series de tiempo, nuestras particiones pueden dividirse por mes. Todo enero vivirá en una partición, mientras que todo el mes de febrero vivirá en la siguiente. En estos casos, operaciones como loc, groupby y join / merge a lo largo del índice pueden ser mucho más eficientes de lo que de otro modo sería posible en paralelo. Puede ver el número de particiones y divisiones de su marco de datos con los siguientes campos


[back to the homepage]({{ site.baseurl }}).
