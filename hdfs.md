---
layout: page
title: HDFS
---

[HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html) es el acrónimo para Hadoop Distributed File System, un sistema de archivos distribuidos(1)  que permite optimizar el flujo de datos mediante su arquitectura de master-slave haciendo uso de sus NodeName y DataNode.

<img src="{{ "/img/hdfs.png" | prepend: site.baseurl | replace: '//', '/' }}" alt="Aplicación">

> “A menudo es mejor migrar el cálculo más cerca de donde se encuentran los datos en lugar de mover los datos a donde se está ejecutando la aplicación.”

Características:
* Gran tolerancia a fallos.
* Diseñado para ser desplegado en hardware de bajo costo.
* Adecuado para aplicaciones con grandes conjuntos de datos (Gb o Tb).
* Su modelo(más abajo) permite aplicaciones de MapReduce por ejemplo.

Objetivos y necesidades
* Las aplicaciones bajo HDFS deben acceder a su set de datos a manera de streaming.
Modelo:
* Write-once-read-many: modelo de acceso a los archivos. "Un archivo una vez creado, escrito y cerrado no necesita ser cambiado”

El NameNode y el DataNode son piezas de software diseñadas para funcionar en máquinas de productos básicos. Estas máquinas suelen ejecutar un sistema operativo (SO) GNU / Linux. HDFS se construye utilizando el lenguaje Java; Cualquier máquina que admita Java puede ejecutar el NameNode o el software DataNode. El uso del lenguaje Java altamente portátil significa que HDFS se puede desplegar en una amplia gama de máquinas. Un despliegue típico tiene una máquina dedicada que ejecuta sólo el software NameNode. Cada una de las otras máquinas del clúster ejecuta una instancia del software DataNode. La arquitectura no excluye la ejecución de múltiples DataNodes en la misma máquina, pero en un despliegue real que rara vez es el caso.

La existencia de un único NameNode en un clúster simplifica enormemente la arquitectura del sistema. NameNode es el árbitro y el repositorio para todos los metadatos HDFS. El sistema está diseñado de tal manera que los datos de usuario nunca fluyen a través del NameNode.

### Como funciona HDFS?

Cada archivo es dividido en bloques y cada bloque es almacenado en los DataNode. El NameNode se encarga de operaciones como operar, cerrar o renombrar ficheros y directorios además de  determinar el mapping de bloque en los DataNodes. Los DataNodes son los responsables de realizar operaciones de creacion, eliminacion y replicacion siempre y cuando el NodeName se lo haya ordenado.
No se especifica si un DataNode debe ser instalado únicamente en un equipo, sin embargo en proyectos “reales” esto pasa muy raramente pues la unicidad de un DataNode en una máquina simplifica la arquitectura del sistema.

La arquitectura está diseñada para que los datos nunca sean dirigidos o manejados por el NodeName.

[back to the homepage]({{ site.baseurl }}).
