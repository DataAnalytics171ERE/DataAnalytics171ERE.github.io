---
layout: page
title: Pruebas Notebook
---

Para nuestras pruebas usamos dos archivos csv cada uno de 1.8 GB aproximadamente el cual consiste en datos de lo staxis de New York City del año 2015 en los meses de Enero y Febrero.


### <span style="color: #2C3F28; font-family: Babas; font-size: 1.5em;">Taxis amarillos NYC</span>

<img src="{{ "/img/taxi.png" | prepend: site.baseurl | replace: '//', '/' }}" alt="Pruebas">


### <span style="color: #2C3F28; font-family: Babas; font-size: 1.5em;">HDFS</span>
Esos archivos fueron puestos en HDFS local en una laptop dando como resultado lo siguiente :

<img src="{{ "/img/hdfs1.png" | prepend: site.baseurl | replace: '//', '/' }}" alt="HDFS">


 ### <span style="color: #970B0B; font-family: Babas; font-size: 1.5em;">Código</span>


Vamos a leer uno de los archivos que guardamos en el HDFS (yellow_tripdata_2015-01.csv y yellow_tripdata_2015-02.csv) y los almacenamos con Dataframe Pandas y Dataframe Dask.


```python
from hdfs3 import HDFileSystem
import dask.dataframe as dd ## Dask Dataframe
import pandas
hdfs = HDFileSystem(host='localhost', port=9000)
print(hdfs.ls('/user/data/tripData/'))
with hdfs.open('/user/data/tripData/yellow_tripdata_2015-01.csv') as f:
    nyc20151pd = pandas.read_csv(f)
nyc20151 = dd.read_csv("hdfs:///user/data/tripData/yellow_tripdata_2015-01.csv", storage_options={ 'port': 9000})
#nyc20151.head()
```

    [{'last_mod': 1494590508, 'size': 1985964692, 'kind': 'file', 'group': 'supergroup', 'last_access': 1495343517, 'block_size': 134217728, 'owner': 'geckolml', 'name': '/user/data/tripData/yellow_tripdata_2015-01.csv', 'permissions': 420, 'replication': 1}, {'last_mod': 1494590599, 'size': 1945357622, 'kind': 'file', 'group': 'supergroup', 'last_access': 1494766894, 'block_size': 134217728, 'owner': 'geckolml', 'name': '/user/data/tripData/yellow_tripdata_2015-02.csv', 'permissions': 420, 'replication': 1}]



* `nyc20151pd` es Dataframe de Pandas que almacena el .csv guardado en hdfs.
* `nyc20151`   es Dataframe de Dask que almacena el .csv guardado en hdfs.


```python
nyc20151.columns
```




    Index(['VendorID', 'tpep_pickup_datetime', 'tpep_dropoff_datetime',
           'passenger_count', 'trip_distance', 'pickup_longitude',
           'pickup_latitude', 'RateCodeID', 'store_and_fwd_flag',
           'dropoff_longitude', 'dropoff_latitude', 'payment_type', 'fare_amount',
           'extra', 'mta_tax', 'tip_amount', 'tolls_amount',
           'improvement_surcharge', 'total_amount'],
          dtype='object')



La cantidad de filas en nuestros Dataframe Pandas y Dask en el archivo `yellow_tripdata_2015-01.csv` es 12748986 como se muestra en la columna de abajo


```python
print(len(nyc20151.index))
print(len(nyc20151pd.index))
```

    12748986
    12748986


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



####  <span style="color: #970B0B; font-family: Babas; font-size: 2em;"> Evaluamos los tiempos de ejecución entre Dataframe de pandas y Dask Dataframe</span>

####  `value_counts()`


```python
timeit nyc20151[nyc20151.tip_amount == 0].payment_type.value_counts()
```

    The slowest run took 4.18 times longer than the fastest. This could mean that an intermediate result is being cached.
    100 loops, best of 3: 1.95 ms per loop



```python
timeit nyc20151pd[nyc20151pd.tip_amount == 0].payment_type.value_counts()
```

    1 loop, best of 3: 987 ms per loop


