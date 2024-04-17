<div align="center">

# Sistema de Predicción de Incendios en Galicia
</div>

<br>
<div align="center">

  <img src="https://github.com/OscarDomPer/Incendios/blob/main/Im%C3%A1genes/mapa.png?raw=true" width="60%">
  
</div>

<br>
<br>

## Tecnologías usadas

**Lenguaje:**
![Python](https://img.shields.io/badge/-Python-3776AB?style=flat&logo=python&logoColor=white)

**Librerías:**
![Pandas](https://img.shields.io/badge/-Pandas-150458?style=flat&logo=pandas&logoColor=white)
![Geopandas](https://img.shields.io/badge/-Geopandas-7D629F?style=flat&logo=geopandas&logoColor=white)
![Geopsy](https://img.shields.io/badge/-Geopsy-478559?style=flat&logo=geopsy&logoColor=white)
![Folium](https://img.shields.io/badge/-Folium-F6993F?style=flat&logo=folium&logoColor=white)
![TensorFlow](https://img.shields.io/badge/-TensorFlow-FF6F00?style=flat&logo=tensorflow&logoColor=white)
![Requests](https://img.shields.io/badge/-Requests-AC3B61?style=flat&logo=requests&logoColor=white)
![Shapely](https://img.shields.io/badge/-Shapely-5F9EA0?style=flat&logo=shapely&logoColor=white)
![Joblib](https://img.shields.io/badge/-Joblib-4285F4?style=flat&logo=joblib&logoColor=white)
![Scikit-learn](https://img.shields.io/badge/-Scikit_learn-FBC02D?style=flat&logo=scikit-learn&logoColor=white)
![Matplotlib](https://img.shields.io/badge/-Matplotlib-388E3C?style=flat&logo=matplotlib&logoColor=white)
![Seaborn](https://img.shields.io/badge/-Seaborn-0277BD?style=flat&logo=seaborn&logoColor=white)
![Imbalanced-learn](https://img.shields.io/badge/-Imbalanced_learn-5D4037?style=flat&logo=imbalanced-learn&logoColor=white)
![tqdm](https://img.shields.io/badge/-tqdm-6A1B9A?style=flat&logo=tqdm&logoColor=white)



<br>
<br>




  
## Introducción
<br>

El objetivo de este proyecto es crear desde cero un sistema capaz de evaluar a tiempo real el __riesgo de incendios en todo el territorio gallego__. Para ello hemos estructurado el proyecto en tres fases: 
__Creación del dataset__ donde obtenemos y estructuramos datos de incendios en Galicia en los ultimos 15 años, así como otras variables de interés. 
Entrenar y evaluar el funcionamiento de un modelo de __Machine Learning__.
Usar este modelo para hacer predicciones con __datos a tiempo real__.

<br>
<br>
<br>

****
## Fase 1: Creación del dataset
------------

<br>
  
### Obtención de los datos

<br>

<div align="center">
  
<img src="https://github.com/OscarDomPer/Incendios/blob/main/Im%C3%A1genes/obte_dat.png?raw=true" width="100%">

  
</div>
  <br>
  
Para generar un dataset completo necesitábamos por un lado, un registro de incendios en Galicia y por otro conocer las condiciones climáticas en las que se dió cada incendio. 
Los incendios los obtuvimos de la plataforma de la __NASA__ 
[firms](https://firms.modaps.eosdis.nasa.gov/) 
que proporciona datos satelitales, reuniendo datos de incendios en España pertenecientes a los __últimos 15 años__.
Las variables climáticas se descargaron del __servicio metereológico gallego__. 
[MeteoGalicia](https://www.meteogalicia.gal/web/home)

<br>
<br>
  
### Ensamblaje del dataset

<br>

<div align="center">

  <img src="https://github.com/OscarDomPer/Incendios/blob/main/Im%C3%A1genes/ensamblaje.png?raw=true" width="100%">
  
</div>

Selecionamos __16 estaciones meteorológicas en Galicia__, cuatro por provincia. El criterio para esta elección fue doble: la capacidad de las estaciones para registrar __humedad del suelo y foliar__, y una distribución geográfica que abarcara de forma amplia la región.

Para la recopilación de datos sobre incendios, empleamos un proceso de __localización inversa__, identificando así los incendios ocurridos en Galicia. Luego, asociamos a cada incendio los datos climáticos registrados por la estación meteorológica más cercana en la fecha del evento. 

Finalmente, al tratarse de un problema de clasificación, __generamos filas negativas__ en el dataset. Utilizamos la librería 'random' para crear coordenadas y fechas aleatorias dentro de nuestros límites temporales y territoriales. A estas coordenadas, les adjuntamos los datos climáticos correspondientes de la estación meteorológica más cercana.

  <br>

El código de este proceso puede consultarse en los siguientes notebooks:
https://github.com/OscarDomPer/Incendios/blob/main/000.Creacion_Data_Incendios_Galicia.ipynb
https://github.com/OscarDomPer/Incendios/blob/main/000.Creacion_Data_NO_Incendios_Galicia.ipynb
https://github.com/OscarDomPer/Incendios/blob/main/001.Union_y_ajustes_DataSet_Incendios_Galicia.ipynb

<br>
<br>

### Análisis Exploratorio de los datos

<br>

<div align="center">
  
<img src="https://github.com/OscarDomPer/Incendios/blob/main/Im%C3%A1genes/correlacion.png?raw=true" width="80%">

  
</div>

Una vez realizados los ajustes básicos(sustitución de datos faltantes utilizando k-nearest neighbors, eliminación de outliers), al calcular la matriz de correlación para las variables numéricas de nuestro dataset, encontramos una correlación casi perfecta con algunos datos satelitales, como “Brightness” o “Scan”. Esta alta correlación se debe a que el satélite en sí actúa como un sistema de detección de incendios. __Así que optamos por eliminarlos__. 

<br>
<br>
<div align="center">

  <img src="https://github.com/OscarDomPer/Incendios/blob/main/Im%C3%A1genes/EDA.png?raw=true" width="100%">
  
</div>

- __Gráfica 1__: Distribución Mensual de Incendios en cada Año. Se observa una mayor frecuencia en los meses de más calor, como era de esperar.



- __Gráfica 2__: Incendios Según la Hora del Día. Muestra una distribución inusual, debido a que los datos son recogidos por satélites de órbita corta que pasan varias veces al día por el mismo punto.



- __Gráfica 3__: Comparativa de Variables Climáticas. Al comparar la media de variables climáticas durante eventos de incendio y no incendio, como era de esperar,  el viento y la temperatura elevada están asociados con la ocurrencia de incendios, mientras que una mayor humedad, en todas sus formas, se relaciona con una menor frecuencia de estos eventos. 

<br>

El código de este proceso puede consultarse en el notebook:
https://github.com/OscarDomPer/Incendios/blob/main/002.EDA_datos_Galicia.ipynb

<br>
<br>
<br>

------------
## Desarrollo del modelo de Machine Learning
------------
<br>

<div align="center">
  
<img src="https://github.com/OscarDomPer/Incendios/blob/main/Im%C3%A1genes/modelos.png?raw=true" width="80%">

  
</div>

Entrenamos los modelo de __Machine Learning más comunes__ y evaluamos su desempeño con el conjunto de prueba. Los resultados fueron en general buenos, aunque los modelos basados en árboles de decisión presentaban un sobreajuste inaceptable. 
Decidimos centrarnos en __Gradient Boosting y AdaBoost__, al tener estos las mejores métricas.

<br>
<br>

<div align="center">
  
<img src="https://github.com/OscarDomPer/Incendios/blob/main/Im%C3%A1genes/tuning.png?raw=true" width="70%">

  
</div>

La mejora de los hiperparámetros para los modelos GradientBoostingClassifier y AdaBoostClassifier se llevó a cabo mediante __GridSearchCV.__ 
Para el GradientBoostingClassifier, ajustamos hiperparámetros  como el número de estimadores, la tasa de aprendizaje y la profundidad máxima de los árboles. El mejor conjunto de hiperparámetros se validó utilizando la métrica ROC-AUC en el conjunto de prueba. Con el AdaBoostClassifier, también optimizamos el número de estimadores y la tasa de aprendizaje, evaluando el mejor modelo con la misma métrica ROC-AUC.

<br>

En el caso del GradientBoostingClasiffier la mejora tras la hiperparametrización
ha sido notable, pasando de un  __ROC-AUC de 0,81 a 0,85__.

<br>
<br>
<div align="center">

  <img src="https://github.com/OscarDomPer/Incendios/blob/main/Im%C3%A1genes/importances.png?raw=true" width="100%">
  
</div>

Los resultados obtenidos al calcular "Feature Importance" validan nuestra decisión inicial de seleccionar estaciones meteorológicas que registran la __humedad del suelo y foliar__, ya que estas variables se revelaron esenciales para optimizar el rendimiento de los modelos.
Resulta interesante destacar la relativa menor relevancia de la variable 'viento' en nuestros modelos. Una posible explicación para esto podría ser que, aunque un viento fuerte es un factor crítico para la 
propagación de incendios, la intensidad del viento tiende a incrementarse significativamente durante eventos extremos o temporales.

<br>
<br>

### Validación en el conjunto de datos reservado

<br>

<div align="center">
  
<img src="https://github.com/OscarDomPer/Incendios/blob/main/Im%C3%A1genes/2022.png?raw=true" width="80%">

  
</div>

Dado que el objetivo final del proyecto es hacer un sistema de predicción a tiempo real, reservamos del estrenamiento y testeo todos los __datos  del año 2022__, con el fin de evaluar la capacidad de gerenalizar del modelo. __Los resultados fueron más bien decepcionantes__, apenas mejorando un modelo dummie en el caso de la detección de positivos.

<br>
<br>
<br>

__De cara a mejorar estos resultados__, tomamos dos vías de acción:

  - Por un lado, Al representar los datos en un mapa, pronto nos dimos cuenta que el modelo memorizaba el patrón de coordenadas y lo repetía de forma sistemática sin tener en cuenta otras variables. Así que procedimos a __eliminar Longitud y Latitud del entrenamiento__.
  
  - Y por otro decidimos probar a entrenar una __red neuronal__.

<br>
<br>

### NDVI

<br>

<div align="center">
  
<img src="https://github.com/OscarDomPer/Incendios/blob/main/Im%C3%A1genes/NDVI2.png?raw=true" width="80%">

  
</div>

El problema de eliminar las coordenadas es que se perdía la información geoespacial del modelo (No habría diferencia entre una gran ciudad o una masa de agua y un bosque). La manera de solucionar esto fue añadiendo una nueva variable que tuviese en cuenta la densidad de vegetación.
El parámetro que elegimos fue  el __NDVI (normalized difference vegetation index)__, que sin entrar en muchos detalles mide por satélite la cantidad de luz absorbida por la clorofila. 
Obtuvimos el NDVI del __Google Earth Engine__ que es un visor de datos satelitales con un amplio catálogo de datos. Entre ellos los necesarios para calcularlo.
<br>
<br>

### Red Neuronal

<br>

<div align="center">
  
<img src="https://github.com/OscarDomPer/Incendios/blob/main/Im%C3%A1genes/red.png?raw=true" width="80%">

  
</div>

De cara a seguir mejorando nuestro proyecto probamos una __red neuronal__.
La arquitectura consta de dos capas densas con funciones activación ReLu y regularización L1, destinada a prevenir el sobreajuste. Se utiliza dropout para la regularización durante el entrenamiento. El modelo se compila con el optimizador Adam y la función de pérdida de entropía binaria. Se incorporan los callbaks EarlyStopping y ReduceLRonPlateau para mejorar la generalización y ajustar la tasa de aprendizaje.

<br>
<br>

<div align="center">

  <img src="https://github.com/OscarDomPer/Incendios/blob/main/Im%C3%A1genes/hiperp.png?raw=true" width="80%">

</div>

Para la optimización de los hiperparámetros de la red usamos la web espicializada: __Weights & Biases__. Los resultados en el conjunto de pruebra no eran malos, pero no mejoraban al modelo anterior. Pero una vez más nuestro objetivo final es un sistema de predicción a tiempo real. __Nos interesa que el modelo generalice bien, no que sea muy bueno prediciendo en el conjunto de prueba__.
<br>
<br>
<br>
<div align="center">

  <img src="https://github.com/OscarDomPer/Incendios/blob/main/Im%C3%A1genes/resultados%20red.png?raw=true" width="80%">

</div>

Para ello pasamos el modelo por los datos del año 2022 que al igual que en el proyecto anterior, habían sido separados del los conjuntos de entrenamiento y prueba.
__Y en este caso el modelo funcionaba incluso mejor pasando de un 77% de nuestro modelo grideado a un 79%
Es decir el modelo generaliza mejor que ajusta__.
<br>
<br>
<br>
<div align="center">

  <img src="https://github.com/OscarDomPer/Incendios/blob/main/Im%C3%A1genes/comparativa_2.png?raw=true" width="80%">

</div>

Aquí es donde se ve claramente la gran mejora con respecto al primer modelo.
__Ahora el modelo es capaz de predecir los incendios de 2022 de una forma mucho más eficiente__.
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

El código de este proceso puede consultarse en los siguientes notebooks:
https://github.com/OscarDomPer/Incendios/blob/main/003.Preprocesamiento_y_evaluaciones_con_MODELOS_con_datos_Galicia.ipynb
https://github.com/OscarDomPer/Incendios/blob/main/005.Probar_Modelo.ipynb
https://github.com/OscarDomPer/Incendios/blob/main/006.NDVI.ipynb
<br>
<br>
<br>
<br>
***
## Predicción a tiempo real
****

<br>

<div align="center">
  
<img src="https://github.com/OscarDomPer/Incendios/blob/main/Im%C3%A1genes/realtime.png?raw=true">

  
</div>

__El fin último y más importante reto de nuestro proyecto, era crear un sistema de predicción de riesgo de incendio a tiempo real__.
Para ello, obviamente, necesitábamos datos a tiempo real de las estaciones metereológicas (otra limitación del sistema anterior es que los datos climáticos eran una media de todo el día).
Conseguimos acceder a los __datos diez-minutales__ (aunque se suben con un retraso de una hora o dos)) de MeteoGalicia de forma automatizada.
Una vez descargados los datos, filtramos las estaciones que recojan humedad foliar y humedad de suelo y nos quedamos con las variables que nos interesan.
<br>
<br>
<div align="center">

  <img src="https://github.com/OscarDomPer/Incendios/blob/main/Im%C3%A1genes/mapas5.png?raw=true" width="80%">

</div>

Hacia el final de nuestro proyecto descubrimos que la __Xunta tiene una herramienta similar__ a nuestro modelo, no ofrece mucha información acerca de cómo calcula el índice, o las variables que tiene en cuenta, pero nos sirve para compararlo con nuestro sistema.

El mapa es un jpg que se  sube cada día, generalmente a las 9:45. 
<br>
<br>
<br>
<div align="center">

  <img src="https://github.com/OscarDomPer/Incendios/blob/main/Im%C3%A1genes/xunta1.png?raw=true" width="80%">

</div>
Con las variables climáticas a tiempo real, creamos un dataset con coordenadas aleatorias de forma análoga a lo que hicimos con los datos de un día concreto de nuestro dataset y le pasamos nuestro modelo.

Si nos fijamos en el patrón, no tanto en los colores que obedecen al umbral con el que trabajamos o los rangos de probabilidad que elegimos, observamos cierta consistencia con el índice de la Xunta de Galicia. __Lo que nos hace pensar que las decisiones que tomamos a lo largo de todo el proyecto fueron correctas en su mayoría__.

<br>
<br>
<br>
<br>
El código de este proceso puede consultarse en el siguiente notebook:
https://github.com/OscarDomPer/Incendios/blob/main/011.Automacizaci%C3%B3n_DATA_Estaciones_Meteorologicas_Galicia.ipynb
------------
<h2>

**Autores:**

- **Carlos Lara** - [Linkedin](https://www.linkedin.com/in/carloslarabarrera/)
- **René Pupo Martínez** - [Linkedin](https://www.linkedin.com/in/rene-pupo-mart%C3%ADnez-a994822a7/)
- **Juan Pablo Aguilar**
- **Óscar Dominguez** - [Linkedin](https://www.linkedin.com/in/oscardominguezpereira/)
























