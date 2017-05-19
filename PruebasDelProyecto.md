---
layout: page
title: Pruebas
---

Para nuestras pruebas usamos dos archivos csv cada uno de 1.8 GB aproximadamente el cual consiste en datos de lo staxis de New York City del año 2015 en los meses de Enero y Febrero.

<img src="{{ "/img/taxi.png" | prepend: site.baseurl | replace: '//', '/' }}" alt="Pruebas">

Esos archivos fueron puestos en HDFS local en una laptop dando como resultado lo siguiente :

<img src="{{ "/img/hdfs1.png" | prepend: site.baseurl | replace: '//', '/' }}" alt="Pruebas2">

## Codigo

Iniciamos el Executor que nos permitira trabajar con ask posteriormente.

Vamos a leer los archivos que guardamos en el HDFS (yellow_tripdata_2015-01.csv y yellow_tripdata_2015-02.csv)


```python
from distributed import Client, LocalCluster ,Executor, progress
c = LocalCluster()
e = Executor('127.0.0.1:8786')
e
from hdfs3 import HDFileSystem
import pandas

hdfs = HDFileSystem(host='localhost', port=9000)
print(hdfs.ls('/user/data/tripData/'))
with hdfs.open('/user/data/tripData/yellow_tripdata_2015-01.csv') as f:
    nyc20151 = pandas.read_csv(f)

with hdfs.open('/user/data/tripData/yellow_tripdata_2015-02.csv') as f:
    nyc20152 = pandas.read_csv(f)


```

    [{'owner': 'geckolml', 'last_mod': 1494590508, 'size': 1985964692, 'group': 'supergroup', 'block_size': 134217728, 'replication': 1, 'last_access': 1494590415, 'name': '/user/data/tripData/yellow_tripdata_2015-01.csv', 'permissions': 420, 'kind': 'file'}, {'owner': 'geckolml', 'last_mod': 1494590599, 'size': 1945357622, 'group': 'supergroup', 'block_size': 134217728, 'replication': 1, 'last_access': 1494590519, 'name': '/user/data/tripData/yellow_tripdata_2015-02.csv', 'permissions': 420, 'kind': 'file'}]


Podemos observar las cinco primeras filas de los csv almacenados en el HDFS. Cada tupla se refiere a un viaje en un taxi el cual posee los siguientes atributos :

* VendorID : Quien provee el grabado : 1-Creative Mobile Technologies, 2-VeriFone Inc.
* tpep_pickup_datetime : La fecha y hora cuando el medidor se activó.
* tpep_dropoff_datetime :  La fecha y hora cuando el medidor se desactivó.
* passenger_count: Numero de pasajeros en el vehiculo .
* trip_distance : La distancia en millas que reportó el taximetro.
* pickup_longitude: Longitud donde el medidor fue activado.
* pickup_latitude : Latitud donde el medidor fue activado.
* RateCodeID :
* store_and_fw_flag
* dropoff_longitude :  Longitud donde el medidor fue desactivado.
* dropoff_latitude : Latitud donde el medidor fue activado.
* payment_type : 1=Tarjeta de Credito, 2=Cash, 3=sin cargo, 4=Disputa, 5=Desconocido, 6=Viaje Anulado
* fare_amount
* extra : Cargo extra si es de noche(0.5\$) o en hora punta(1\$)
* mta_tax: Impuesto de MTA de \$ 0.50 que se activa automáticamente basado en la Tarifa en uso.
* improvement_surcharge: \$ 0.3 de recargo de mejora de los viajes evaluados. El recargo de la mejor comenzo a ser gravado en 2015.
* tip_amount : Este campo se rellena automaticamente para tarjets de credito. No incluye propinas en efectivo.
* tolls_amount:  Cantidad total de todos los peajes pagados en viaje

* total_amount: La cantidad total cobrado a los pasajeros. No incluye consejos de efectivo.



