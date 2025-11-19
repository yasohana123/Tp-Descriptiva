# Analisis de Olist - Segmentación de clientes y campañas de marketing
## 📌 Contexto
Olist es una plataforma de Brasil de e-commerce que conecta PYMES con grandes marketplaces.

Publica los productos de los vendedores, gestiona pagos, logística y se ocupa de gestionar parte de la experiencia del cliente final.

## 🗃️ Dataset
- **Fuente**: [Brazilian E-Commerce Public Dataset by Olist](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce)

- **Registros**: 100.000

- **Archivos clave**:
  - _Olist_orders_dataset.csv_: cada fila es una orden.
  - _Olist_order_items_dataset.csv_: cada fila es un producto dentro de una orden.
  - _Olist_order_payments_dataset.csv_: cada fila es un pago o un intento de pago de una orden.
  - _Olist_order_reviews_dataset.csv_: cada fila es una reseña que el cliente deja sobre su orden.
  - _Olist_customers_dataset.csv_: cada fila es un cliente al momento de la compra.
  - _Olist_products_dataset.csv_: cada fila es un producto del catálogo.
  - _Olist_geolocation_dataset.csv_: cada fila es un punto geográfico asociado a un prefijo de código postal.
  - _Product_category_name_translation.csv_: es la traducción de las categorías del producto.
  
- **Tablas con las que trabajamos**: Para trabajar, generamos dos fact tables: la primera, llamada “fact_table_orders” contiene los registros a nivel de cada orden. La segunda, llamada “fact_table_order_items” detalla las órdenes a nivel de los ítems que la componen.
 
## 🎯 Objetivo 

Identificar clusters de clientes en el e-commerce de Olist que permitan diseñar campañas de marketing segmentadas y personalizadas, con el fin de mejorar la retención, la fidelización y el valor de vida del cliente.

## ⚠️ Problema abordado

Necesitamos comprender perfiles de clientes, entender los drivers de satisfacción y explicar el rol de la logística para identificar segmentos de valor y personalizar acciones que mejoren la retención.

## ⚙️ Preprocesamiento, hipótesis y resultados estadísticos

### **Preprocesamiento**

- _Tratamiento de outliers_: En el caso de la latitud y longitud, decidimos excluirlos del analisis de outliers porque son direcciones. Analizamos los outliers de las variables cuantitativas continuas, como no son normales, aplicamos boxcox y luego buscamos los outliers con z score y umbral de 4 desviaciones estandar sobre la media. Al haber algunas variables que tienen valores negativos, aplicamos Yeo Johnson en vez de Box Cox. Luego de imputar con la mediana, pudimos ver que la cantidad de outliers queda en 0 en casi todos los casos o se reduce.

- _Tratamiento de nulos_: Pudimos ver que las columnas de titulo de la review y la review en si tienen muchos valores nulos, y como no vamos a hacer analisis de texto, decidimos eliminarlas. Por otro lado, en el resto de las columnas se rechaza que sean MCAR. Para analizar si eran MAR, realizamos una regresión. En la mayoría de los casos, no rechazamos H0 (todos los p valores eran grandes), por lo que no podíamos afirmar que los datos fueran MAR. Entonces, consideramos que al tratarse de una cantidad menor al 3% de faltantes por columna, aplicamos KNN en todos los casos porque no consideramos que el test fuera tan concluyente ya que la cantidad de datos faltantes era poca. En conclusión, aplicamos KNN en todos los casos, en las columnas no numéricas de texto imputamos con la moda y en las de fecha y hora rellenamos con NaT, que es un dato nulo pero de tiempo.

- _Tratamiento de duplicados_: Solamente nos encontramos con duplicados en el archivo de geolocation, decidimos agrupar los prefijos (códigos postales) y realizar el promedio tanto de la latitud como de la longitud. Y en cuanto a la ciudad y al estado, utilizamos el más frecuente (la moda). Realizamos esto ya que un mismo prefijo aparecia muchas veces con diferentes latitudes y longitudes y eso nos dificultaba la union y la creacion de las fact tables.


### **Tests de hipótesis**
- _Hipotesis 1_: los clientes pueden agruparse en diferentes clusters dependiendo de la recencia. En este caso, realizamos un test de comparacion de medias, y con un pvalue < 0,05 tanto en fact orders como en fact order items, rechazamos H0: las medias de ambos grupos son iguales, llegando a la conclusion de que, tanto en fact ordes como en fact order items se refleja una diferencia de medias.
  
- _Hipotesis 2_: realizamos un test de Mann-Whitney en fact orders en el que se rechaza H0: Las distribuciones de delivery days son iguales con reviews <= 3 y > 3 con un p value < 0,05. Esto nos permite concluir que las distribuciones difieren. Tambien, realizamos el test de Spearman en el que obtuvimos una asociacion negativa que es significativa. Esto ultimo nos permite concluir que a mas dias de entrega, menor review score. Mayores delivery days se asocian con peores satisfacciones.
  
- _Hipotesis 3_: realizamos un test de Mann-Whitney en fact orders items, en donde se rechaza con un p valor < 0,05 la H0: las distribuciones de % de flete son iguales en cancelados y no cancelados. Como rechazamos, llegamos a la conlcusion de que no son iguales. Por otro lado, para ver si existe la correlacion entre la review y el % de flete hicimos un test de Spearman que nos indico que existe una asociacion negativa pero es muy debil. Tambien, realizamos el test de Kruskal-Walls y concluimos que hay diferencia entre los grupos y mas % de flete se asocia a una ligera reduccion de puntaje promedio. 

