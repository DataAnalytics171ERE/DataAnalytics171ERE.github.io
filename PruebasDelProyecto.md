
.markdown-here-wrapper {
  font-family: Verdana, sans;
}

---

### <span style="color: #970B0B; font-family: Babas; font-size: 3em;">Pruebas</span>
---

Para nuestras pruebas usamos dos archivos csv cada uno de 1.8 GB aproximadamente el cual consiste en datos de lo staxis de New York City del año 2015 en los meses de Enero y Febrero.

![taxi](taxi.png)

Esos archivos fueron puestos en HDFS local en una laptop dando como resultado lo siguiente :

![gg](hdfs1.png)

 #### <span style="color: #970B0B; font-family: Babas; font-size: 2em;">Codigo</span>


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

    [{'owner': 'geckolml', 'kind': 'file', 'last_access': 1495301469, 'group': 'supergroup', 'last_mod': 1494590508, 'name': '/user/data/tripData/yellow_tripdata_2015-01.csv', 'block_size': 134217728, 'replication': 1, 'size': 1985964692, 'permissions': 420}, {'owner': 'geckolml', 'kind': 'file', 'last_access': 1494766894, 'group': 'supergroup', 'last_mod': 1494590599, 'name': '/user/data/tripData/yellow_tripdata_2015-02.csv', 'block_size': 134217728, 'replication': 1, 'size': 1945357622, 'permissions': 420}]


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


#####  <span style="color: #970B0B; font-family: Babas; font-size: 2em;"> Evaluamos los tiempos de ejecución entre Dataframe de pandas y Dask Dataframe</span>


```python
%time nyc20151[nyc20151.tip_amount == 0].payment_type.value_counts().compute()
```

    CPU times: user 49.9 s, sys: 8.23 s, total: 58.2 s
    Wall time: 1min 6s





    2    4816808
    1     270456
    3      38567
    4      11934
    5          2
    Name: payment_type, dtype: int64




```python
%time nyc20151pd[nyc20151pd.tip_amount == 0].payment_type.value_counts()
```

    CPU times: user 776 ms, sys: 100 ms, total: 876 ms
    Wall time: 872 ms





    2    4816808
    1     270456
    3      38567
    4      11934
    5          2
    Name: payment_type, dtype: int64




```python
nyc20151.passenger_count.sum()
```




    dd.Scalar<series-..., dtype=int64>




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
%time nyc20151.payment_type.value_counts()
```

    CPU times: user 0 ns, sys: 0 ns, total: 0 ns
    Wall time: 3.16 ms





    dd.Series<value-c..., npartitions=1>




```python
%time nyc20151[nyc20151.tip_amount == 0].payment_type.value_counts()
```

    CPU times: user 4 ms, sys: 0 ns, total: 4 ms
    Wall time: 4.4 ms





    dd.Series<value-c..., npartitions=1>



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


![png](output_15_0.png)


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




```python
# Esta celda da el estilo al notebook
from IPython.core.display import HTML
css_file = 'style/style.css'
HTML(open(css_file, "r").read())
```




div.text_cell {
width: 105ex /* instead of 100%, */
}

div.text_cell_render {
/*font-family: "Helvetica Neue", Arial, Helvetica, Geneva, sans-serif;*/
font-family: "Charis SIL", serif; /* Make non-code text serif. */
line-height: 145%; /* added for some line spacing of text. */
width: 105ex; /* instead of 'inherit' for shorter lines */
}

/* Set the size of the headers */
div.text_cell_render h1 {
font-size: 18pt;
}

div.text_cell_render h2 {
font-size: 14pt;
}


.CodeMirror {
font-family: Consolas, monospace;
}






```python

```
