# outer-joins-ministore

Práctica de LEFT, RIGHT y FULL OUTER JOIN sobre un catálogo de productos y un historial de ventas de MiniStore, una tienda minorista.

## Estructura

- `schema.sql`: crea las tablas `productos` y `ventas`, y carga los datos de prueba.
- `soluciones.sql`: contiene las tres consultas con los distintos tipos de JOIN.

## ¿Por qué usé LEFT JOIN en la Consulta 1 y no INNER JOIN?

Un INNER JOIN solo devuelve las filas que tienen coincidencia en ambas tablas. Si lo hubiera usado acá, los productos que nunca se vendieron (Hub USB-C y Parlante Bluetooth) directamente no aparecerían en el resultado, como si no existieran en el catálogo. La pregunta de negocio era justo esa: identificar qué productos nunca se vendieron. Con LEFT JOIN me aseguro de traer todos los productos, tengan ventas o no, y los que no tienen venta quedan con `NULL` en las columnas de la tabla `ventas`.

## ¿Por qué usé RIGHT JOIN en la Consulta 2? ¿Qué tabla quedó a la izquierda y cuál a la derecha?

En mi consulta, `productos` está en el `FROM` (a la izquierda) y `ventas` está en el `RIGHT JOIN` (a la derecha). Usé RIGHT JOIN porque necesitaba garantizar que TODAS las ventas aparecieran en el resultado, incluso aquellas cuyo `producto_id` no existe en el catálogo (como la venta con `producto_id = 999`). De esta forma puedo detectar posibles errores de carga de datos: ventas que hacen referencia a productos inexistentes.

## ¿Qué representan los valores NULL en cada resultado?

Un `NULL` indica que no hubo coincidencia entre las tablas para esa fila.

- En la **Consulta 1**, si `venta_id` es `NULL`, significa que ese producto del catálogo nunca fue vendido. Por ejemplo, el producto "Hub USB-C 7p" aparece con `venta_id` en `NULL` porque no tiene ninguna venta asociada.
- En la **Consulta 2**, si `producto_id` de la tabla `productos` es `NULL`, significa que la venta hace referencia a un producto que no existe en el catálogo. Es el caso de la venta 10, con `producto_id = 999`: ese producto nunca fue dado de alta en `productos`, por lo que probablemente sea un error de carga de datos.

## ¿Cuándo usaría FULL OUTER JOIN en un caso real de negocio?

Usaría FULL OUTER JOIN cuando necesito una vista de auditoría completa, donde no puedo perder información de ninguna de las dos tablas. Por ejemplo, para detectar en un mismo reporte tanto productos sin ventas como ventas con datos corruptos (productos inexistentes), sin tener que correr dos consultas separadas y después cruzar los resultados a mano.