### **Preprocesamiento de datos para ML**

- _Correlación:_ No hay variables altamente correlacionadas (consideramos correlación mayor a 0.8) en ninguno de los dos casos es por eso que decidimos mantener todas.

-  _Encoding de variables categóricas:_ Se analizaron solamente las variables categoricas que tengan menos de 15 categorias. Aplicamos one hot encoder sobre esas variables ya que eran variables nominales. Esto significa que no hay un orden o jerarquía inherente entre las categorías. La codificación one-hot crea una nueva columna binaria para cada categoría única, evitando que el modelo asuma una relación ordinal que no existe.
  
-  _Estandarización:_ Se aplica Z Score ya que transforma los datos para que tengan media 0 y desvio 1. Sirve cuando los datos no tienen un rango fijo.

-  _Clustering_: Intentamos realizar clusters a partir de lo observado pero en un principio llegamos a la conclusión de que no se podia determinar una cantidad optima ya que los metodos del codo, la silueta y VRC no coincian entre si. Es por eso que realizamos RFM en el dataset original, primero dividiendo el dataset segun variables de recencia, frecuencia y monetización. Luego, realizamos el escalado de datos, aplicamos PCA en las columnas RFM llegando a que se necesitaban 12 componentes unicos. Despues, realizando el grafico del codo y de la silueta, a pesar de que no nos coincidian esos criterios, logramos detectar dos clusters distinguibles. En estos casos, logramos ver a simple vista que la media de recencia en ambos clusters difiere. Realizando el test de hipotesis, rechazamos que ambas medias sean iguales. Llegamos a la conclusion de que esto puede servir para realizar campañas de marketing dirigidas a la reactivación de clientes que hace mucho no compran. No logramos sacar conclusiones de las hipotesis planteadas en un principio (la 1 y 2) pero si descubrimos que la recencia es un factor que hay que considerar.

## 🔍 Insights principales

- El comportamiento inicial muestra baja recurrencia y fuerte sensibilidad a la logística.

- La logística (puntualidad y costo de envío) es el principal driver tanto de satisfacción como de cancelación.

- La recencia es el factor más predictivo del comportamiento futuro.

- La segmentación RFM transforma el volumen de datos en decisiones accionables para marketing, retención y logística

- La puntualidad y el costo del envío son los principales impulsores de satisfacción: mejorar logística mejora el negocio.

## 🤖 Modelos Implementados

En este proyecto utilizamos un modelo de segmentación RFM para clasificar a los clientes según su comportamiento temporal de compra.
RFM es un método clásico de análisis de clientes que se basa en tres dimensiones:

1. Recency (R) – Recencia

Mide cuántos días pasaron desde la última compra del cliente.
Cuanto menor es la recencia, más activo se considera el cliente.
En nuestro dataset, la recencia resultó ser la variable más importante para distinguir el comportamiento de los usuarios.

2. Frequency (F) – Frecuencia

Cuenta la cantidad total de compras que un cliente realizó.
En Olist observamos que la mayoría compra solo una vez, por lo que esta variable mostró baja capacidad de segmentación.

3. Monetary (M) – Monetización

Calcula el gasto total del cliente (suma de todos los pedidos).
En nuestro caso, también mostró poca dispersión y no generó grupos diferenciables.

## ✅ Conclusiones y recomendaciones de negocio


### Desarrollamos un modelo de clustering basado en RFM (Recencia, Frecuencia y Monetización) que permite:

- Identificar dos segmentos clave: clientes activos y clientes inactivos.
- Anticipar el riesgo de abandono mediante la recencia.
- Personalizar acciones de marketing según el comportamiento del cliente.
- Optimizar decisiones logísticas asignando esfuerzos a las zonas y segmentos más críticos.
- Pasar de campañas masivas a estrategias basadas en datos.

### Impacto en el negocio:

_Incrementar el ingreso por cliente_
- Implementar programas de fidelización para los clientes activos.
- Aplicar cross-sell y up-sell basados en historial real de compras.

_Tomar decisiones operativas basadas en datos:_
- Identificar zonas críticas para mejorar tiempos de entrega.
- Ajustar inventario y proveedores según el análisis de demoras.

_Reducir cancelaciones mejorando logística:_
- Ajustar la promesa de entrega según región.
- Invertir en rutas/transportistas más eficientes en los estados con peores tiempos.
- Comunicar mejor el estado del envío.

_Aumentar la retención con campañas inteligentes:_
- Activar campañas automáticas según recencia (30/60/120 días).
- Recomendar productos de entrega rápida a clientes inactivos para mejorar su “primer regreso”.

Este proyecto convierte el histórico de datos de Olist en una herramienta concreta para reducir cancelaciones, retener clientes y mejorar ingresos, alineando decisiones de marketing y logística con el comportamiento real de los usuarios.

## ❕ Limitaciones 

Queda por fuera del alcance el detalle las operaciones logísticas (tiempos de entrega, costos de envío), salvo como variables secundarias que puedan aportar información para segmentar clientes. También se evitan los análisis del texto de las reseñas (aunque pensamos en capaz implementar procesamiento del lenguaje natural, lo vemos poco viable, más que nada por la ausencia de datos de la base en cuanto a los títulos o descripciones de las reseñas y por el idioma) y el cálculo de distancias exactas (se calcula todo a nivel de los prefijos). Tampoco se traducirán los comentarios en portugués ni las categorías que no cuenten con traducción en el archivo de traducción, estas últimas se englobarán en una sección de ‘sin traducción’.
