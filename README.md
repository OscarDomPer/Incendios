<div align="center">

# Titulo Provisional
</div>

<br>
<br>
<br>
<div align="center">

  <img src="https://github.com/OscarDomPer/Incendios/blob/main/Im%C3%A1genes/mapa.png?raw=true" width="60%">
  
</div>

<br>
<br>
## Tecnologías usadas

**Lenguaje:** Python.

**Librerias:** numpy, pandas, matplotlib, seaborn, plotly, requests, BeautifulSoup

<br>
<br>

------------

<h2>
  
## Obtención de los datos

<br>

<div align="center">
  
<img src="https://github.com/OscarDomPer/Incendios/blob/main/Im%C3%A1genes/obte_dat.png?raw=true" width="100%">

  
</div>
  <br>
  
Para generar un dataset completo necesitábamos por un lado, un registro de incendios en Galicia y por otro conocer las condiciones climáticas en las que se dió cada incendio. 
Los incendios los obtuvimos de la plataforma de la NASA 
[firms](https://firms.modaps.eosdis.nasa.gov/) 
que proporciona datos satelitales, reuniendo datos de incendios en España pertenecientes a los últimos 15 años.
Las variables climáticas se descargaron del servicio metereológico gallego 
[MeteoGalicia](https://www.meteogalicia.gal/web/home)


<br>
<br>

<div align="center">

  <img src="https://github.com/OscarDomPer/Incendios/blob/main/Im%C3%A1genes/ensamblaje.png?raw=true" width="100%">
  
</div>
Selecionamos 16 estaciones meteorológicas en Galicia, cuatro por provincia. El criterio para esta elección fue doble: la capacidad de las estaciones para registrar humedad del suelo y foliar, y una distribución geográfica que abarcara de forma amplia la región.

Para la recopilación de datos sobre incendios, empleamos un proceso de localización inversa, identificando así los incendios ocurridos en Galicia. Luego, asociamos a cada incendio los datos climáticos registrados por la estación meteorológica más cercana en la fecha del evento. 

Finalmente, al tratarse de un problema de clasificación, generamos filas negativas en el dataset. Utilizamos la librería 'random' para crear coordenadas y fechas aleatorias dentro de nuestros límites temporales y territoriales. A estas coordenadas, les adjuntamos los datos climáticos correspondientes de la estación meteorológica más cercana.

  <br>

El código de este proceso puede consultarse en los siguientes notebooks:
https://github.com/OscarDomPer/Incendios/blob/main/000.Creacion_Data_Incendios_Galicia.ipynb
https://github.com/OscarDomPer/Incendios/blob/main/000.Creacion_Data_NO_Incendios_Galicia.ipynb
https://github.com/OscarDomPer/Incendios/blob/main/001.Union_y_ajustes_DataSet_Incendios_Galicia.ipynb

<br>
<br>

------------
<h2>
  
## Análisis Exploratorio de los datos

<br>

<div align="center">
  
<img src="https://github.com/OscarDomPer/Incendios/blob/main/Im%C3%A1genes/correlacion.png?raw=true" width="80%">

  
</div>
Una vez realizados los ajustes básicos(sustitución de datos faltantes utilizando k-nearest neighbors, eliminación de outliers), al calcular la matriz de correlación para las variables numéricas de nuestro dataset, encontramos una correlación casi perfecta con algunos datos satelitales, como “Brightness” o “Scan”. Esta alta correlación se debe a que el satélite en sí actúa como un sistema de detección de incendios. Así que optamos por eliminarlos. 
<br>
<br>

<div align="center">

  <img src="https://github.com/OscarDomPer/Incendios/blob/main/Im%C3%A1genes/EDA.png?raw=true" width="100%">
  
</div>

Gráfica 1: Distribución Mensual de Incendios en cada Año. Se observa una mayor frecuencia en los meses de más calor, como era de esperar.

<br>

Gráfica 2: Incendios Según la Hora del Día. Muestra una distribución inusual, debido a que los datos son recogidos por satélites de órbita corta que pasan varias veces al día por el mismo punto.

<br>

Gráfica 3: Comparativa de Variables Climáticas.C omparando la media de variables climáticas durante eventos de incendio y no incendio, como era de esperar,  el viento y la temperatura elevada están asociados con la ocurrencia de incendios, mientras que una mayor humedad, en todas sus formas, se relaciona con una menor frecuencia de estos eventos. 








