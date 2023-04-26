******************
Procesamiento natural del lenguaje
******************

- UBA-CEIA 2023  
- Materia: Procesamiento natural del lenguaje  
- Profesor: Rodrigo Cárdenas
- Alumno: Fux, Santiago Javier (6ta Cohorte)

En el presente documento se listan los desafíos llevados adelante durante la cursada de la materia Procesamiento Natural del Lenguaje
durante los meses de marzo y abril de 2023.


Ejercicio 1 (Vectorización de texto)
===========================
Introducción
-----------------------------
El objetivo de este desafío es componer un corpus a partir de 3 documentos, obtener los términos y crear vectores con representaciones 
one-hot encoding, frequency encoding y TF-IDF.  

Por último se llevó adelante una comparativa mediante similitud coseno entre los documentos para cada tipo de representación. 

Ejercicio 2 (Bot "simple")
===========================
Introducción
-----------------------------
El objetivo de este ejercicio es implementar un chatbot capaz de responder con algunas de las frases propuestas a partir del texto
de entrada enviado por el usuario. Dicho texto es comparado mediante similtud coseno con la tabla definida para poder identificar la categoría más semejante.

Implementación
-----------------------------
Se utiliza la librería SpaCy-Stanza en español como pipeline para trabajar con los términos del dataset.

El dataset está compuesto por un vector de intenciones. Cada una de ellas contiene los campos tag (categoría), patterns (lista de preguntas posibles a recibir) y 
responses (lista de respuestas posibles a devolver).

Cada uno de los patterns son procesados y categorizados según su tag. Luego se construyen vectores one-hot encoding tanto para el bag of words como para las categorías (tags).
A continuación se construye un modelo con dos capas densas y se entrena con 200 épocas.

.. image:: ejercicio%202/train.png

En la última sección se prueba el modelo entrenado mediante entradas del usuario, las cuales son convertidas a vectores one-hot encoding para luego inferir su categoría.
En base a este resultado se devuelve una respuesta de las provistas en el  listado del campo responses.

.. image:: ejercicio%202/result.png


Ejercicio 3 (Word embedding)
===========================
Introducción
-----------------------------
Este ejercicio propone construir un espacio de embedding a partir de un dataset de entrada. Se propone para tal fin de un dataset 
con el contenido de las letras de todas las canciones de la banda de róck argentina Patricio Rey y sus redonditos de ricota.

Implementación
-----------------------------
Las letras son obtenidas de un archivo txt, el cual es filtrado y tokenizado debidamente. Luego se crea el modelo generador con un modelo Skipgram y se entrena con 100 épocas.

Las pruebas realizadas se efectuaron para buscar términos similares y diferentes:

- Similar a prisión: barbazul, cajita, pimpollos.
- 
- Similar a vaca: aquella, bla, ta, cubana, teatro, disturbios, solitaria, etc.
- 
- Similar a negrita: asustada, brillar, hasta, mí, bandas, etc.
- 
- Similar a valija: carcajada, mejoría, gil, novedosa, etc.
- 
- Diferente a bares: deténganme, safó, shangai, chivato, etc.

Por último se implementó con la herramienta TSNE la visualización del espacio de embedding, tal como se muestra en la figura. 

.. image:: ejercicio%203/tsne_plot.png

Ejercicio 4 (Predicción de próxima palabra)
===========================
Introducción
-----------------------------
El objetivo es utilizar documentos / corpus para crear embeddings de palabras basado en ese contexto utilizando la layer 
Embedding de Keras. Se aplican esos embeddings junto con layers LSTM para predecir la próxima posible palabra. El dataset utilizado es el 
mismo que en el ejercicio 3.

Implementación
-----------------------------
Se lee el archivo txt con el dataset y obtiene los tokens para luego armar las secuencias de largo 4 (incluyendo las 3 palabras de entrada y la palabra de salida).
Luego se crean los vectores de palabras para traducir las secuencias de texto a secuencias de números y se lleva a categorías a las palabras de salidas para poder entrenar el modelo. 

.. image:: ejercicio%204/model.png

El resulado del entrenamiento presenta un gran overfitting y los resultados reflejan esta falla.

.. image:: ejercicio%204/train.png"

A continuación se pueden ver algunos ejemplos probados, donde las 3 primeras palabras fueron ingresadas y la 4 es la predicha por el modelo:

-  dique que el nova

-  vamos negrita canta que

-  en este film muere

-  para alien duce voy

- susanita tan bonita sin

-  miraba el cielo lavi

-  banderas en tu vudú

-  dice que el maté

-  todo un palo 

-  el pibe de tu

-  ciertos reyes no reyes

-  el infierno esta caro

-  quemando la turbina roto

-  a brillar mi me


Ejercicio 5 (Análisis de sentimientos)
===========================
Introducción
-----------------------------

Este desafío consiste en aplicar Embedding y LSTM para clasificar críticas de compradores de ropa. Esta clasificación se realiza 
a partir del dataset Women's E-Commerce Clothing Reviews.
Se debe probar con y sin embeddings pre-entrenados.

Implementación
-----------------------------
Sólo vamos a trabajar con dos columnas del dataset: Review Text y Rating. Las puntuaciones (clases) están marcadamente desbalanceadas:
:: 
  Categoría / Cantidad
  4    12540
  3     4908
  2     2823
  1     1549
  0      821

Se aplica submuestreo para el balance de clases y se ajusta el máximo de cada categoría en 821 items.
Luego se avanza con la tokenización de las reseñas, se ajusta la cantidad de palabras máximas a utilizar en 2000 y se convierten en vectores numéricos.
En las sentencias con menos palabras que el límite máximo impuesto (115) se agrega un padding tipo pre.
El dataset es dividido en train y validation antes de iniciar los entrenamientos.


Entrenamiento con Embeddings + LSTM
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. image:: ejercicio%205/model1.png

El entrenamiento arroja un overfit grande, tal como se visualiza en la diferencia de las curvas de train y validation en esta figura.

.. image:: ejercicio%205/model1-train.png


Entrenamiento con Embeddings Fasttext + LSTM
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
En este caso se emplea un embeddings existente, limitándolo a la cantidad de las palabras definidas en el vocabulario. Por tal motivo se evita entrenar el Embeddings.
.. image:: ejercicio%205/model2.png

En este caso también se genera un gran overfit con un bajo accuracy.

.. image:: ejercicio%205/model2-train.png


Ejercicio 6 (Bot conversacional)
===========================
Introducción
-----------------------------
El objetivo de este ejercicio es utilizar datos disponibles del challenge ConvAI2 (Conversational Intelligence Challenge 2) de conversaciones en inglés. 
Con el mismo, se debe construir un BOT para responder a preguntas del usuario (QA).

Implementación
-----------------------------
En cada una de las entradas del dataset en formato JSON se disponen los campos:

- dialog

- start_time

- end_time

- bot_profile

- user_profile

- eval_score

- profile_match

- participant1_id

- participant2_id

Se lleva adelante el preprocesamiento de los datos como en los ejercicios previos y se incorpora el embeddings de Fasttext limitado a una dimensión de 300. Luego se entrenan el modelo encoder-decoder.

 .. image:: ejercicio%206/model1.png

El entrenamiento refleja un nivel aceptable con overfitting marcado. 

 .. image:: ejercicio%206/train.png

Las pruebas reflejan el error mencionado.

 .. image:: ejercicio%206/result.png
