# Tutorial segemntación de tumores cerebrales con U-Net

Bienvenido al tutorial de U-Net, donde exploraremos el proceso paso a paso para implementar y entrenar una red neuronal para la segmentación de tumores cerebrales en imágenes de resonancia magnética utilizando la arquitectura U-Net.

## **Instrucciones:**
El tutorial se organiza en una serie de 7 pasos para realizar el proceso de segmentación de inicio a fin:

## **Paso 0 - Definición del Problema**

Segmentación de gliomas en imágenes de resonancia magnética preoperatorias.

Los tumores cerebrales son crecimientos anómalos de tejido que pueden causar complicaciones de salud al ejercer presión en el cerebro. Estas complicaciones varían según la ubicación del tumor y pueden incluir resistencia a la terapia, problemas de suministro sanguíneo y efectos secundarios como edema e hipertensión intracraneal.

Las técnicas de imagen, especialmente la resonancia magnética (MRI), son fundamentales para detectar tumores cerebrales. La segmentación manual en las imágenes es laboriosa y propensa a variabilidad entre evaluadores. Para abordar esto, se han desarrollado métodos automatizados, como el algoritmo U-Net, que pertenece a la familia de técnicas de aprendizaje profundo.

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
**Paso 2 - Descargar el Conjunto de Datos / Exploración de Datos**

Para este tutorial descargaremos el conjunto de datos BraTS2020, proporcionado por los organizadores del desafío BraTS 2020 y contienen exploraciones de resonancia magnética multimodal preoperatorias clínicamente adquiridas de glioblastoma (GBM/HGG) y glioma de bajo grado (LGG), que incluyen (a) volúmenes nativos (T1) y (b) post-contraste ponderados en T1 (T1Gd), (c) ponderados en T2 (T2), y (d) recuperación de inversión atenuada por líquido (FLAIR) (más detalles en la última parte).

Para descargar el conjunto de datos de entrenamiento:

```
!wget 'https://www.cbica.upenn.edu/MICCAI_BraTS2020_TrainingData'
```
Para descargar el conjunto de datos de validación:

```
!wget 'https://www.cbica.upenn.edu/MICCAI_BraTS2020_ValidationData'
```

**Preprocesamiento de MRI en la segmentación de tumores**

En el análisis de la resonancia magnética estructural del cerebro, se llevan a cabo procedimientos específicos de preprocesamiento antes de la segmentación automatizada. Estos suelen incluir tareas como registro de imágenes, eliminación del cráneo y corrección del campo de sesgo.

Si el conjunto de datos que va a utilizar no posee preprocesamiento, debe realizarlo previamente.

**Exploración de datos**
Se realiza una primera visualización de las imágenes y sus máscaras de segmentación, para identificar que si correspondan a la delimitación del tumor, así como para la detección de imagenes con componenetes artefactuales que dificulten el entrenamiento del modelo.

**FIFI**

**Paso 3 - Crear el Modelo || U-Net**
Implementaremos la arquitectura U-Net para la segmentación de tumores cerebrales y analizaremos sus parámetros y hiperparámetros.

**ISA**

**Paso 4 - Preparar Datos para el Entrenamiento**
Acondicionaremos los datos para que sean adecuados para el entrenamiento de la red neuronal.

**FIFI**

**Paso 5 - Entrenar el Modelo**
Realizaremos el proceso de entrenamiento de la red U-Net con el conjunto de datos preparado.

**ISA**

**Paso 6 - Predecir Segmentaciones de Tumores**
Utilizaremos el modelo entrenado para realizar predicciones de segmentación en nuevas imágenes.

**Paso 7 - Evaluar el Modelo**
Evaluar el rendimiento del modelo en términos de precisión y eficacia en la tarea de segmentación de tumores cerebrales.

Siga cada paso detenidamente para obtener una comprensión completa del proceso y lograr una implementación exitosa de U-Net en la tarea de segmentación médica. 
