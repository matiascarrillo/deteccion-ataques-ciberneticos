Detección de ataques cibernéticos con Minería de Datos

Análisis exploratorio y clasificación de tráfico de red con Python, utilizando el conjunto de datos UNSW-NB15. El proyecto estudia la calidad de los datos, identifica patrones asociados a conexiones maliciosas y evalúa un árbol de decisión para distinguir tráfico normal de ataques.

Objetivo

Explorar registros de tráfico de red y construir un modelo supervisado de clasificación binaria (label: 0 = tráfico normal, 1 = ataque). Se busca estudiar tanto la capacidad de detección como el costo potencial de falsas alarmas y ataques no detectados.

Datos

El notebook trabaja con 175.341 registros y 45 columnas del conjunto de entrenamiento UNSW-NB15. Entre las variables analizadas se encuentran duración de la conexión (dur), protocolo (proto), servicio (service), paquetes (spkts, dpkts), bytes transmitidos (sbytes, dbytes), categoría de ataque (attack_cat) y etiqueta binaria (label).

Distribución de etiquetas:

Clase	Registros
Tráfico normal (0)	56.000
Ataques (1)	119.341


Importante: las clases no están balanceadas: predominan los registros maliciosos.

Metodología

1. Exploración inicial: revisión de dimensiones, tipos de datos, valores nulos, duplicados y distribución de clases y categorías de ataques.
2. Control de calidad: se detectaron 67.601 registros idénticos al excluir id; se conservaron porque no se pudo establecer que fueran errores. Se examinaron los servicios indicados como - y 2.657 conexiones con duración cero, que también se conservaron.
3. Análisis de valores atípicos: mediante el rango intercuartílico (IQR), se identificaron 22.873 registros por encima del límite superior de 3.374 bytes para sbytes. De ellos, 14.003 correspondían a tráfico normal y 8.870 a ataques. Se conservaron: un valor atípico no implica necesariamente actividad maliciosa.
4. Preparación del modelo: X excluye id, attack_cat y label; y es label. Se empleó GroupShuffleSplit para mantener observaciones con las mismas características en un solo subconjunto, y pd.get_dummies() para codificar proto, service y state.
5. Entrenamiento y evaluación: árbol de decisión (DecisionTreeClassifier) con max_depth=5, evaluado mediante matriz de confusión, accuracy, precision, recall y F1-score.
   
Resultados

El conjunto de prueba contiene 35.196 registros.
Métrica	Resultado en prueba
Accuracy	93,65 %
Precision	91,99 %
Recall	99,36 %
F1-score	95,53 %


Matriz de confusión (filas = clase real; columnas = clase predicha):
	Predicho normal	Predicho ataque
Real normal	9.041	2.082
Real ataque	154	23.919


El modelo detectó 23.919 ataques en el conjunto de prueba y dejó pasar 154, pero produjo 2.082 falsas alarmas. La accuracy fue 93,50 % en entrenamiento y 93,65 % en prueba; esta comparación no muestra una brecha importante, aunque no descarta otras formas de sobreajuste o cambios de distribución.

Conclusiones y limitaciones

El árbol de decisión obtuvo un recall alto en la evaluación realizada, una característica relevante cuando interesa reducir los ataques no detectados. Sin embargo, la cantidad de falsos positivos podría generar una carga operativa significativa. Estos resultados no justifican utilizar el modelo como mecanismo autónomo de defensa en producción.

La evaluación corresponde a una partición del conjunto de datos del notebook. Para estudiar su generalización sería conveniente evaluar con datos independientes, revisar posibles cambios temporales y analizar el costo de falsos positivos y falsos negativos. El notebook no implementa aislamiento automático de equipos ni cambios de reglas de firewall.
Herramientas
- Python
- Pandas y NumPy
- Plotly Express
- Scikit-learn
- Google Colab
Notebook

[Ver análisis y modelo en Google Colab / GitHub](MDA_P2_Carrillo_Matias.ipynb)

Reproducción

El notebook original carga los datos desde una ruta de Google Drive en Colab. Para ejecutarlo en otra cuenta es necesario disponer del archivo de entrenamiento UNSW-NB15 y adaptar la ruta de lectura a su ubicación. El archivo CSV no se incluye en este repositorio.
Origen de los datos
UNSW-NB15, desarrollado en la Universidad de Nueva Gales del Sur (UNSW), Australia. Consultar las condiciones de uso y la documentación del dataset antes de redistribuir sus archivos.


Alcance
Proyecto académico de Minería de Datos, presentado como demostración de exploración, preparación de datos, clasificación supervisada e interpretación de métricas. Los resultados corresponden a la ejecución documentada en el notebook.
