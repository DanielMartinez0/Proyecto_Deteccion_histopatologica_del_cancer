## **PROYECTO: DETECCIÓN HISTOPATOLÓGICA DEL CANCER**
### Fundamentos de Deep Learning - 2023-2
*Daniel H. Martínez S. - UdeA* \
dhumberto.martinez@udea.edu.co 

### CONTEXTO

La histopatología es el estudio y diagnóstico de una enfermedad mediante el examen microscópico de una biopsia que se procesa y se fija en un portaobjetos de vidrio. Para visualizar diferentes componentes del tejido, las secciones se tiñen con tintes con el objetivo de revelar los componentes celulares. Los patólogos han utilizado la tinción con hematoxilina-eosina (H&E) durante más de 100 años [1-3]. 

![image](https://github.com/DanielMartinez0/Proyecto_Deteccion_histopatologica_del_cancer/assets/79658767/f47192f2-6201-4daf-994f-1b81d013ee7c)

###### Figura 1. Imagenes de histopatología digitalizada, a) muestras pertenecientes a la clase 1 (con cáncer en la región central), b) muestras pertenecientes a la clase 0 (sin cáncer en la región central), c) selección de la región central para la clase 1 y d) *white-padding* para la región central de la clase 0. 

### OBJETIVO
Para este proyecto se ha seleccionado una competencia de [Kaggle](https://www.kaggle.com/competitions/histopathologic-cancer-detection/overview "Histopathologic Cancer Detection") sobre detección histopatológica del cáncer, la cual estuvo activa desde el 16/10/2018 hasta el 30/03/2019. En este proyecto se debe crear un algoritmo para identificar el cáncer de tejido metastásico en pequeños recortes de imágenes tomadas de exploraciones patológicas digitales más grandes. Los datos son una versión de los datos de referencia de PacthCamelion ([PCam](https://github.com/basveeling/pcam#readme "PatchCamelyon (PCam)"))  que a su vez son derivados de los datos de [CAMELYON16](https://camelyon16.grand-challenge.org/ "CAMELYON16 challenge") [4-7]. 

>El objetivo del proyecto es predecir la probabilidad de que en la región central $(32\times32\ pix)$ de la imagen, contenga al menos un pixel de tejido tumoral. Para estudiar este problema de clasificación, se seleccionaron de forma aleatoria 5400 imágenes las cuales estan divididas en archivos <25M en la carpeta [data](https://github.com/DanielMartinez0/Proyecto_Deteccion_histopatologica_del_cancer/tree/main/data "datos de entrenamiento").  

### MÉTRICA
En este proyecto la métrica de validación utilizada fue el **AUC** (*Area under the receiver operating characteristic curve*) entre la probabilidad predicha y el objeto observado. 
>*Esta métrica es igual a la probabilidad de que un clasificador clasifique una muestra aleatoria positiva más alta que una muestra aleatoria negativa.*

Para entender esto es importante saber que los **falsos negativos** (muestras con cáncer detectadas como negativas) y **los falsos positivos** (muestras sin cáncer detectadas como positivas), son muestras que se clasificaron **incorrectamente**. Por el contrario, los **verdaderos negativos** (muestras sin cáncer detectadas como negativas) y los **verdaderos positivos** (muestras con cáncer detectadas como positivas) son muestras que se clasificaron correctamente. Esta información se cuantifica en la **matriz de confusión**.

### NOTEBOOKS
•	[01– Exploración de datos](note_books/01_Exploración_de_datos.ipynb "01– Exploración de datos.ipynb"):  Se estudia el número de imágenes y clases, luego se selecciona una muestra de 5400 imágenes localizada en la carpeta [data](data " datos de entrenamiento - 5400 imágenes") con el fin de facilitar su análisis y posterior entrenamiento de diferentes modelos.

•	[02 – Estudio de la métrica AUC](note_books/02_Estudio_Métrica_AUC.ipynb "02 – Estudio de la métrica AUC.ipynb"): Se separan de los datos `train`, `val` y `test`. Se crea un modelo de prueba para evaluar el comportamiento de diferentes métricas de validación para un clasificador biclase como el AUC, el Recall, la matriz de confusión, entre otros.

•	[03 – Aplicación de modelos de CNN](note_books/03_Aplicación_modelos_CNN.ipynb "03 – Aplicación de modelos de CNN.ipynb"): Se prueban diferentes arquitecturas de Redes Neuronales Convolucionales (CNN), variando tanto el número como el tipo capas. Se evalúan 6 modelos (A, B, C, D, E y F) en función de las métricas de validación (en los moedelos C, D, E y F se aplica *transfer learning*).

•	[04 – Modelos con datos aumentados](note_books/04_Modelos_&_Datos_aumentados_CNN.ipynb "04 – Modelos con datos aumentados.ipynb"): Con el objetivo de intentar disminuir el overfiting encontrado en los resultados del `notebook 03`, se prueban los 6 modelos en un nuevo dataset con datos aumentados. Para lograr esto, se utilizan las imágenes iniciales y se les agrega un *white-padding (wp)* en la región central a las muestras que no tienen células cancerígenas (`clase 0`), mientras que a las muestras que tienen cáncer (`clase 1`), se hace el *wp* en la región por fuera del centro. Con estas nuevas imágenes se aumentan los datos del entrenamiento.

• [05 – Modelos con datos aumentados - 02](note_books/05_Modelos_&_Datos_aumentados_02_CNN.ipynb "05 – Modelos con datos aumentados - 02.ipynb"): Con el objetivo de intentar disminuir el *overfiting* se realiza un procedimiento similar al `notebook 04`, pero aumentando los datos de entrenamiento solo realizando el *wp* a las muestras que no tienen células cancerígenas (`clase 0`).


### RESULTADOS
En general los modelos presentan overfiting. De acuerdo a las ideas expresadas en los `notebooks 04 `y `05`, se aumentó el tamaño de los datos de entrenamiento. Las iteraciones anteriores arrojaron los siguientes resultados respecto a la métrica AUC:

<!-- TABLE -->
#### Tabla 1. Resultados del AUC
| Modelos | $AUC^1$ | $AUC^2$ | $AUC^3$ |
|-----|------|-------|---------|
|  A  |   0.650  |    0.719   |    0.680     |
|  B  |   0.717  |   0.784   |     0.758     |
|  C  |   0.868  |    0.891   |    0.830      |
|  D  |   0.902  |   0.732    |    0.863      |
|  E  |   **0.937**  |   0.913    | 0.914         |
|  F  |   0.905  |   0.884    |      0.912    |

Donde $AUC^1$ corresponde a la métrica de los datos originales (**resultados del notebook 03**), $AUC^2$ a los datos aumentados con *wp* tanto en la región central de la clase 0 como en la región por fuera del centro en la clase 1 (**resultados del notebook 04**), y $AUC^3$ corresponde a los datos aumentados solo con *wp* para la región central de la clase 0 (**resultados del notebook 05**).

Estos resultados nos muestras que con la estrategia de *wp* se mejora el *score* solo en los modelos A, B, C para $AUC^2$ y el modelo F para $AUC^3$ (respecto a $AUC^1$ y $AUC^2$ simultáneamente). Sin embargo, estos resultados no superan el *score* del modelo E, el cual obtuvo el mejor desempeño de acuerdo a la métrica $\mathbf{AUC^1=0.937}$. Los resultados de las iteraciones de este modelo en 50 épocas y tamaño de batch de 32 se muestran en la Figura 2 y 3.

![image](https://github.com/DanielMartinez0/Proyecto_Deteccion_histopatologica_del_cancer/assets/79658767/89c31cda-61b0-4339-b4bc-8f13a130ad3b)

###### Figura 3. Comportamiento de diferentes métricas en función de las épocas del Modelo-E ( $AUC^1$ ).

![image](https://github.com/DanielMartinez0/Proyecto_Deteccion_histopatologica_del_cancer/assets/79658767/983f3888-08a2-42a9-9ec8-4e93795e649b)

###### Figura 2. Matriz de confusión y curva ROC del Modelo-E ( $AUC^1$ ).
Algunas ideas para continuar mejorando el *score* podrían ser aumento de color, normalización de la tinción o alineación de dominios con GAN (*Generative Adversarial Networks*)  [8].

### VIDEO 

### CONCLUSIONES
- Se obtuvo un AUC de 0.937 con el Modelo-E correspondiente a la implementación de *transfer learning* del modelo *Xception*, superando una implementación análoga con los modelos *Resnet50* e I*nceptionV3*.
- La metodología implementada de aumento de datos, tuvo mejor desempeño en la variante de *White-padding* a ambas clases, pero no superaron el *score* del modelo-E utilizando los datos originales.


### BIBLIOGRAFÍA
[1]	M. N. Gurcan, L. E. Boucheron, A. Can, A. Madabhushi, N. M. Rajpoot, and B. Yener, “Histopathological Image Analysis: A Review,” IEEE Rev Biomed Eng, vol. 2, pp. 147–171, 2009, doi: 10.1109/RBME.2009.2034865.

[2]	American Cancer Society, “Lymph Nodes and Cancer.” Accessed: Sep. 27, 2023. [Online]. Available: https://www.cancer.org/content/dam/CRC/PDF/Public/7902.00.pdf

[3]	H. West and J. Jin, “Lymph Nodes and Lymphadenopathy in Cancer,” JAMA Oncol, vol. 2, no. 7, p. 971, Jul. 2016, doi: 10.1001/jamaoncol.2015.3509.

[4]	B. E. Bejnordi et al., “Stain Specific Standardization of Whole-Slide Histopathological Images,” IEEE Trans Med Imaging, vol. 35, no. 2, pp. 404–415, Feb. 2016, doi: 10.1109/TMI.2015.2476509.

[5]	B. S. Veeling, J. Linmans, J. Winkens, T. Cohen, and M. Welling, “Rotation Equivariant CNNs for Digital Pathology,” Jun. 2018.

[6]	C. Szegedy et al., “Going Deeper with Convolutions,” Sep. 2014.

[7]	W. Cukierski, “Histopathologic Cancer Detection.” Kaggle, 2018. [Online]. Available: https://kaggle.com/competitions/histopathologic-cancer-detection

[8]       Stacke, K., Eilertsen, G., Unger, J., & Lundström, C. (2021). "Measuring Domain Shift for Deep Learning in Histopathology". IEEE Journal of Biomedical and Health Informatics, 25(2), 325–336. https://doi.org/10.1109/JBHI.2020.3032060
