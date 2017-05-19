---
layout: page
title: HDFS
---

HDFS es el acrónimo para Hadoop Distributed File System, un sistema de archivos distribuidos(1)  que permite optimizar el flujo de datos mediante su arquitectura de master-slave haciendo uso de sus NodeName y DataNode.

<img src="{{ "/img/hdfs.png" | prepend: site.baseurl | replace: '//', '/' }}" alt="Aplicación">

> “it is often better to migrate the computation closer to where the data is located rather than moving the data to where the application is running..”

Características:
* High fault tolerance.
* Designed to be deployed in low-cost hardware.
* Suitable for applications with large data sets(Gb or Tb).
* Su modelo(más abajo) permite aplicaciones de MapReduce por ejemplo.

Objetivos y necesidades
* Las aplicaciones bajo HDFS deben acceder a su set de datos a manera de streaming.
Modelo:
* write-once-read-many access model to files. “ A file once created, written, and closed need not be changed”

### How does HDFS work?

Cada archivo es dividido en bloques y cada bloque es almacenado en los DataNode. El NameNode se encarga de operaciones como operar, cerrar o renombrar ficheros y directorios además de  determinar el mapping de bloque en los DataNodes. Los DataNodes son los responsables de realizar operaciones de creacion, eliminacion y replicacion siempre y cuando el NodeName se lo haya ordenado.
No se especifica si un DataNode debe ser instalado únicamente en un equipo, sin embargo en proyectos “reales” esto pasa muy raramente pues la unicidad de un DataNode en una máquina simplifica la arquitectura del sistema.

La arquitectura está diseñada para que los datos nunca sean dirigidos o manejados por el NodeName.

### Where HDFS can be deployed?
HDFS está escrito en Java, así que cualquier equipo que soporte Java podrá correr HDFS.

[back to the homepage]({{ site.baseurl }}).
