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

**Aclaración**: todos los archivos se encuentran cargados en GITHUB salvo Olist_geolocation_dataset.csv. Todo se puede encontrar en el link de kaggle. 

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
- _notebooks_: incluye un archivo con el ánalisis exploratorio inicial, ánalisis descriptivo y graficos.