#### `sum()`


```python
timeit nyc20151.passenger_count.sum()
```

    1000 loops, best of 3: 613 µs per loop




```python
timeit nyc20151pd.passenger_count.sum()
```

    100 loops, best of 3: 11.7 ms per loop


#### `Numero de filas`


```python
timeit len(nyc20151.index)
```

    1 loop, best of 3: 26.9 s per loop



```python
timeit len(nyc20151pd.index)
```

    The slowest run took 54660.36 times longer than the fastest. This could mean that an intermediate result is being cached.
    1000000 loops, best of 3: 969 ns per loop


#### Convirtiendo el tipo de datos Series a datatime en las columnas tpep_pickup_datetime y tpep_dropoff_datetime

#### Dataframe Pandas


```python
nyc20151pd[['tpep_pickup_datetime','tpep_dropoff_datetime']]=nyc20151pd[['tpep_pickup_datetime','tpep_dropoff_datetime']].apply(pandas.to_datetime)
```


```python
nyc20151pd.dtypes
```




    VendorID                          int64
    tpep_pickup_datetime     datetime64[ns]
    tpep_dropoff_datetime    datetime64[ns]
    passenger_count                   int64
    trip_distance                   float64
    pickup_longitude                float64
    pickup_latitude                 float64
    RateCodeID                        int64
    store_and_fwd_flag               object
    dropoff_longitude               float64
    dropoff_latitude                float64
    payment_type                      int64
    fare_amount                     float64
    extra                           float64
    mta_tax                         float64
    tip_amount                      float64
    tolls_amount                    float64
    improvement_surcharge           float64
    total_amount                    float64
    dtype: object



#### Dataframe Dask


```python
nyc20151.tpep_pickup_datetime=nyc20151.tpep_pickup_datetime.astype('datetime64[ns]')
nyc20151.tpep_dropoff_datetime=nyc20151.tpep_dropoff_datetime.astype('datetime64[ns]')
```


```python
nyc20151.dtypes
```




    VendorID                          int64
    tpep_pickup_datetime     datetime64[ns]
    tpep_dropoff_datetime    datetime64[ns]
    passenger_count                   int64
    trip_distance                   float64
    pickup_longitude                float64
    pickup_latitude                 float64
    RateCodeID                        int64
    store_and_fwd_flag               object
    dropoff_longitude               float64
    dropoff_latitude                float64
    payment_type                      int64
    fare_amount                     float64
    extra                           float64
    mta_tax                         float64
    tip_amount                      float64
    tolls_amount                    float64
    improvement_surcharge           float64
    total_amount                    float64
    dtype: object



#### <span style="color: #970B0B; font-family: Babas; font-size: 1.8em;">Preguntas, respuestas y comparaciones</span>
---

#### ¿Qué días de la semana tienen mayor numero de pasajeros los taxis de NYC?


```python
nyc20151pd.groupby(nyc20151pd.tpep_pickup_datetime.dt.dayofweek).passenger_count.sum()
```




    tpep_pickup_datetime
    0    2200087
    1    2260433
    2    2758308
    3    3621723
    4    3732988
    5    4141314
    6    2722450
    Name: passenger_count, dtype: int64



>  Repuesta : Los sábados son los dias en que hay mayor flujo de pasajeros en los taxis de NYC.

#### <span style="color: #006633; font-family: Babas; font-size: 1.15em;">Comparación de tiempo:</span>


```python
timeit nyc20151.groupby(nyc20151.tpep_pickup_datetime.dt.dayofweek).passenger_count.sum()
```

    The slowest run took 158.63 times longer than the fastest. This could mean that an intermediate result is being cached.
    1 loop, best of 3: 3.07 ms per loop



```python
timeit nyc20151pd.groupby(nyc20151pd.tpep_pickup_datetime.dt.dayofweek).passenger_count.sum()
```

    1 loop, best of 3: 786 ms per loop


#### ¿Qué días de la semana tienen mayor numero de propinas los taxis de NYC?


