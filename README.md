# Tp Descriptiva

**Proyecto:**

Realizar clusters de clientes para segmentar campañas de marketing

**Descripción del datasets:**

_Dataset_: Brazilian E-Commerce Public Dataset by Olist [https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce].

Contiene información de 100.000 órdenes realizadas en múltiples marketplaces de Brasil, entre 2016 y 2018. Es un modelo relacional que incluye tablas de órdenes, ítems, pago, clientes, vendedores, productos, reseñas y geolocalización.
Seleccionamos este dataset como preferido ya que el comercio electrónico es un tema de interés personal para todos los integrantes del grupo, incluye todo el ciclo de vida de una compra, es un caso real de ecommerce de Brasil. Cuenta con muchas tablas y muchos datos, y cuenta con información de clientes que es bueno para poder segmentar mediante técnicas de clustering y diseñar campañas de marketing.

Los archivos con la información son:
- _Olist_orders_dataset.csv_: cada fila es una orden. Las columnas que tiene son  order_id, customer_id, order_status, fechas/horas de compra, fecha/hora de aprobación, salida del carrier, entrega al cliente, entrega estimada (8 en total). Este archivo nos sirve para tiempos de entrega, puntualidad y retrasos, cancelaciones (acá habría que definir si el estado ‘unavailable’, además del de ‘canceled’, cuenta como cancelaciones), series temporales. Tiene datos nulos en: order_delivered_carrier_date (1783 en total) y order_delivered_customer_date (2965 en total).
- _Olist_order_items_dataset.csv_: cada fila es un producto dentro de una orden, si una misma orden trae tres productos, hay tres filas. Las columnas son order_id, order_item_id, product_id, seller_id, shipping_limit_date, price, freight_value (7 en total). No cuenta con datos nulos.  Nos sirve para calcular ventas por precio, flete por ítem, mezclar categorías y sellers.
- _Olist_order_payments_dataset.csv_: cada fila es un pago o un intento de pago de una orden. Tiene las columnas order_id, payment_type (credit_card, boleto, debit_card, etc), payment_installments (cantidad de cuotas), payment_value (monto) y payment_sequential (es el número de la transacción de pago dentro de una misma orden) (5 en total). Para definir el total pagado en una orden hay que considerar todas las diferentes transacciones, por ejemplo si se pagó una parte en tarjeta de crédito y otra en débito se deberían sumar (sería sumar payment_value por order_id). Sirve para evaluar métodos de pagos, cuotas, valor pagado, entre otros. Hay que tener en cuenta que algunas órdenes tienen más de un pago.
- _Olist_order_reviews_dataset.csv_: cada fila es una reseña que el cliente deja sobre su orden. Las columnas son review_id, order_id, review_score (un número de 1 al 5), review_comment_title (un asunto de la reseña), review_comment_message (el comentario del cliente), review creation date (fecha y hora de creación de la reseña), review_answer_timestamp (fecha y hora en la que la reseña fue respondida) (7 en total). Tiene datos nulos en review_comment_title (87656 en total) y en review_comment_message (58247 en total). Sirve para analizar la satisfacción del cliente, analizar el texto, analizar el tiempo de respuesta, las reseñas positivas y negativas, etc. Hay 547 órdenes con más de una reseña. El idioma es en portugués, esto requiere traducción.
- _Olist_customers_dataset.csv_: cada fila es un cliente al momento de la compra. Las columnas son customer_id (es la unión con órdenes), customer_unique_id (identifica a la misma persona a lo largo del tiempo, hay 96096 unicos, menos que la cantidad total de filas), customer_zip_code_prefix, customer_city, cistomer_state (5 en total). No tiene datos nulos. Sirve para observar re compras por persona y realizar un análisis por ciudad estado. Hay problemas con la base de geolocalización ya que un mismo prefijo tiene distintas latitudes y longitudes, esto se explica más abajo. También se puede analizar segmentaciones de clientes. Una persona puede tener varios customer_id.
- _Olist_sellers_Dataset.csv_: cada fila es un vendedor. Las columnas son seller_id, seller_zip_code_prefix, seller_city, seller_state (4 en total). Sirve para analizar por origen del envío, desempeño por vendedor/región. No hay datos nulos.
- _Olist_products_dataset.csv_: cada fila es un producto del catálogo. Las columnas son product_id, product_Category_name (En portugués), product_name_lenght, product_description_lenght, product_photos_qty, product_weight_g, product_lenght_cm, product_height_cm, product_width_cm (9 en total). Tiene 610 datos nulos en product_category_name, product_name_lenght, product_description_lenght y product_photos_qty. Sirve para categorías y atributos físicos.
- _Olist_geolocation_dataset.csv_: cada fila es un punto geográfico asociado a un prefijo de código postal. Las columnas son geolocation_zip_code_prefix, geolocation_lat, geolocation_lng, geolocation_city, geolocation_state (5 en total). Sirve para analizar por zona pero no por dirección exacta. El archivo tiene aproximadamente 1 millón de filas pero el mismo prefijo aparece varias veces. Es por eso que consideramos que sería una buena alternativa compactarlo a una sola fila por prefijo y sacar un promedio de latitud y longitud y quedarse con la ciudad o estado más frecuente o la de ese promedio (esto se realizó con promedio pero no sabemos cuál sería la mejor opción). Creemos que es una mejor alternativa también para la unión ya que el archivo de los sellers y customers cuenta con el prefijo. No tiene datos nulos.
- _Product_category_name_translation.csv_: es la traducción de las categorías del producto. Las columnas son product_category_name y product_category_name_english (2 en total). Sirve para mostrar la categoría en inglés, pero la traducción no llega al 100% de los casos (de las 73 categorías de producto únicas, 71 cuentan con traducción).