```python
nyc20151.head()
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>VendorID</th>
      <th>tpep_pickup_datetime</th>
      <th>tpep_dropoff_datetime</th>
      <th>passenger_count</th>
      <th>trip_distance</th>
      <th>pickup_longitude</th>
      <th>pickup_latitude</th>
      <th>RateCodeID</th>
      <th>store_and_fwd_flag</th>
      <th>dropoff_longitude</th>
      <th>dropoff_latitude</th>
      <th>payment_type</th>
      <th>fare_amount</th>
      <th>extra</th>
      <th>mta_tax</th>
      <th>tip_amount</th>
      <th>tolls_amount</th>
      <th>improvement_surcharge</th>
      <th>total_amount</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2</td>
      <td>2015-01-15 19:05:39</td>
      <td>2015-01-15 19:23:42</td>
      <td>1</td>
      <td>1.59</td>
      <td>-73.993896</td>
      <td>40.750111</td>
      <td>1</td>
      <td>N</td>
      <td>-73.974785</td>
      <td>40.750618</td>
      <td>1</td>
      <td>12.0</td>
      <td>1.0</td>
      <td>0.5</td>
      <td>3.25</td>
      <td>0.0</td>
      <td>0.3</td>
      <td>17.05</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>2015-01-10 20:33:38</td>
      <td>2015-01-10 20:53:28</td>
      <td>1</td>
      <td>3.30</td>
      <td>-74.001648</td>
      <td>40.724243</td>
      <td>1</td>
      <td>N</td>
      <td>-73.994415</td>
      <td>40.759109</td>
      <td>1</td>
      <td>14.5</td>
      <td>0.5</td>
      <td>0.5</td>
      <td>2.00</td>
      <td>0.0</td>
      <td>0.3</td>
      <td>17.80</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1</td>
      <td>2015-01-10 20:33:38</td>
      <td>2015-01-10 20:43:41</td>
      <td>1</td>
      <td>1.80</td>
      <td>-73.963341</td>
      <td>40.802788</td>
      <td>1</td>
      <td>N</td>
      <td>-73.951820</td>
      <td>40.824413</td>
      <td>2</td>
      <td>9.5</td>
      <td>0.5</td>
      <td>0.5</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.3</td>
      <td>10.80</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1</td>
      <td>2015-01-10 20:33:39</td>
      <td>2015-01-10 20:35:31</td>
      <td>1</td>
      <td>0.50</td>
      <td>-74.009087</td>
      <td>40.713818</td>
      <td>1</td>
      <td>N</td>
      <td>-74.004326</td>
      <td>40.719986</td>
      <td>2</td>
      <td>3.5</td>
      <td>0.5</td>
      <td>0.5</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.3</td>
      <td>4.80</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1</td>
      <td>2015-01-10 20:33:39</td>
      <td>2015-01-10 20:52:58</td>
      <td>1</td>
      <td>3.00</td>
      <td>-73.971176</td>
      <td>40.762428</td>
      <td>1</td>
      <td>N</td>
      <td>-74.004181</td>
      <td>40.742653</td>
      <td>2</td>
      <td>15.0</td>
      <td>0.5</td>
      <td>0.5</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.3</td>
      <td>16.30</td>
    </tr>
  </tbody>
</table>
</div>




```python
nyc20151.passenger_count.sum()
```




    21437303




```python
print(e)
```

    <Client: scheduler="127.0.0.1:8786" processes=8 cores=8>



```python
nyc20151.dtypes
```




    VendorID                   int64
    tpep_pickup_datetime      object
    tpep_dropoff_datetime     object
    passenger_count            int64
    trip_distance            float64
    pickup_longitude         float64
    pickup_latitude          float64
    RateCodeID                 int64
    store_and_fwd_flag        object
    dropoff_longitude        float64
    dropoff_latitude         float64
    payment_type               int64
    fare_amount              float64
    extra                    float64
    mta_tax                  float64
    tip_amount               float64
    tolls_amount             float64
    improvement_surcharge    float64
    total_amount             float64
    dtype: object




```python
 nyc20151[['fare_amount', 'tip_amount', 'payment_type']].head()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>fare_amount</th>
      <th>tip_amount</th>
      <th>payment_type</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>12.0</td>
      <td>3.25</td>
      <td>1</td>
    </tr>
    <tr>
      <th>1</th>
      <td>14.5</td>
      <td>2.00</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>9.5</td>
      <td>0.00</td>
      <td>2</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3.5</td>
      <td>0.00</td>
      <td>2</td>
    </tr>
    <tr>
      <th>4</th>
      <td>15.0</td>
      <td>0.00</td>
      <td>2</td>
    </tr>
  </tbody>