```python
nyc20151pd.groupby(nyc20151pd.tpep_pickup_datetime.dt.dayofweek).tip_amount.sum()

```




    tpep_pickup_datetime
    0    2.096523e+06
    1    2.162308e+06
    2    2.740310e+06
    3    3.487074e+06
    4    3.485685e+06
    5    3.322782e+06
    6    6.339563e+06
    Name: tip_amount, dtype: float64



>   Respuesta : Los días Domingos los taxistas de NYC reciben mas propinas.


#### <span style="color: #006633; font-family: Babas; font-size: 1.15em;">Comparación de tiempo : </span>


```python
timeit nyc20151.groupby(nyc20151.tpep_pickup_datetime.dt.dayofweek).tip_amount.sum()
```

    100 loops, best of 3: 3.34 ms per loop



```python
timeit nyc20151pd.groupby(nyc20151pd.tpep_pickup_datetime.dt.dayofweek).tip_amount.sum()
```

    The slowest run took 10.59 times longer than the fastest. This could mean that an intermediate result is being cached.
    1 loop, best of 3: 837 ms per loop


#### Salida de taxis con posicion

#### <span style="color: #006633; font-family: Babas; font-size: 1.15em;">Comparación de tiempo</span>


```python
timeit nyc20151.groupby(['tpep_pickup_datetime','pickup_longitude','pickup_latitude']).passenger_count.sum()
```

    100 loops, best of 3: 2.9 ms per loop



```python
timeit nyc20151pd.groupby(['tpep_pickup_datetime','pickup_longitude','pickup_latitude']).passenger_count.sum()
```

    1 loop, best of 3: 8.58 s per loop


#### <span style="color: #970B0B; font-family: Babas; font-size: 1.5em;">Gráficas Tiempo de Comparación Dask Dataframe y Pandas Dataframe</span>


```python
import pandas
timeNyc20151 = pandas.DataFrame({'total':[1.95,0.613,26.9,3.07,3.34,2.9],
                                 'abbrev':['value_counts','sum()','nrows','pasajerosPorDiaSemana','PropinasPorDiaSemana','salidaTaxisMismaPosicion']})
timeNyc20151pd = pandas.DataFrame({'total':[987,11.7,0.969,786,837,8.58],
                                   'abbrev':['value_counts','sum()','nrows','pasajerosPorDiaSemana','PropinasPorDiaSemana','salidaTaxisMismaPosicion']})
```


```python
import seaborn as sns
import matplotlib.pyplot as plt
sns.set(style="whitegrid")
# Initialize the matplotlib figure
f, ax = plt.subplots(figsize=(10, 5))

# Plot the time Dataframe Pandas
sns.set_color_codes("muted")
sns.barplot(x="total", y="abbrev", data=timeNyc20151pd,
            label="Dataframe Pandas", color="b")

# Plot the time Dataframe Dask
sns.set_color_codes("pastel")
sns.barplot(x="total", y="abbrev", data=timeNyc20151,
            label="Dataframe Dask", color="b") # "x" es valor y "y" es la etiqueta



# Add a legend and informative axis label
ax.legend(ncol=2, loc="lower right", frameon=True)
ax.set(xlim=(0, 24), ylabel="",
       xlabel="Tiempo de ejecucion en ms")
sns.despine(left=True, bottom=True)

plt.show()
```

<img src="{{ "/img/output_45_0.png" | prepend: site.baseurl | replace: '//', '/' }}" alt="Comparacion">

### Grafica Distancia de Viaje vs Precio Total


```python
import seaborn as sns
import matplotlib.pyplot as plt


g = sns.factorplot(x="trip_distance",y="total_amount",data=nyc20151[(nyc20151.payment_type == 2) & (nyc20151.passenger_count == 1)].head(20), size=20, kind="bar", palette="muted")

g.set_titles("Grafica")
g.despine(left=False, right=False, top=False, bottom= False)
g.set_xlabels("Distancia de Viaje(Millas)")
g.set_ylabels("Precio Total($)")
plt.subplots_adjust(top=0.9)
g.fig.suptitle('Distancia de Viaje vs Precio Total ( Transporte en taxi NYC)')
plt.show()


```