**Aclaración**: todos los archivos se encuentran cargados en GITHUB salvo Olist_geolocation_dataset.csv. Todo se puede encontrar en el link de kaggle. Los archivos crudos están subidos en el repositorio de GitHub pero no realizamos la ingesta desde ahí ya que el archivo "Olist_geolocation_dataset.csv" era muy pesado y no permitía ser subido al repositorio.

_Ventajas_: reconstrucción del ciclo punta a punta de una compra (desde el pedido hasta la evaluación). Esto permite calcular tiempos de entrega, puntualidad,  retrasos.
Cuenta con muchas variables, como pagos (métodos, tipo, valor), descripciones de los productos, ubicación de origen (vendedor) y de destino (cliente), clientes, categorías, entre otras cosas. También tiene datos geográficos, lo que permite realizar un análisis regional.
Contiene un gran volumen de datos.
Se puede relacionar la demora con las reviews y cancelaciones.

_Limitaciones_: datos de un período limitado (2016-2018) y que puede no reflejar la actualidad.
Aunque existe un archivo de traduccion, al tener datos en portugues que no se encuentran en ese archivo, requiere traduccion adicional.
Hay campos con datos faltantes y eventos incompletos.
Es un modelo relacional que requiere realizar joins entre las diferentes tablas y verificar que no haya relaciones de muchos a muchos. No se puede unir todo en una misma base (se explica más abajo en el alcance).

**Contexto y situación de negocio**:

Olist es una plataforma brasileña de comercio electrónico que conecta a pequeñas y medianas empresas (PYMES) para publicar y vender sus productos con grandes mercados en línea como Mercado Libre y Amazon. La empresa simplifica el proceso de venta en línea, gestiona la logística y la publicidad para que el comerciante se concentre únicamente en su negocio.
Entre los actores de la plataforma se encuentran: Olist, los vendedores y los clientes finales. La principal problemática que queremos abordar es identificar y segmentar clientes mediante técnicas de clustering, con el fin de diseñar campañas de marketing más efectivas y personalizadas que aumenten la retención, la fidelización y el valor de vida del cliente. Como parte de este análisis, también buscamos considerar factores vinculados a la logística (tiempos de entrega, retrasos y costos de envío) y a la satisfacción del cliente (reviews), ya que influyen directamente en el comportamiento y la pertenencia a cada segmento de clientes.

**Preguntas clave**:

_Descriptivo_:
- ¿Qué perfiles de clientes se observan según frecuencia de compra, ticket promedio y categorías adquiridas?
- ¿Qué porcentaje de clientes realiza compras únicas vs. recurrentes?
- ¿Qué categorías de productos generan mayores ventas?
- ¿Qué categorías concentran la mayor cantidad de reseñas positivas y negativas (asumimos positivas >= 3 y negativas <= 2)?
- ¿En qué estados o ciudades se concentran más pedidos?
- ¿Cuál es el método de pago más utilizado?
- ¿Cuál es el tiempo promedio de entrega por estado, ciudad o categoría de producto?
- ¿Cómo evolucionan las órdenes por mes?

