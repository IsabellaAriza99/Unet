# Segementación de tumores cerebrales con U-Net

<p align="justify">
Bienvenido al tutorial de U-Net, donde exploraremos el proceso paso a paso para implementar y entrenar una red neuronal para la segmentación de tumores cerebrales en imágenes de resonancia magnética utilizando la arquitectura U-Net.
</p>

## **Instrucciones:**
El tutorial se organiza en una serie de 7 pasos para realizar el proceso de segmentación de inicio a fin:
**ISA**
## **Paso 0 - Definición del Problema**

Segmentación de gliomas en imágenes de resonancia magnética preoperatorias.
<p align="justify">
Los tumores cerebrales son crecimientos anómalos de tejido que pueden causar complicaciones de salud al ejercer presión en el cerebro. Estas complicaciones varían según la ubicación del tumor y pueden incluir resistencia a la terapia, problemas de suministro sanguíneo y efectos secundarios como edema e hipertensión intracraneal.
</p>
<p align="justify">
Las técnicas de imagen, especialmente la resonancia magnética (MRI), son fundamentales para detectar tumores cerebrales. La segmentación manual en las imágenes es laboriosa y propensa a variabilidad entre evaluadores. Para abordar esto, se han desarrollado métodos automatizados, como el algoritmo U-Net, que pertenece a la familia de técnicas de aprendizaje profundo.
</p>

En la segmentación de imágenes cada píxel en la imagen debe ser asignado a una etiqueta:

Si el píxel forma parte de una zona tumoral (1, 2 o 3) -> puede pertenecer a una de varias clases / subregiones.

De lo contrario -> el píxel no está en una región tumoral (0).

Las subregiones del tumor consideradas para la evaluación son: 
1) El "tumor realzado" (ET)
2) El "núcleo del tumor" (TC)
3) El "tumor completo" (WT)
   
Las etiquetas de segmentación proporcionadas tienen valores de 1 para NCR y NET, 2 para ED, 4 para ET y 0 para cualquier otra región.

