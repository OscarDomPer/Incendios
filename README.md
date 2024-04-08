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
<br>
<div align="center">

  <img src="https://github.com/OscarDomPer/Incendios/blob/main/Im%C3%A1genes/EDA.png?raw=true" width="100%">
  
</div>

Gráfica 1: Distribución Mensual de Incendios en cada Año. Se observa una mayor frecuencia en los meses de más calor, como era de esperar.
<br>
Gráfica 2: Incendios Según la Hora del Día. Muestra una distribución inusual, debido a que los datos son recogidos por satélites de órbita corta que pasan varias veces al día por el mismo punto.
<br>
Gráfica 3: Comparativa de Variables Climáticas. Al comparar la media de variables climáticas durante eventos de incendio y no incendio, como era de esperar,  el viento y la temperatura elevada están asociados con la ocurrencia de incendios, mientras que una mayor humedad, en todas sus formas, se relaciona con una menor frecuencia de estos eventos. 

<br>
<br>

------------
<h2>
  
## Desarrollo del modelo de Machine Learning

<br>

<div align="center">
  
<img src="https://github.com/OscarDomPer/Incendios/blob/main/Im%C3%A1genes/modelos.png?raw=true" width="80%">

  
</div>
Entrenamos los modelo de Machine Learning más comunes y evaluamos su desempeño con el conjunto de prueba. Los resultados fueron en general buenos, aunque los modelos basados en árboles de decisión presentaban un sobreajuste inaceptable. 
Decidimos centrarnos en Gradient Boosting y AdaBoost, al tener estos las mejores métricas.
<br>
<br>

<div align="center">
  
<img src="https://github.com/OscarDomPer/Incendios/blob/main/Im%C3%A1genes/tuning.png?raw=true" width="70%">

  
</div>
La mejora de los hiperparámetros para los modelos GradientBoostingClassifier y AdaBoostClassifier se llevó a cabo mediante GridSearchCV. 
Para el GradientBoostingClassifier, ajustamos hiperparámetros  como el número de estimadores, la tasa de aprendizaje y la profundidad máxima de los árboles. El mejor conjunto de hiperparámetros se validó utilizando la métrica ROC-AUC en el conjunto de prueba. Con el AdaBoostClassifier, también optimizamos el número de estimadores y la tasa de aprendizaje, evaluando el mejor modelo con la misma métrica ROC-AUC.

<br>

En el caso del GradientBoostingClasiffier la mejora tras la hiperparametrización
ha sido notable, pasando de un  ROC-AUC de 0,81 a 0,85.

<br>
<br>
<br>
<div align="center">

  <img src="https://github.com/OscarDomPer/Incendios/blob/main/Im%C3%A1genes/importances.png?raw=true" width="100%">
  
</div>

Los resultados obtenidosal calcular "Feature Importance" validan nuestra decisión inicial de seleccionar estaciones meteorológicas que registran la humedad del suelo y foliar, ya que estas variables se revelaron esenciales para optimizar el rendimiento de los modelos.

Resulta interesante destacar la relativa menor relevancia de la variable 'viento' en nuestros modelos. Una posible explicación para esto podría ser que, aunque un viento fuerte es un factor crítico para la propagación de incendios, la intensidad del viento tiende a incrementarse significativamente durante eventos extremos o temporales.
<br>
<br>

------------
<h2>
  
## Validación en el conjunto de datos reservado

<br>

<div align="center">
  
<img src="https://github.com/OscarDomPer/Incendios/blob/main/Im%C3%A1genes/2022.png?raw=true" width="80%">

  
</div>
Dado que el objetivo final del proyecto es hacer un sistema de predicción a tiempo real, reservamos del estrenamiento y testeo todos los datos  del año 2022, con el fin de evaluar la capacidad de gerenalizar del modelo. Los resultados fueron más bien decepcionantes, apenas mejorando un modelo dummie en el caso de la detección de positivos.