_Diagnóstico_:
- ¿Qué características diferencian a los clientes que recompran de los que solo compran una vez?
- ¿Qué factores (ticket promedio, categorías, método de pago, ubicación) influyen en la pertenencia a un cluster de clientes de alto valor?
- ¿Cómo se relacionan los segmentos de clientes con la satisfacción (reseñas)?
- ¿Qué factores explican los retrasos en la entrega de pedidos?
- ¿Cómo se relaciona la clasificación de reseñas con tiempos de entrega y precios de envío?
- ¿Existe relación entre el método de pago y la probabilidad de cancelación o retraso?

_Predictivo_:
- ¿Existen patrones de recurrencia entre los clientes?
- ¿Qué características comparten los clientes de mayor valor o frecuencia de compra?
- ¿Qué variables parecen influir más en las diferencias entre segmentos (categoría de producto, método de pago, geografía, tiempos de entrega)?


_Prescriptivo_:
- ¿Qué estrategia de marketing conviene aplicar en cada cluster de clientes (ej. descuentos, retención)?
- ¿Cómo diseñar campañas de fidelización diferenciadas según el segmento (ej. clientes frecuentes vs. esporádicos)?
- ¿Qué productos recomendarías a cada cluster para maximizar el ticket promedio y la recompra?
- ¿Qué regiones deberían priorizarse para mejorar la logística?
- ¿Qué categorías o métodos de pago conviene promocionar para maximizar ingresos?
- ¿Qué promesa de entrega conviene por estado/prefijo para mejorar la puntualidad?

**Hipótesis y alcance**:

- _Hipótesis 1_: Los clientes pueden agruparse en clusters diferenciados en función de variables como ticket promedio, frecuencia de compra, categorías preferidas y métodos de pago.
- _Hipótesis 2_: Los distintos clusters de clientes muestran comportamientos de compra y niveles de fidelidad diferentes, lo cual permite diseñar estrategias de marketing personalizadas (por ejemplo retención para clientes frecuentes, descuentos para clientes esporádicos).
- _Hipótesis 3_: A mayor demora de entrega, mayor probabilidad reviews <= 3 (impacto en satisfacción).
- _Hipótesis 4_: Un % de flete alto (sobre el precio) incrementa las cancelaciones y baja la satisfacción.

**Alcance**:

Se armaron dos bases unificando los pedidos con sus pagos, productos, reseñas, zonas del cliente y zonas del vendedor. En la primera base se conservarán todas las órdenes, y los pagos se agruparán por orden (sumando el monto, la cantidad de cuota y dejando el método más frecuente) con el objetivo de evitar multiplicar filas. Las reseñas serán una sobre cada orden, y la geolocalización se compactará a un registro por prefijo. Hay que tener en cuenta que, como se mencionó, usaremos dos fact tables para evitar duplicados. Una va a ser una fila por orden y otra va a ser una fila por pedido. Si metemos todo en una sola tabla, cada pedido que tenga varios ítems/pagos/reviews se multiplica (por ejemplo un pedido con 2 ítems x 3 pagos x 2 reviews da un total de 12 filas). Eso distorsiona totales (pagos, pedidos, reviews) y obliga a corregir todo el tiempo. Se verifica (en el colab mencionado anteriormente) que la tabla orders y fact_orders contienen misma cantidad de registros (99441). La tabla fact_order_items tiene más, pero es de esperar ya que una misma orden puede tener varios ítems, que igualmente coinciden con la cantidad de registros de la tabla items (112650).

Otras aclaraciones con respecto a las uniones de bases son que la compactación a un registro de la geolocalización genera que el análisis geográfico sea a nivel estado/ciudad/prefijo y no a ubicación exacta. En los clientes se identifica a la persona por customer_unique_id y no por customer_id. Los tiempos de entrega se calcularán con ordenes con fechas válidas.

Se analizarán las compras, recurrencia de clientes, tickets promedio, categorías, medios de pago, localización (prefijos únicamente) y estacionalidad (picos de la demanda en el tiempo). También, se puede observar que es lo que se asocia con demoras (por ejemplo la distancia, los tipos de productos, etc) y cómo eso impacta en las reseñas.

