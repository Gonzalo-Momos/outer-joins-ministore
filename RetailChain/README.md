# RetailChain — UNION y UNION ALL

Práctica de consolidación de inventario entre la Sucursal Norte y la Sucursal Sur de RetailChain, usando `UNION` y `UNION ALL`.

## ¿Cuántas filas devuelve cada consulta y por qué son distintas?

En este caso particular, las dos consultas devuelven exactamente **14 filas cada una** (no hay diferencia). Esto se debe a que, aunque los productos 103 (Monitor 4K 27"), 104 (Teclado Mecánico) y 106 (SSD Externo 1TB) existen en ambas sucursales, su `stock` es distinto en cada una (por ejemplo, el Monitor 4K tiene 5 unidades en Norte y 3 en Sur). Como `UNION` solo elimina filas que sean **idénticas en todas sus columnas**, y en este dataset ningún producto repetido tiene el mismo stock en las dos sucursales, no hay ninguna fila para eliminar. Por eso `UNION` y `UNION ALL` coinciden en este caso: 14 y 14.

## ¿Por qué UNION ALL es más eficiente que UNION?

`UNION`, antes de devolver el resultado final, tiene que comparar cada fila con todas las demás para detectar cuáles son exactamente iguales y eliminar los duplicados. Esa comparación (que internamente suele implicar ordenar o agrupar los datos) es una operación extra que consume tiempo y recursos, especialmente en tablas grandes. `UNION ALL` se salta ese paso por completo: simplemente junta los resultados de ambas consultas tal cual vienen, sin comparar ni filtrar nada, por eso es más rápido.

## ¿En qué casos de negocio usaría cada uno?

**UNION** lo usaría, por ejemplo, para armar una lista de clientes únicos que compraron en dos campañas de marketing distintas (si alguien participó en ambas con exactamente los mismos datos, no quiero contarlo dos veces), o para consolidar un catálogo de productos entre dos proveedores que a veces cargan el mismo producto por error.

**UNION ALL** lo usaría para sumar el total de transacciones diarias de dos cajas registradoras distintas en un mismo local (ahí me interesa el conteo real de operaciones, no filtrar nada), o para juntar logs de errores de dos servidores distintos en un sistema de monitoreo, donde cada registro es un evento real y no quiero perder ninguno aunque el texto del error se repita.

## ¿Qué pasa si las columnas no coinciden en número o tipo?

Si la primera parte del `UNION`/`UNION ALL` selecciona una cantidad distinta de columnas que la segunda, SQL Server devuelve un error de sintaxis indicando que ambas consultas deben tener el mismo número de columnas. Si el número de columnas coincide pero los tipos de datos no son compatibles entre sí (por ejemplo, una columna de texto contra una numérica en la misma posición), SQL también da error o, en algunos casos, intenta convertir el tipo automáticamente si es posible — pero si no puede, falla la ejecución completa de la consulta.
