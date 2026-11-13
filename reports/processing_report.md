# Informe de procesamiento: actividad EV06

Resultados obtenidos al ejecutar `notebooks/pandas_labs.ipynb` con los cinco CSV originales de Instacart (pandas 3.1.1). Todas las cifras coinciden con los valores esperados del material de formación.

## 1. Limpieza (Lección 2)

| Tabla | Paso | Filas | Nulos | Memoria (MB) |
| --- | --- | --- | --- | --- |
| orders | crudo | 478 967 | 28 819 | 23,0 |
| orders | sin duplicados | 478 952 | 28 817 | 23,0 |
| orders | tipos | 478 952 | 28 817 | 7,2 |
| products | crudo | 49 694 | 1 258 | 3,1 |
| products | nombres | 49 694 | 0 | 3,1 |
| order_prods | crudo | 4 545 007 | 836 | 145,4 |
| order_prods | tipos | 4 545 007 | 836 | 54,5 |

Decisiones tomadas:

| Problema | Evidencia | Decisión |
| --- | --- | --- |
| 15 pedidos duplicados | Todos el miércoles a las 2 a. m.: fallo del sistema | Eliminar |
| Tipos `int64`/`float64` sobredimensionados | `orders` pasa de 23,0 a 7,2 MB | Convertir a `int8`/`int16`/`int32` e `Int16` *nullable* |
| 28 817 nulos en `days_since_prior_order` | Todos son primeros pedidos | Conservar (no hay pedido anterior) |
| 1 258 nulos en `product_name` | Todos en el pasillo 100 / departamento `missing` | Rellenar con `Unknown` |
| 836 nulos en `add_to_cart_order` | 70 pedidos de más de 64 productos; el sistema solo registra 64 posiciones | Conservar |
| 403 nombres con espacios dobles y 16 con espacio invisible | 6 321 nombres cambiaron al estandarizar | Corregir con `.str` |
| 141 nombres repetidos tras estandarizar | 268 de 280 productos implicados aparecen en pedidos | Conservar; analizar por `product_id` |

`order_prods`, `aisles` y `departments` no tienen duplicados ni problemas de espacios. El pipeline con `pipe` produce exactamente las mismas tablas que la limpieza manual (`equals` = `True`).

## 2. Hábitos de compra (Lección 3)

- La hora pico es las 10 a. m. (40 578 pedidos) y la celda más alta es el lunes a las 10 (7 881).
- El domingo es el día con más pedidos (84 090) y clientes (57 661).
- Segmentos de clientes según los pedidos observados:

| Segmento | Clientes | % | Pedidos | Espera mediana (días) |
| --- | --- | --- | --- | --- |
| ocasional (1) | 55 357 | 35,2 | 55 357 | 16 |
| regular (2 a 4) | 71 553 | 45,4 | 191 649 | 11 |
| frecuente (5 o más) | 30 527 | 19,4 | 231 946 | 6 |

- El 19,4 % de los clientes (frecuentes) hace casi la mitad de los pedidos y compra más por la mañana (35,2 % frente a 30,0 % de los ocasionales).
- El 9,31 % de los pedidos se hace exactamente 7 días después del anterior: hay un hábito de compra semanal.
- Los productos de las primeras posiciones del carrito se recompran más (0,679 en la 1.ª posición frente a 0,617 en la 5.ª).

## 3. Combinación y análisis por producto (Lección 4)

- La tabla maestra parte de `order_prods` y se une con `products`, `aisles`, `departments` y `orders` usando `how='left'` y `validate='m:1'`. Conserva las 4 545 007 filas, tiene 14 columnas y ninguna clave sin pareja.
- 28 906 pedidos (6 %) no tienen productos registrados; no forman parte de la tabla maestra. Una unión `inner` los habría eliminado sin aviso.
- Productos más pedidos: Banana (66 050) y Bag Of Organic Bananas (53 297).
- `produce` concentra el 29 % de los artículos, pero `dairy eggs` tiene mayor recompra (0,671 frente a 0,651).
- Entre los 739 productos con al menos 1 000 pedidos, el líder de recompra de cada uno de los 18 departamentos se guarda en `department_leaders.csv`. El primero es Whole Organic Omega 3 Milk (0,863): el producto más pedido no siempre es el más fiel.
- Los pasillos con más recompra son `milk` (0,781), `water seltzer sparkling water` y `fresh fruits`; el de menos es `spices seasonings` (0,152).

## Limitaciones

- Los segmentos usan los pedidos presentes en el dataset, no necesariamente todo el historial del cliente.
- Las tasas de recompra son proporciones de artículos, no de clientes.
- Hay 1 258 productos sin nombre (`Unknown`) y 141 nombres compartidos por varios `product_id`; los análisis agrupan por identificador.
- `master_table.parquet` (85 MB) y `order_products_clean.parquet` (32 MB) no se suben a GitHub por su tamaño; se regeneran ejecutando el cuaderno.
