# Spark SQL · Cuaderno de referencia

Guía práctica en español para aprender y recordar los comandos más habituales de **Spark SQL**, con ejemplos de una tienda: clientes, productos, pedidos y líneas de pedido.

**Autora:** Jennifer Sánchez Richart  
**Contexto de aprendizaje:** Microsoft Fabric y análisis de datos.

## Notebook

Abre [Spark_SQL_Referencia.ipynb](Spark_SQL_Referencia.ipynb) para consultar las explicaciones y los ejemplos.

GitHub permite visualizar el notebook. Para ejecutar sus celdas necesitas una sesión Spark, por ejemplo en Microsoft Fabric o en un entorno local con PySpark.

## Contenido

- Carga de CSV con esquemas y tipos de datos explícitos.
- Exploración: `SHOW TABLES` y `DESCRIBE TABLE`.
- Consultas: `SELECT`, alias, `DISTINCT`, `ORDER BY` y `LIMIT`.
- Filtros: `WHERE`, `AND`, `OR`, `IN`, `BETWEEN` y `LIKE`.
- Transformaciones: `CAST`, `COALESCE`, `CASE WHEN`, texto y fechas.
- Agregaciones: `COUNT`, `SUM`, `AVG`, `MIN`, `MAX`, `GROUP BY` y `HAVING`.
- Relaciones: `INNER JOIN`, `LEFT JOIN` y `LEFT ANTI JOIN`.
- Combinación de resultados con `UNION ALL`.
- CTE con `WITH` y ventanas con `ROW_NUMBER`, `LAG` y `SUM OVER`.
- Controles de duplicados, referencias e importes.
- Prácticas opcionales de Delta: creación, escritura, historial, time travel y clonación.
- Diferencias entre Spark SQL y el endpoint SQL del Lakehouse.
- Ejercicios y una tabla de consulta rápida.

## Archivos del repositorio

Guarda los siguientes elementos en la raíz del repositorio, manteniendo los CSV dentro de `data/`:

| Ruta | Descripción |
|---|---|
| [Spark_SQL_Referencia.ipynb](Spark_SQL_Referencia.ipynb) | Notebook principal |
| [requirements.txt](requirements.txt) | Dependencias para el entorno local |
| [data/clientes.csv](data/clientes.csv) | Clientes |
| [data/productos.csv](data/productos.csv) | Catálogo de productos |
| [data/pedidos.csv](data/pedidos.csv) | Cabeceras de pedidos |
| [data/detalle_pedidos.csv](data/detalle_pedidos.csv) | Líneas de cada pedido |
| README.md | Presentación e instrucciones |

Los archivos de datos son CSV con cabecera, codificación UTF-8 y decimales con punto.

## Modelo de datos

| Tabla | Una fila representa | Clave | Filas originales |
|---|---|---|---:|
| clientes | Un cliente | id_cliente | 500 |
| productos | Un producto | id_producto | 100 |
| pedidos | Un pedido | id_pedido | 5.000 |
| detalle_pedidos | Una línea de pedido | id_detalle | 10.681 |

Relaciones utilizadas:

- `clientes.id_cliente` → `pedidos.id_cliente`.
- `pedidos.id_pedido` → `detalle_pedidos.id_pedido`.
- `productos.id_producto` → `detalle_pedidos.id_producto`.

## Usar en Microsoft Fabric

1. Importa `Spark_SQL_Referencia.ipynb` en tu área de trabajo.
2. Usa PySpark como lenguaje del notebook y adjunta un Lakehouse con acceso desde la sesión.
3. Sube los cuatro CSV a la carpeta `Files/spark_sql/data/` de ese Lakehouse.
4. En la celda de configuración, establece:

   ```python
   BASE_DATOS = "Files/spark_sql/data"
   ```

5. Ejecuta primero la configuración y la carga de datos.
6. Ejecuta después las consultas que quieras practicar.

