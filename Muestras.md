---
layout: page
title: Muestras
---

La aplicación que deseamos desarrollar trata de lo siguiente:

Se pleteara una colección de puntos con la ayuda de la api de google maps, en la cual se representará como 2 tipos de puntos:

* Pickup:  Puntos en los cuales los usuarios partieron.
* DropOff: Puntos en los cuales los usuarios llegarón.

Para ello se presentará una colección de puntos de partida y llegada de una base de datos de taxis de New York. Al hacer click observaremos ambas colecciones de puntos. Viendo los denso que son en ciertas áreas, mostrándonos que estás areas ha sido muy frecuentada por los usuarios ya sea de partida o llegada.

Ahora bien, estamos proponiendo realizar un **colectivo de autos**, teniendo en cuenta solo a los usuarios que parten de un área parecida con un destino de área parecida, bajo el estudio de la base de datos de 2 años podemos realizar estadísticas que nos ayuden a poder realizar este estudio y ver que la propuesta puede ayudar a que el costo de viaje disminuya y el tiempo de viaje de un puntos a otro sea rápido, para realizar ello se tendrá que evaluar de cuantos metros cuadrados serán estos clusteres donde se recogerán a las persona y de igual forma su partida, agregando además el tiempo desde el que se comienza a forma estos pequeños clusteres de personas que solicitan el servicio que en un promedio se ha colocado que sea de 10 personas.

<div id="floating-panel">
  <button onclick="changeData('Pickup')">Pickup</button>
  <button onclick="changeData('DropOff')">DropOff</button>
  <button onclick="changeData()">Change data</button>
</div>

<div id="map"></div>

[back to the homepage]({{ site.baseurl }}).
