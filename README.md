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