<img src="{{ "/img/output_12_0.png" | prepend: site.baseurl | replace: '//', '/' }}" alt="Grafico">


### Datos: 


```python
nyc20151[['VendorID','trip_distance','total_amount']][(nyc20151.payment_type == 2) & (nyc20151.passenger_count == 1)].head(20)
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>VendorID</th>
      <th>trip_distance</th>
      <th>total_amount</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2</th>
      <td>1</td>
      <td>1.80</td>
      <td>10.8</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1</td>
      <td>0.50</td>
      <td>4.8</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1</td>
      <td>3.00</td>
      <td>16.3</td>
    </tr>
    <tr>
      <th>6</th>
      <td>1</td>
      <td>2.20</td>
      <td>15.3</td>
    </tr>
    <tr>
      <th>12</th>
      <td>1</td>
      <td>0.30</td>
      <td>4.3</td>
    </tr>
    <tr>
      <th>14</th>
      <td>1</td>
      <td>1.10</td>
      <td>7.3</td>
    </tr>
    <tr>
      <th>20</th>
      <td>2</td>
      <td>3.60</td>
      <td>19.3</td>
    </tr>
    <tr>
      <th>26</th>
      <td>2</td>
      <td>1.53</td>
      <td>10.8</td>
    </tr>
    <tr>
      <th>36</th>
      <td>1</td>
      <td>2.90</td>
      <td>14.3</td>
    </tr>
    <tr>
      <th>38</th>
      <td>2</td>
      <td>0.02</td>
      <td>3.8</td>
    </tr>
    <tr>
      <th>39</th>
      <td>2</td>
      <td>1.73</td>
      <td>12.3</td>
    </tr>
    <tr>
      <th>41</th>
      <td>2</td>
      <td>3.53</td>
      <td>13.8</td>
    </tr>
    <tr>
      <th>49</th>
      <td>2</td>
      <td>0.49</td>
      <td>5.3</td>
    </tr>
    <tr>
      <th>53</th>
      <td>2</td>
      <td>1.10</td>
      <td>7.3</td>
    </tr>
    <tr>
      <th>58</th>
      <td>2</td>
      <td>10.20</td>
      <td>40.3</td>
    </tr>
    <tr>
      <th>60</th>
      <td>2</td>
      <td>0.03</td>
      <td>3.3</td>
    </tr>
    <tr>
      <th>62</th>
      <td>2</td>
      <td>0.36</td>
      <td>4.3</td>
    </tr>
    <tr>
      <th>66</th>
      <td>2</td>
      <td>0.85</td>
      <td>6.3</td>
    </tr>
    <tr>
      <th>75</th>
      <td>2</td>
      <td>1.39</td>
      <td>8.3</td>
    </tr>
    <tr>
      <th>76</th>
      <td>2</td>
      <td>1.64</td>
      <td>9.3</td>
    </tr>
  </tbody>
</table>
</div>



# Numero de pasajeros en taxis por Hora

Agruparemos los taxis por dia y sumaremos todos los pasajeros para mostrar

vendorID tpep_pickup_datetime passenger_count




```python
qwerty=nyc20151pd.groupby(nyc20151pd.tpep_pickup_datetime.dt.hour).passenger_count.sum()
qwerty.head
```






    <bound method Series.head of tpep_pickup_datetime
    0      812590
    1      614577
    2      464257
    3      345682
    4      245561
    5      204041
    6      415329
    7      724645
    8      909290
    9      943225
    10     937121
    11     996254
    12    1068850
    13    1072042
    14    1112355
    15    1103702
    16     979060
    17    1127840
    18    1346525
    19    1362960
    20    1240139
    21    1211079
    22    1179532
    23    1020647
    Name: passenger_count, dtype: int64>



> Respuesta : Se consideraria hora punta a las 17 horas debido

>  a su gran numero de pasajeros en los taxis en NYC.

```python

```


[back to the homepage]({{ site.baseurl }}).