Por otro lado, se analiza una segmentación de clientes en base a todo lo mencionado anteriormente. Se aplicarán técnicas de clustering no supervisado para identificar perfiles de clientes. El estudio busca conectar la segmentación con acciones de marketing (campañas de fidelización, retención y recomendación de productos).

Queda por fuera del alcance el detalle las operaciones logísticas (tiempos de entrega, costos de envío), salvo como variables secundarias que puedan aportar información para segmentar clientes. También se evitan los análisis del texto de las reseñas (aunque pensamos en capas implementar procesamiento del lenguaje natural, lo vemos poco viable, más que nada por la ausencia de datos de la base en cuanto a los títulos o descripciones de las reseñas y por el idioma) y el cálculo de distancias exactas (se calcula todo a nivel de los prefijos). Tampoco se traducirán los comentarios en portugués ni las categorías que no cuenten con traducción en el archivo de traducción, estas últimas se englobarán en una sección de ‘sin traducción’.

**Objetivo principal**:

Identificar clusters de clientes en el e-commerce de Olist que permitan diseñar campañas de marketing segmentadas y personalizadas, con el fin de mejorar la retención, la fidelización y el valor de vida del cliente.

**Descripción de la estructura de carpetas del repo**: 
- _data/raw_: incluye 8 de los 9 archivos utilizados en crudo. El último se puede encontrar en el link de Kaggle. 
- _notebooks_: incluye un archivo con el ánalisis exploratorio inicial, ánalisis descriptivo y graficos. Se incluye un archivo para el analisis de Fact_orders y otro para el analisis de Fact_orders_items. 

**Primeros hallazgos del EDA (para ambas tablas)**

- _Preprocesamiento incial de los datos_:
  - **Tratamiento de outliers:** En el caso de la latitud y longitud, decidimos excluirlos del analisis de outliers porque son direcciones y consideramos que no debiamos incluirlos. Analizamos los outliers de las variables cuantitativas continuas, como no son normales, aplicamos boxcox y luego buscamos los outliers con z score y umbral de 4 desviaciones estandar sobre la media. Al haber algunas variables que tienen valores negativos, aplicamos Yeo Johnson en vez de Box Cox. Luego de imputar con la mediana, pudimos ver que la cantidad de outliers queda en 0 en casi todos los casos o se reduce.
  - **Tratamiento de nulos:** Pudimos ver que las columnas de titulo de la review y la review en si tienen muchos valores nulos, y como no vamos a hacer analisis de texto, decidimos eliminarlas. Por otro lado, en el resto de las columnas se rechaza que sean MCAR. Para analizar si eran MAR, realizamos una regresión. En la mayoría de los casos, no rechazamos H0 (todos los p valores eran grandes), por lo que no podíamos afirmar que los datos fueran MAR. Entonces, consideramos que al tratarse de una cantidad menor al 3% de faltantes por columna, aplicamos KNN en todos los casos porque no consideramos que el test fuera tan concluyente ya que la cantidad de datos faltantes era poca. En conclusión, aplicamos KNN en todos los casos, en las columnas no numéricas de texto imputamos con la moda y en las de fecha y hora rellenamos con NaT, que es un dato nulo pero de tiempo.
  - **Tratamiento de duplicados:** Solamente nos encontramos con duplicados en el archivo de geolocation, decidimos agrupar los prefijos (códigos postales) y realizar el promedio tanto de la latitud como de la longitud. Y en cuanto a la ciudad y al estado, utilizamos el más frecuente (la moda). Realizamos esto ya que un mismo prefijo aparecia muchas veces con diferentes latitudes y longitudes y eso nos dificultaba la union y la creacion de las fact tables.
 
- _Análisis exploratorio inicial con visualizaciones:_
  - Consideramos que las variables están altamente correlaciones si la correlación es 0.8 o 80%. No se ven variables altamente correlacionadas, entonces no eliminamos.
  
