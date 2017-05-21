---
layout: page
title: Dask
---

Dask es una biblioteca de computación paralela flexible para la computación analítica.

Dask se compone de dos componentes:

* Programación dinámica de tareas optimizada para el cálculo. Esto es similar a Airflow, Luigi, Celery, or Make, pero optimizado para cargas de trabajo computacionales interactivas.
* Colecciones "Big Data", como matrices paralelas, marcos de datos y listas que extienden interfaces comunes como iteradores NumPy, Pandas o Python a entornos más grandes que la memoria o distribuidos. Estas colecciones paralelas se ejecutan en la parte superior de los programadores de tareas dinámicas.

<img src="{{ "/img/dask.png" | prepend: site.baseurl | replace: '//', '/' }}" alt="Aplicación">

Dask destaca las siguientes virtudes:

* Familiar: Proporciona objetos de Matriz NumPy y Pandas DataFrame paralelizados
* Flexible: Proporciona una interfaz de programación de tareas para más cargas de trabajo personalizadas e integración con otros proyectos.
* Nativo: permite la computación distribuida en Pure Python con acceso a la pila PyData.
* Rápido: Funciona con baja sobrecarga, baja latencia y serialización mínima necesaria para algoritmos numéricos rápidos
* Scales up: Se ejecuta resilientemente en clusters con 1000s de núcleos
* Scales down: Trivial para configurar y ejecutar en un ordenador portátil en un solo proceso
* Responsive: Diseñado pensando en la informática interactiva, proporciona retroalimentación y diagnósticos rápidos para ayudar a los seres humanos

[back to the homepage]({{ site.baseurl }}).