Las consultas se ejecutan mediante `spark.sql(...)` dentro de celdas Python. Puedes copiar únicamente el SQL a una celda de Fabric que empiece por `%%sql`.

La carga registra vistas temporales llamadas `clientes`, `productos`, `pedidos` y `detalle_pedidos`. No es necesario crear antes tablas con prefijo `dbo`. Las vistas desaparecen al terminar la sesión: vuelve a ejecutar la carga cuando abras una sesión nueva.

## Usar en local

Necesitas Python, Java compatible con la versión de Spark y un entorno con PySpark. Desde la carpeta del repositorio:

```bash
python -m pip install -r requirements.txt
python -m jupyterlab
```

Abre el notebook con el kernel del entorno donde instalaste las dependencias. Conserva esta configuración:

```python
BASE_DATOS = "data"
```

Si tu directorio de trabajo es otro, ajusta la ruta a la carpeta de los CSV.

## Prácticas opcionales con Delta

El notebook incluye estos indicadores, desactivados por defecto:

```python
ACTIVAR_DELTA = False
ACTIVAR_CLON = False
```

Activa `ACTIVAR_DELTA` solo en una sesión compatible con Delta Lake. Instalar PySpark por sí solo no añade soporte Delta. Activa `ACTIVAR_CLON` únicamente si tu runtime admite `SHALLOW CLONE`.

Las prácticas crean una tabla con nombre único para probar `INSERT`, `UPDATE`, `DELETE`, `DESCRIBE DETAIL`, `DESCRIBE HISTORY`, `VERSION AS OF` y `TIMESTAMP AS OF`.

La limpieza se controla mediante `ELIMINAR_PRACTICA`, también desactivado por defecto. Un clon superficial referencia archivos del origen y no constituye una copia de seguridad independiente.

## Cómo aprovechar el cuaderno

1. Lee la explicación en Markdown de cada bloque.
2. Ejecuta la consulta y observa sus columnas y resultados.
3. Cambia un filtro, una agrupación o un criterio de ordenación.
4. Practica con los ejercicios finales.
5. Usa la chuleta para recuperar la sintaxis cuando la necesites.

### Detalles que conviene recordar

- Spark SQL utiliza `LIMIT`; los ejemplos T-SQL con `TOP` deben adaptarse.
- `importe_total` pertenece al pedido y `importe_linea` a cada línea: sumar el importe de cabecera después de unirlo a sus líneas puede duplicar totales.
- Los ejemplos de ventas entregadas filtran `estado = 'entregado'`.
- `descuento_pct = 0.20` representa un descuento del 20 %.
- Un control de calidad puede devolver incidencias: es un resultado para investigar, no necesariamente un fallo del código.

## Validación

Las celdas de carga y las consultas de lectura se ejecutaron con los cuatro CSV en Spark 3.5.3. Las prácticas Delta quedaron desactivadas durante esa validación y deben comprobarse en el runtime de destino.

El notebook se distribuye sin resultados guardados ni credenciales.

## Datos y publicación

Antes de publicar los CSV, comprueba que son datos de práctica y que puedes compartirlos. `clientes.csv` incluye nombres, correos y teléfonos. Si no deben difundirse, usa un repositorio privado o sustituye esos campos por datos ficticios.

## Documentación oficial

- [Apache Spark: comenzar con Spark SQL](https://spark.apache.org/docs/3.5.3/sql-getting-started.html)
- [Apache Spark: sintaxis SQL](https://spark.apache.org/docs/3.5.3/sql-ref-syntax.html)
- [Microsoft Fabric: endpoint SQL del Lakehouse](https://learn.microsoft.com/en-us/fabric/data-engineering/lakehouse-sql-analytics-endpoint)
- [Microsoft Fabric: clonar tablas Delta](https://learn.microsoft.com/en-us/fabric/data-engineering/delta-lake-clone)
- [Delta Lake: utilidades de tablas](https://docs.delta.io/delta-utility/)