- _Gráficos básicos que permitan contrastar hipótesis de la entrega 1. Conclusiones preliminares:_
  - **Hipótesis 1:** Los clientes pueden agruparse en clusters diferenciados en función de variables como ticket promedio, frecuencia de compra, categorías preferidas y métodos de pago.
    **Conclusiones:** Podemos ver que ticket promedio y frecuencia no muestran que haya evidencia para decir que hay clusters separados. La mayoría de los consumidores tiene una frecuencia de compra muy baja (muchas veces por única vez) y la distribución del ticket promedio es asimétrica. Podemos ver que categoría preferida y método de pago no muestran que haya evidencia para decir que hay clusters separados. En conclusión, a simple vista no se ve una separación de grupos de clientes que permita realizar clustering, será analizado en profunidad más adelante.
  - **Hipótesis 2:** Los distintos clusters de clientes muestran comportamientos de compra y niveles de fidelidad diferentes, lo cual permite diseñar estrategias de marketing personalizadas (por ejemplo retención para clientes frecuentes, descuentos para clientes esporádicos).
    **Conclusiones:** Podemos ver que hay diferencias notables entre el ticket promedio mediano de consumidores que compraron una única vez y consumidores recurrentes. Podemos ver que los compradores recurrentes tienden a preferir ciertos tipos de pago más que los compradores únicos. Podemos ver que los compradores recurrentes están más concentrados en ciertas categorías de productos en comparación con los compradores únicos. En resumen, las métricas de comportamiento de compra (ticket promedio, categoría/método de pago preferido) difieren según un nivel de fidelidad simple (frecuencia de compra). Si bien estos gráficos no prueban definitivamente la hipótesis para todos los posibles clusters, demuestran que existen diferencias de comportamiento entre al menos dos grupos de clientes definidos por su frecuencia de compra.
  - **Hipótesis 3:** A mayor demora de entrega, mayor probabilidad reviews <= 3 (impacto en satisfacción).
    **Conclusiones:** Al observar este gráfico de caja, vemos la distribución de la demora de entrega para los pedidos que recibieron una puntuación de reseña baja (<= 3) en comparación con los que recibieron una puntuación de reseña alta (> 3). Compara la mediana de la demora de entrega (la línea dentro de la caja) entre los dos grupos. Compara el rango intercuartílico (RIC) (la caja en sí), que muestra la dispersión del 50% central de los datos. Observa los bigotes y los posibles puntos atípicos (outliers) para comprender el rango completo y los valores extremos de la demora de entrega en cada grupo. Basándote en estas comparaciones visuales, puedes evaluar si los pedidos con menor satisfacción tienden a tener mayores demoras de entrega en comparación con los pedidos con mayor satisfacción. Podemos concluir que parece haber una relación entre la demora de entrega y la satisfacción del cliente. La demora en satisfacción alta es menor en promedio que la de satisfacción baja.
  - **Hipótesis 4:** Un % de flete alto (sobre el precio) incrementa las cancelaciones y baja la satisfacción.
    **Conclusiones:** Podemos ver que hay diferencia entre el % de flete sobre el precio según el estado del envío, si fue enviado o cancelado. Podemos ver que no se pueden sacar conclusiones a la vista acerca de si un mayor % de flete alto baja la satisfacción. Hay muchos casos de reviews altas con % de flete altos. Las visualizaciones proporcionan cierta evidencia visual que apoya la idea de que un porcentaje de flete más alto podría estar relacionado con las cancelaciones. Sin embargo, la relación entre un porcentaje de flete alto y una menor satisfacción del cliente (basada en la puntuación de la reseña) no parece ser tan clara a simple vista y puede que no haya suficiente evidencia visual para respaldar esa parte de la hipótesis. Se necesitaría un análisis estadístico más riguroso para confirmar estas observaciones preliminares.


**Tests de hipótesis**
- _Hipotesis 1 redefinida_: los clientes pueden agruparse en diferentes clusters dependiendo de la recencia. En este caso, realizamos un test de comparacion de medias, y con un pvalue < 0,05 tanto en fact orders como en fact order items, rechazamos H0: las medias de ambos grupos son iguales, llegando a la conclusion de que, tanto en fact ordes como en fact order items se refleja una diferencia de medias.
  
- _Hipotesis 3_: realizamos un test de Mann-Whitney en fact orders en el que se rechaza H0: Las distribuciones de delivery days son iguales con reviews <= 3 y > 3 con un p value < 0,05. Esto nos permite concluir que las distribuciones difieren. Tambien, realizamos el test de Spearman en el que obtuvimos una asociacion negativa que es significativa. Esto ultimo nos permite concluir que a mas dias de entrega, menor review score. Mayores delivery days se asocian con peores satisfacciones.
  