</table>
</div>




```python
%time nyc20151.payment_type.value_counts()
```

    CPU times: user 88 ms, sys: 8 ms, total: 96 ms
    Wall time: 469 ms





    1    7881388
    2    4816992
    3      38632
    4      11972
    5          2
    Name: payment_type, dtype: int64




```python
%time nyc20151[nyc20151.tip_amount == 0].payment_type.value_counts()
```

    CPU times: user 808 ms, sys: 124 ms, total: 932 ms
    Wall time: 1.1 s





    2    4816808
    1     270456
    3      38567
    4      11934
    5          2
    Name: payment_type, dtype: int64




```python

```


```python
import seaborn as sns
import matplotlib.pyplot as plt


g = sns.factorplot(x="trip_distance",y="total_amount",data=nyc20151[(nyc20151.payment_type == 2) & (nyc20151.passenger_count == 1)].head(20), size=20, kind="bar", palette="muted")

g.set_titles("Grafica")
g.despine(left=False, right=False, top=False, bottom= False)
g.set_xlabels("Distancia de Viaje")
g.set_ylabels("Precio Total")
plt.subplots_adjust(top=0.9)
g.fig.suptitle('Distancia de Viaje vs Precio Total ( Transporte en taxi NYC)')
plt.show()


```


<img src="{{ "/img/output_12_0.png" | prepend: site.baseurl | replace: '//', '/' }}" alt="Grafica">



