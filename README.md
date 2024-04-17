<div align="center">

# Sistema de Predicción de Incendios en Galicia
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
  
## Introducción
<br>

El objetivo de este proyecto es crear desde cero un sistema capaz de evaluar a tiempo real el __riesgo de incendios en todo el territorio gallego__. Para ello hemos estructurado el proyecto en tres fases: 
__Creación del dataset__ donde obtenemos y estructuramos datos de incendios en Galicia en los ultimos 15 años, así como otras variables de interés. 
Entrenar y evaluar el funcionamiento de un modelo de __Machine Learning__.
Usar este modelo para hacer predicciones con __datos a tiempo real__.

****
## Fase 1: Creación del dataset
------------

<h2>
  
# Obtención de los datos

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

Los resultados obtenidos al calcular "Feature Importance" validan nuestra decisión inicial de seleccionar estaciones meteorológicas que registran la humedad del suelo y foliar, ya que estas variables se revelaron esenciales para optimizar el rendimiento de los modelos.

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
<br>
De cara a mejorar estos resultados, tomamos dos vías de acción:

  - Por un lado, Al representar los datos en un mapa, pronto nos dimos cuenta que el modelo memorizaba el patrón de coordenadas y lo repetía de forma sistemática sin tener en cuenta otras variables. Así que procedimos a eliminar Longitud y Latitud del entrenamiento.
  
  - Y por otro decidimos probar a entrenar una red neuronal.

<br>
<br>

------------
<h2>
  
## NDVI

<br>

<div align="center">
  
<img src="https://github.com/OscarDomPer/Incendios/blob/main/Im%C3%A1genes/NDVI2.png?raw=true" width="80%">

  
</div>
El problema de este enfoque es que se perdía la información geoespacial del modelo (No habría diferencia entre una gran ciudad o una masa de agua y un bosque). La manera de solucionar esto fue añadiendo una nueva variable que tuviese en cuenta la densidad de vegetación.
El parámetro que elegimos fue  el NDVI (normalized difference vegetation index), que sin entrar en muchos detalles mide por satélite la cantidad de luz absorbida por la clorofila. 
Obtuvimos el NDVI del Google Earth Engine que es un visor de datos satelitales con un amplio catálogo de datos. Entre ellos los necesarios para calcularlo.
<br>
<br>

------------
<h2>
  
## Red Neuronal

<br>

<div align="center">
  
<img src="https://github.com/OscarDomPer/Incendios/blob/main/Im%C3%A1genes/red.png?raw=true" width="80%">

  
</div>
De cara a seguir mejorando nuestro proyecto probamos una red neuronal.
La arquitectura consta de dos capas densas con funciones activación ReLu y regularización L1, destinada a prevenir el sobreajuste. Se utiliza dropout para la regularización durante el entrenamiento. El modelo se compila con el optimizador Adam y la función de pérdida de entropía binaria. Se incorporan los callbaks EarlyStopping y ReduceLRonPlateau para mejorar la generalización y ajustar la tasa de aprendizaje.
<br>
<br>
<br>
<div align="center">

  <img src="https://github.com/OscarDomPer/Incendios/blob/main/Im%C3%A1genes/hiperp.png?raw=true" width="80%">

</div>
Para la optimización de los hiperparámetros de la red usamos la web espicializada: Weights & Biases. Los resultados en el conjunto de pruebra no eran malos, pero no mejoraban al modelo anterior. Pero una vez más nuestro objetivo final es un sistema de predicción a tiempo real. Nos interesa que el modelo generalice bien, no que sea muy bueno prediciendo en el conjunto de prueba.
<br>
<br>
<br>
<div align="center">

  <img src="https://github.com/OscarDomPer/Incendios/blob/main/Im%C3%A1genes/resultados%20red.png?raw=true" width="80%">