- _Hipotesis 4_: realizamos un test de Mann-Whitney en fact orders items, en donde se rechaza con un p valor < 0,05 la H0: las distribuciones de % de flete son iguales en cancelados y no cancelados. Como rechazamos, llegamos a la conlcusion de que no son iguales. Por otro lado, para ver si existe la correlacion entre la review y el % de flete hicimos un test de Spearman que nos indico que existe una asociacion negativa pero es muy debil. Tambien, realizamos el test de Kruskal-Walls y concluimos que hay diferencia entre los grupos y mas % de flete se asocia a una ligera reduccion de puntaje promedio. 

**Preprocesamiento de datos para ML**

- _Correlación:_ No hay variables altamente correlacionadas (consideramos correlación mayor a 0.8) en ninguno de los dos casos es por eso que decidimos mantener todas.

-  _Encoding de variables categóricas (one-hot, ordinal encoding, etc.) con justificación:_
   - **Fact_orders:** De las variables categoricas vamos a analizar solamente las que tengan menos de 15 categorias. En este caso, serian 'order_status' y 'payment_type_mode'. Aplicamos one hot encoder sobre dichas variables ya que estas variables son nominales. Esto significa que no hay un orden o jerarquía inherente entre las categorías (por ejemplo, 'tarjeta de crédito' no es 'mayor que' o 'menor que' 'boleto'). La codificación one-hot crea una nueva columna binaria para cada categoría única, evitando que el modelo asuma una relación ordinal que no existe.
   - **Fact_orders_items:** En este caso, la única variable categorica con menos de 15 categorias es 'payment_type_mode'. Tambien decidimos aplicar one hot encoder por la misma razon que arriba. 
  
-  _Estandarización:_
   - **Fact_orders:** En este caso, aplicamos Z Score ya que transforma los datos para que tengan media 0 y desvio 1. Sirve cuando los datos no tienen un rango fijo.
   - **Fact_orders_items:** En este caso, tambien decidimos que lo mejor era aplicar Z Score. 

-  _PCA:_
    - **Fact_orders:** Realizamos PCA luego de estandarizar e imputar los nulos previamente y llegamos a la conclusión que, por el criterio de Kaiser, se deberian mantener los primeros 12 componentes ya que sus autovalores son > 1.
    - **Fact_orders_items:** Basandonos en el criterio de Kaiser, en este caso se deben mantener los primeros 6 componentes principales.
    
-  _Clustering_:
    - **Fact_orders**: Intentamos realizar clusters a partir de lo observado pero en un principio llegamos a la conclusión de que no se podia determinar una cantidad optima ya que los metodos del codo, la silueta y VRC no coincian entre si. Es por eso que realizamos RFM en el dataset original, primero dividiendo el dataset segun variables de recencia, frecuencia y monetización. Luego, realizamos el escalado de datos, aplicamos PCA en las columnas RFM llegando a que se necesitaban COMPLETAR componentes unicos. Despues, realizando el grafico del codo y de la silueta, a pesar de que no nos coincidian esos criterios, logramos detectar dos clusters distinguibles. En estos casos, logramos ver a simple vista que la media de recencia en ambos clusters difiere. Realizando el test de hipotesis, rechazamos que ambas medias sean iguales. Llegamos a la conclusion de que esto puede servir para realizar campañas de marketing dirigidas a la reactivación de clientes que hace mucho no compran. No logramos sacar conclusiones de las hipotesis planteadas en un principio (la 1 y 2) pero si descubrimos que la recencia es un factor que hay que considerar.
    - **Fact_order_items**: En este caso, tampoco logramos determinar clusters a partir de lo observado en un principio en los graficos del codo y de la silueta. Es por eso que decidimos aplicar, al igual que en la otra tabla, RFM. Llegamos a la misma conclusion, la recencia es un factor clave a la hora de determinar clusters. Tambien determinamos que 2 clusters era la decision mas adecuada. Las medias de los diferentes grupos en esta variable tambien difieren.  

**Dashboard**: realizamos un tablero que cuenta con 5 paginas. Cada una de estas paginas se asocia a un tema particular. La primera es acerca de los compradores, sus estados y sus ciudades. La segunda es sobre los vendedores, sus estados y sus ciudades. La tercera habla sobre los tiempos de entrega. La cuarta sobre los productos. La quinta y ultima sobre los diferentes medios de pagos. Se realizan graficos que permiten sacar conclusiones y se utilizan filtros dinamicos para una mejor exploracion. 