```python
nyc20151[(nyc20151.payment_type == 2) & (nyc20151.passenger_count == 1)].head(20)
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>VendorID</th>
      <th>tpep_pickup_datetime</th>
      <th>tpep_dropoff_datetime</th>
      <th>passenger_count</th>
      <th>trip_distance</th>
      <th>pickup_longitude</th>
      <th>pickup_latitude</th>
      <th>RateCodeID</th>
      <th>store_and_fwd_flag</th>
      <th>dropoff_longitude</th>
      <th>dropoff_latitude</th>
      <th>payment_type</th>
      <th>fare_amount</th>
      <th>extra</th>
      <th>mta_tax</th>
      <th>tip_amount</th>
      <th>tolls_amount</th>
      <th>improvement_surcharge</th>
      <th>total_amount</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2</th>
      <td>1</td>
      <td>2015-01-10 20:33:38</td>
      <td>2015-01-10 20:43:41</td>
      <td>1</td>
      <td>1.80</td>
      <td>-73.963341</td>
      <td>40.802788</td>
      <td>1</td>
      <td>N</td>
      <td>-73.951820</td>
      <td>40.824413</td>
      <td>2</td>
      <td>9.5</td>
      <td>0.5</td>
      <td>0.5</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.3</td>
      <td>10.8</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1</td>
      <td>2015-01-10 20:33:39</td>
      <td>2015-01-10 20:35:31</td>
      <td>1</td>
      <td>0.50</td>
      <td>-74.009087</td>
      <td>40.713818</td>
      <td>1</td>
      <td>N</td>
      <td>-74.004326</td>
      <td>40.719986</td>
      <td>2</td>
      <td>3.5</td>
      <td>0.5</td>
      <td>0.5</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.3</td>
      <td>4.8</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1</td>
      <td>2015-01-10 20:33:39</td>
      <td>2015-01-10 20:52:58</td>
      <td>1</td>
      <td>3.00</td>
      <td>-73.971176</td>
      <td>40.762428</td>
      <td>1</td>
      <td>N</td>
      <td>-74.004181</td>
      <td>40.742653</td>
      <td>2</td>
      <td>15.0</td>
      <td>0.5</td>
      <td>0.5</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.3</td>
      <td>16.3</td>
    </tr>
    <tr>
      <th>6</th>
      <td>1</td>
      <td>2015-01-10 20:33:39</td>
      <td>2015-01-10 20:58:31</td>
      <td>1</td>
      <td>2.20</td>
      <td>-73.983276</td>
      <td>40.726009</td>
      <td>1</td>
      <td>N</td>
      <td>-73.992470</td>
      <td>40.749634</td>
      <td>2</td>
      <td>14.0</td>
      <td>0.5</td>
      <td>0.5</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.3</td>
      <td>15.3</td>
    </tr>
    <tr>
      <th>12</th>
      <td>1</td>
      <td>2015-01-10 20:33:41</td>
      <td>2015-01-10 20:35:23</td>
      <td>1</td>
      <td>0.30</td>
      <td>-74.008362</td>
      <td>40.704376</td>
      <td>1</td>
      <td>N</td>
      <td>-74.009773</td>
      <td>40.707726</td>
      <td>2</td>
      <td>3.0</td>
      <td>0.5</td>
      <td>0.5</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.3</td>
      <td>4.3</td>
    </tr>
    <tr>
      <th>14</th>
      <td>1</td>
      <td>2015-01-10 20:33:41</td>
      <td>2015-01-10 20:39:23</td>
      <td>1</td>
      <td>1.10</td>
      <td>-74.006721</td>
      <td>40.731777</td>
      <td>1</td>
      <td>N</td>
      <td>-73.995216</td>
      <td>40.739895</td>
      <td>2</td>
      <td>6.0</td>
      <td>0.5</td>
      <td>0.5</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.3</td>
      <td>7.3</td>
    </tr>
    <tr>
      <th>20</th>
      <td>2</td>
      <td>2015-01-15 19:05:41</td>
      <td>2015-01-15 19:31:00</td>
      <td>1</td>
      <td>3.60</td>
      <td>-73.976601</td>
      <td>40.751896</td>
      <td>1</td>
      <td>N</td>
      <td>-73.998924</td>
      <td>40.714596</td>
      <td>2</td>
      <td>17.5</td>
      <td>1.0</td>
      <td>0.5</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.3</td>
      <td>19.3</td>
    </tr>
    <tr>
      <th>26</th>
      <td>2</td>
      <td>2015-01-15 19:05:42</td>
      <td>2015-01-15 19:16:18</td>
      <td>1</td>
      <td>1.53</td>
      <td>-73.991127</td>
      <td>40.750080</td>
      <td>1</td>
      <td>N</td>
      <td>-73.988609</td>
      <td>40.734890</td>
      <td>2</td>
      <td>9.0</td>
      <td>1.0</td>
      <td>0.5</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.3</td>
      <td>10.8</td>
    </tr>
    <tr>
      <th>36</th>
      <td>1</td>
      <td>2015-01-10 19:12:21</td>
      <td>2015-01-10 19:31:33</td>
      <td>1</td>
      <td>2.90</td>
      <td>-74.003052</td>
      <td>40.727718</td>
      <td>1</td>
      <td>N</td>
      <td>-73.976036</td>
      <td>40.763969</td>
      <td>2</td>
      <td>13.5</td>
      <td>0.0</td>
      <td>0.5</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.3</td>
      <td>14.3</td>
    </tr>
    <tr>
      <th>38</th>
      <td>2</td>
      <td>2015-01-25 00:13:04</td>
      <td>2015-01-25 00:13:18</td>
      <td>1</td>
      <td>0.02</td>
      <td>-73.994812</td>
      <td>40.727741</td>
      <td>1</td>
      <td>N</td>
      <td>-73.996941</td>
      <td>40.725559</td>
      <td>2</td>
      <td>2.5</td>
      <td>0.5</td>
      <td>0.5</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.3</td>
      <td>3.8</td>
    </tr>
    <tr>
      <th>39</th>
      <td>2</td>
      <td>2015-01-25 00:13:05</td>
      <td>2015-01-25 00:29:11</td>
      <td>1</td>
      <td>1.73</td>
      <td>-73.985939</td>
      <td>40.726765</td>
      <td>1</td>
      <td>N</td>
      <td>-74.006546</td>
      <td>40.737865</td>
      <td>2</td>
      <td>11.0</td>
      <td>0.5</td>
      <td>0.5</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.3</td>
      <td>12.3</td>
    </tr>
    <tr>
      <th>41</th>
      <td>2</td>
      <td>2015-01-25 00:13:05</td>
      <td>2015-01-25 00:25:50</td>
      <td>1</td>
      <td>3.53</td>
      <td>-73.988968</td>
      <td>40.721680</td>
      <td>1</td>
      <td>N</td>
      <td>-73.978729</td>
      <td>40.762470</td>
      <td>2</td>
      <td>12.5</td>
      <td>0.5</td>
      <td>0.5</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.3</td>
      <td>13.8</td>
    </tr>
    <tr>
      <th>49</th>
      <td>2</td>
      <td>2015-01-25 00:13:07</td>
      <td>2015-01-25 00:15:39</td>
      <td>1</td>
      <td>0.49</td>
      <td>-73.986160</td>
      <td>40.759949</td>
      <td>1</td>
      <td>N</td>
      <td>-73.983604</td>
      <td>40.763939</td>
      <td>2</td>
      <td>4.0</td>
      <td>0.5</td>
      <td>0.5</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.3</td>
      <td>5.3</td>
    </tr>
    <tr>
      <th>53</th>
      <td>2</td>
      <td>2015-01-25 00:13:08</td>
      <td>2015-01-25 00:18:57</td>
      <td>1</td>
      <td>1.10</td>
      <td>-73.979881</td>
      <td>40.746246</td>
      <td>1</td>
      <td>N</td>
      <td>-73.987663</td>
      <td>40.732674</td>
      <td>2</td>
      <td>6.0</td>
      <td>0.5</td>
      <td>0.5</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.3</td>
      <td>7.3</td>
    </tr>
    <tr>
      <th>58</th>
      <td>2</td>
      <td>2015-01-25 00:13:09</td>
      <td>2015-01-25 01:02:40</td>
      <td>1</td>
      <td>10.20</td>
      <td>-73.997612</td>
      <td>40.762241</td>
      <td>1</td>
      <td>N</td>
      <td>-73.908577</td>
      <td>40.690048</td>
      <td>2</td>
      <td>39.0</td>
      <td>0.5</td>
      <td>0.5</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.3</td>
      <td>40.3</td>
    </tr>
    <tr>
      <th>60</th>
      <td>2</td>
      <td>2015-01-04 13:44:51</td>
      <td>2015-01-04 13:45:03</td>
      <td>1</td>
      <td>0.03</td>
      <td>-73.944511</td>
      <td>40.794567</td>
      <td>1</td>
      <td>N</td>
      <td>-73.944702</td>
      <td>40.794334</td>
      <td>2</td>
      <td>2.5</td>
      <td>0.0</td>
      <td>0.5</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.3</td>
      <td>3.3</td>
    </tr>
    <tr>
      <th>62</th>
      <td>2</td>
      <td>2015-01-04 13:44:52</td>
      <td>2015-01-04 13:46:38</td>
      <td>1</td>
      <td>0.36</td>
      <td>-73.991249</td>
      <td>40.765461</td>
      <td>1</td>
      <td>N</td>
      <td>-73.987534</td>
      <td>40.768410</td>
      <td>2</td>
      <td>3.5</td>
      <td>0.0</td>
      <td>0.5</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.3</td>
      <td>4.3</td>
    </tr>
    <tr>
      <th>66</th>
      <td>2</td>
      <td>2015-01-04 13:44:52</td>
      <td>2015-01-04 13:49:03</td>
      <td>1</td>
      <td>0.85</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>1</td>
      <td>N</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>2</td>
      <td>5.5</td>
      <td>0.0</td>
      <td>0.5</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.3</td>
      <td>6.3</td>
    </tr>
    <tr>
      <th>75</th>
      <td>2</td>
      <td>2015-01-04 13:44:54</td>
      <td>2015-01-04 13:53:15</td>
      <td>1</td>
      <td>1.39</td>
      <td>-73.986252</td>
      <td>40.730438</td>
      <td>1</td>
      <td>N</td>
      <td>-73.991219</td>
      <td>40.742168</td>
      <td>2</td>
      <td>7.5</td>
      <td>0.0</td>
      <td>0.5</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.3</td>
      <td>8.3</td>
    </tr>
    <tr>
      <th>76</th>
      <td>2</td>
      <td>2015-01-15 14:00:41</td>
      <td>2015-01-15 14:11:18</td>
      <td>1</td>
      <td>1.64</td>
      <td>-73.983727</td>
      <td>40.746342</td>
      <td>1</td>
      <td>N</td>
      <td>-73.966797</td>
      <td>40.761406</td>
      <td>2</td>
      <td>8.5</td>
      <td>0.0</td>
      <td>0.5</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.3</td>
      <td>9.3</td>
    </tr>
  </tbody>
</table>
</div>




```python
nyc20152[nyc20152.payment_type == 3].head()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>VendorID</th>
      <th>tpep_pickup_datetime</th>
      <th>tpep_dropoff_datetime</th>
      <th>passenger_count</th>
      <th>trip_distance</th>
      <th>pickup_longitude</th>
      <th>pickup_latitude</th>
      <th>RateCodeID</th>
      <th>store_and_fwd_flag</th>
      <th>dropoff_longitude</th>
      <th>dropoff_latitude</th>
      <th>payment_type</th>
      <th>fare_amount</th>
      <th>extra</th>
      <th>mta_tax</th>
      <th>tip_amount</th>
      <th>tolls_amount</th>
      <th>improvement_surcharge</th>
      <th>total_amount</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>168</th>
      <td>1</td>
      <td>2015-02-27 20:18:38</td>
      <td>2015-02-27 20:40:55</td>
      <td>1</td>
      <td>8.10</td>
      <td>-73.990425</td>
      <td>40.751686</td>
      <td>1</td>
      <td>N</td>
      <td>-73.939827</td>
      <td>40.847099</td>
      <td>3</td>
      <td>25.5</td>
      <td>0.5</td>
      <td>0.5</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.3</td>
      <td>26.8</td>
    </tr>
    <tr>
      <th>327</th>
      <td>1</td>
      <td>2015-02-27 20:37:34</td>
      <td>2015-02-27 20:39:53</td>
      <td>1</td>
      <td>0.40</td>
      <td>-73.945305</td>
      <td>40.807678</td>
      <td>1</td>
      <td>N</td>
      <td>-73.941887</td>
      <td>40.812561</td>
      <td>3</td>
      <td>3.5</td>
      <td>0.5</td>
      <td>0.5</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.3</td>
      <td>4.8</td>
    </tr>
    <tr>
      <th>747</th>
      <td>2</td>
      <td>2015-02-03 13:03:23</td>
      <td>2015-02-03 13:05:59</td>
      <td>1</td>
      <td>0.19</td>
      <td>-73.995377</td>
      <td>40.717339</td>
      <td>1</td>
      <td>N</td>
      <td>-73.994194</td>
      <td>40.719791</td>
      <td>3</td>
      <td>-3.5</td>
      <td>0.0</td>
      <td>-0.5</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.3</td>
      <td>-4.3</td>
    </tr>
    <tr>
      <th>1018</th>
      <td>1</td>
      <td>2015-02-27 22:47:17</td>
      <td>2015-02-27 22:49:09</td>
      <td>1</td>
      <td>0.10</td>
      <td>-73.962013</td>
      <td>40.760551</td>
      <td>1</td>
      <td>N</td>
      <td>-73.962173</td>
      <td>40.761364</td>
      <td>3</td>
      <td>3.0</td>
      <td>0.5</td>
      <td>0.5</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.3</td>
      <td>4.3</td>
    </tr>
    <tr>
      <th>1719</th>
      <td>1</td>
      <td>2015-02-08 15:55:51</td>
      <td>2015-02-08 16:08:28</td>
      <td>1</td>
      <td>2.20</td>
      <td>-73.979683</td>
      <td>40.784115</td>
      <td>1</td>
      <td>N</td>
      <td>-73.954857</td>
      <td>40.805138</td>
      <td>3</td>
      <td>10.5</td>
      <td>0.0</td>
      <td>0.5</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.3</td>
      <td>11.3</td>
    </tr>
  </tbody>
</table>
</div>




```python

```

[back to the homepage]({{ site.baseurl }}).