</div>
Para ello pasamos el modelo por los datos del año 2022 que al igual que en el proyecto anterior, habían sido separados del los conjuntos de entrenamiento y prueba.
Y en este caso el modelo funcionaba incluso mejor pasando de un 77% de nuestro modelo grideado a un 79%
Es decir el modelo generaliza mejor que ajusta.
<br>
<br>
<br>
<div align="center">

  <img src="https://github.com/OscarDomPer/Incendios/blob/main/Im%C3%A1genes/comparativa_2.png?raw=true" width="80%">

</div>
Aquí es donde se ve claramente la gran mejora con respecto al primer modelo.
Ahora el modelo es capaz de predecir los incendios de 2022 de una forma mucho más eficiente.
<br>
<br>
<br>
<div align="center">

  <img src="https://github.com/OscarDomPer/Incendios/blob/main/Im%C3%A1genes/comp_inc.png?raw=true" width="80%">

</div>
En estos mapas se observan los incendios que sucedieron en 2022 frente a los que predice el modelo.
Nótese que al sistema no se le da ningún tipo de información geoespacial. Sitúa los incendios en base a las demás variables.
<br>
<br>
<br>
<div align="center">

  <img src="https://github.com/OscarDomPer/Incendios/blob/main/Im%C3%A1genes/mapas5.png?raw=true" width="80%">

</div>
Distintos tipos de mapas, cada uno proporciona una aproximación distinta y en su conjunto ofrecen una visión general.
Algunos representan los resultado de predict con un umbral determinado otros  representan la probabilidad de incendio a partir de los datos de predict directamente, la mayoría son interactivos.
<br>
<br>

------------
<h2>
  
## Predicción a tiempo real
<br>

<div align="center">
  
<img src="https://github.com/OscarDomPer/Incendios/blob/main/Im%C3%A1genes/realtime.png?raw=true">

  
</div>
El fin último y más importante reto de nuestro proyecto, era crear un sistema de predicción de riesgo de incendio a tiempo real.
Para ello, obviamente, necesitábamos datos a tiempo real de las estaciones metereológicas (otra limitación del sistema anterior es que los datos climáticos eran una media de todo el día).
Conseguimos acceder a los datos diez-minutales (aunque se suben con un retraso de una hora o dos)) de MeteoGalicia de forma automatizada.
Una vez descargados los datos, filtramos las estaciones que recojan humedad foliar y humedad de suelo y nos quedamos con las variables que nos interesan.
<br>
<br>
<br>
<div align="center">

  <img src="https://github.com/OscarDomPer/Incendios/blob/main/Im%C3%A1genes/mapas5.png?raw=true" width="80%">

</div>
Hacia el final de nuestro proyecto descubrimos que la Xunta tiene una herramienta similar a nuestro modelo, no ofrece mucha información acerca de cómo calcula el índice, o las variables que tiene en cuenta, pero nos sirve para compararlo con nuestro sistema.

El mapa es un jpg que se  sube cada día, generalmente a las 9:45. 
<br>
<br>
<br>
<div align="center">

  <img src="https://github.com/OscarDomPer/Incendios/blob/main/Im%C3%A1genes/xunta1.png?raw=true" width="80%">

</div>
Con las variables climáticas a tiempo real, creamos un dataset con coordenadas aleatorias de forma análoga a lo que hicimos con los datos de un día concreto de nuestro dataset y le pasamos nuestro modelo.

Si nos fijamos en el patrón, no tanto en los colores que obedecen al umbral con el que trabajamos o los rangos de probabilidad que elegimos, observamos cierta consistencia con el índice de la Xunta de Galicia. Lo que nos hace pensar que las decisiones que tomamos a lo largo de todo el proyecto fueron correctas en su mayoría.
<br>
<br>
<br>
<br>

------------
<h2>

**Autores:**

- **Carlos Lara** - [Linkedin](https://www.linkedin.com/in/carloslarabarrera/)
- **René Pupo Martínez** - [Linkedin](https://www.linkedin.com/in/rene-pupo-mart%C3%ADnez-a994822a7/)
- **Juan Pablo Aguilar**
- **Óscar Dominguez** - [Linkedin](https://www.linkedin.com/in/oscardominguezpereira/)
