![Brats2020](https://www.med.upenn.edu/cbica/assets/user-content/images/BraTS/brats-tumor-subregions.jpg)

## **Paso 1 - Configurar el entorno de trabajo**
Prepararemos el entorno de trabajo para garantizar que todas las dependencias estén instaladas y listas para su uso.

Las librerías utilizadas son:

**Manipulación y visualización de imagenes**

- skimage, matplotlib, numpy, pandas, seaborn, nilearn, nibabel.

**Desarrollo y evaluación del modelo de Deep Learning**

- Keras, Tensorflow.

Puedes instalar los requerimientos así:
```
pip install -r requirements.txt
```
## **Paso 2 - Descargar el Conjunto de Datos / Exploración de Datos**

<p align="justify">
Para este tutorial descargaremos el conjunto de datos BraTS2020, proporcionado por los organizadores del desafío BraTS 2020 y contienen exploraciones de resonancia magnética multimodal preoperatorias clínicamente adquiridas de glioblastoma (GBM/HGG) y glioma de bajo grado (LGG), que incluyen (a) volúmenes nativos (T1) y (b) post-contraste ponderados en T1 (T1Gd), (c) ponderados en T2 (T2), y (d) recuperación de inversión atenuada por líquido (FLAIR) (más detalles en la última parte).
</p>

Para descargar el conjunto de datos de entrenamiento:

```
!wget 'https://www.cbica.upenn.edu/MICCAI_BraTS2020_TrainingData'
```
Para descargar el conjunto de datos de validación:

```
!wget 'https://www.cbica.upenn.edu/MICCAI_BraTS2020_ValidationData'
```

**Preprocesamiento de MRI en la segmentación de tumores**

<p align="justify">
En el análisis de la resonancia magnética estructural del cerebro, se llevan a cabo procedimientos específicos de preprocesamiento antes de la segmentación automatizada. Estos suelen incluir tareas como registro de imágenes, eliminación del cráneo y corrección del campo de sesgo.
</p>

Si el conjunto de datos que va a utilizar no posee preprocesamiento, debe realizarlo previamente.

**Exploración de datos**

<p align="justify">
Se realiza una primera visualización de las imágenes y sus máscaras de segmentación, para identificar que si correspondan a la delimitación del tumor por medio de las librerías nilearn, nibabel, matplotlib, así como para la detección de imagenes con componenetes artefactuales que dificulten el entrenamiento del modelo.
</p>

## **Paso 3 - Crear el Modelo || U-Net** 

En esta sección, vamos a construir y configurar el modelo U-Net para la segmentación de tumores cerebrales en imágenes de resonancia magnética. U-Net es una arquitectura de red neuronal convolucional diseñada específicamente para tareas de segmentación de imágenes médicas. Su estructura única facilita la captura de contextos locales y globales en las imágenes, lo cual es crucial para la identificación precisa de regiones tumorales.

**A - Definición del Modelo**

El modelo U-Net se caracteriza por su arquitectura simétrica, que incluye un camino de contracción (codificador) y un camino de expansión (decodificador), permitiendo una segmentación precisa a nivel de píxel. La conexión de salto entre el codificador y el decodificador ayuda a recuperar la información espacial perdida durante el proceso de agrupación máxima.

El modelo se construye en Keras y TensorFlow, utilizando capas de convolución, capas de agrupación máxima para la reducción de dimensiones, y capas de upsampling para la reconstrucción de la imagen segmentada. Además, aplicamos la técnica de dropout para reducir el sobreajuste.

**B - Pérdida**

Para entrenar nuestro modelo, utilizamos la pérdida de entropía cruzada categórica y una serie de métricas personalizadas, incluyendo la precisión, sensibilidad, especificidad y el coeficiente de Dice para diferentes subregiones del tumor. El coeficiente de Dice es una medida estadística que ayuda a evaluar la similitud entre la segmentación predicha y la segmentación real, siendo crucial para validar la eficacia del modelo en tareas de segmentación.

**C - Función de Activación de Salida**

Para obtener esta distribución de probabilidad sobre las diferentes clases para cada píxel, aplicamos una función de activación softmax a la capa de salida de nuestra red neuronal.

Esto significa que durante el entrenamiento, nuestra CNN ajustará sus pesos para minimizar nuestra función de pérdida, la cual compara las probabilidades predichas dadas por la función softmax con las de la segmentación de verdad fundamental.

**D - Métricas**

También es importante monitorear el rendimiento del modelo utilizando métricas de evaluación.

Por supuesto, utilizaremos la precisión, que es una medida muy popular. Sin embargo, esta métrica puede ser engañosa cuando se trabaja con conjuntos de datos desequilibrados como BraTS2020, donde la clase de Fondo está sobre representada. Para abordar este problema, utilizaremos otras métricas como la intersección sobre la unión (IoU), el coeficiente de Dice, precisión, sensibilidad y especificidad.

- Precisión (Accuracy): Mide la proporción general de píxeles clasificados correctamente, incluyendo tanto píxeles positivos como negativos.

- Intersección sobre Unión (IoU): Mide la superposición entre las segmentaciones predichas y las segmentaciones de verdad fundamental.

- Sensibilidad (Recall o Tasa de Positivos Verdaderos): Mide la proporción de píxeles positivos de verdad fundamental que fueron predichos correctamente como positivos.

- Precisión (Valor Predictivo Positivo): Mide la proporción de píxeles positivos predichos que son realmente positivos.

- Especificidad (Tasa de Negativos Verdaderos): Mide la proporción de píxeles negativos de verdad fundamental que fueron predichos correctamente como negativos.

**E - Compilación**

Una vez definido el modelo, lo compilamos con el optimizador Adam. La tasa de aprendizaje y otros hiperparámetros pueden ser ajustados según la necesidad. 

## **Paso 4 - Preparar Datos para el Entrenamiento**

**A - Split data into 3 sets**
Acondicionaremos los datos para que sean adecuados para el entrenamiento de la red neuronal.

Dividiremos nuestro conjunto de datos en 

Training - 68% 
Test - 15%
Validation - 17%

**B - Crear un Generador de Datos**

Para entrenar una red neuronal en la segmentación de objetos en imágenes, es necesario alimentarla tanto con los datos brutos de la imagen (X) como con las segmentaciones de verdad fundamental (y). Combinando estos dos tipos de datos, la red neuronal puede aprender a reconocer patrones de tumores y hacer predicciones precisas sobre el contenido del escáner de un paciente.

Desafortunadamente, nuestras imágenes de modalidades (X) y nuestras segmentaciones (y) no pueden ser enviadas directamente al modelo de IA. Cargar todas estas imágenes 3D sobrecargaría la memoria de nuestro entorno y podría causar la caída del sistema. Esto también llevaría a errores de desajuste de forma. Por lo tanto, es necesario realizar un preprocesamiento de imágenes antes de su uso, lo cual se hace mediante un Generador de Datos, donde realizamos cualquier operación que consideremos necesaria al cargar las imágenes.

Más específicamente, para cada muestra:

- Recuperamos las rutas de todas sus modalidades (FLAIR, T1CE, T1 y T2), ya que cada una de estas proporciona información única y complementaria sobre la anatomía y el contraste de tejidos del cerebro.
- Recuperamos la ruta de la Verdad Fundamental (segmentación original).
- Cargamos todas las modalidades y la segmentación.
- Creamos un arreglo X que contendrá todas las rebanadas seleccionadas (60-135) de estas modalidades.
- Creamos un arreglo y que contendrá todas las rebanadas seleccionadas de la segmentación.
- Asignamos a todos los 4 en el arreglo de máscara el valor 3 (para corregir el caso faltante explicado anteriormente).

## **Paso 5 - Entrenar el Modelo** 

**A - Callbacks**
Claro, aquí tienes un párrafo sobre los callbacks inspirado en tu código y explicación:

Los callbacks son funciones que se pueden ejecutar durante el proceso de entrenamiento de un modelo de aprendizaje automático y son esenciales para optimizar este proceso y guardar información relevante.

En nuestro caso, utilizaremos tres callbacks específicos:

    ReduceLROnPlateau: Este callback reduce la tasa de aprendizaje cuando una métrica ha dejado de mejorar, en nuestro caso, la pérdida de validación. La tasa de aprendizaje se reduce por un factor de 0.2 después de 2 épocas sin mejoras, y la tasa mínima de aprendizaje se establece en 0.000001. Esta técnica ayuda a refinar el entrenamiento cuando nos acercamos a un óptimo local.

    ModelCheckpoint: Guarda los mejores pesos del modelo (el modelo que ha obtenido la menor pérdida de validación durante las diferentes épocas). Guardar un modelo nos permite reutilizarlo más tarde o compartirlo, sin tener que entrenarlo desde cero. ¡Esto nos ahorra tiempo y recursos! En nuestro código, se guardarán estos pesos en archivos nombrados con el formato 'model_.{epoch:02d}-{val_loss:.6f}.m5'.

    CSVLogger: Añade métricas a un archivo CSV, que en nuestro caso se llama 'training.log'. El parámetro append está establecido en False, lo que significa que el archivo se sobrescribirá si ya existe. Esto nos permite tener un registro detallado del proceso de entrenamiento para su análisis posterior.

El uso de estos callbacks no solo mejora la eficiencia del proceso de entrenamiento, sino que también proporciona herramientas valiosas para monitorear y ajustar el rendimiento del modelo.

**B-Entranamiento**

Ahora estamos listos para entrenar nuestra red neuronal profunda utilizando el método .fit() en Keras. Pasaremos nuestros 3 callbacks al método para que se ejecuten durante el proceso de entrenamiento, el cual durará 35 épocas.

## **Paso 6 - Predecir Segmentaciones de Tumores**

Utilizaremos el modelo entrenado para realizar predicciones de segmentación en nuevas imágenes.

En este caso usaremos las imagenes del conjunto de test y compararemos con la máscara de segmentación manual.

La función utilizada es `predictByPath()`

## **Paso 7 - Evaluar el Modelo**
Evaluaremos el rendimiento del modelo en términos de precisión y eficacia en la tarea de segmentación de tumores cerebrales.

Para eso utilizaremos la función `showPredictsById()`

Siga cada paso detenidamente para obtener una comprensión completa del proceso y lograr una implementación exitosa de U-Net en la tarea de segmentación médica. 
