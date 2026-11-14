# Proyecto Instacart

Análisis de los hábitos de compra de Instacart con Python y Pandas.

- **EV04 / EV05:** configuración del entorno y exploración inicial (`notebooks/00_entorno.ipynb`, `notebooks/01_ev04_instacart.ipynb`).
- **EV06:** limpieza, análisis y estructuración de los datos (`notebooks/pandas_labs.ipynb`), siguiendo las lecciones 2, 3 y 4 del material [Preparar y estructurar datos con Python](https://educated-show-144.notion.site/Preparar-y-estructurar-datos-con-Python-40f4671e02a180dd9642deb111c14df2).

## Estructura

```
instacart-analisis/
├── README.md
├── .gitignore
├── requirements.txt
├── data/
│   ├── raw/          # cinco CSV originales (no se suben a GitHub)
│   └── processed/    # archivos generados por pandas_labs.ipynb
├── notebooks/
│   ├── 00_entorno.ipynb
│   ├── 01_ev04_instacart.ipynb
│   └── pandas_labs.ipynb
└── reports/
    └── processing_report.md
```

## Datos

Copiar los cinco archivos originales, sin modificarlos, en `data/raw/`:

- `instacart_orders.csv`
- `order_products.csv`
- `products.csv`
- `aisles.csv`
- `departments.csv`

Los archivos usan punto y coma (`;`) como separador. No se incluyen en el repositorio: `data/raw/*.csv` está excluido en `.gitignore`.

## Ejecución en Windows

Desde la raíz del proyecto:

```powershell
py -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install -r requirements.txt
jupyter lab
```

Abrir `notebooks/pandas_labs.ipynb`, seleccionar el kernel del entorno `.venv` y ejecutar **Kernel → Restart Kernel and Run All Cells**. Las lecciones dependen unas de otras, así que deben ejecutarse en orden. La ejecución completa tarda alrededor de un minuto.

También se puede ejecutar sin abrir Jupyter:

```powershell
python -m jupyter execute --inplace notebooks/pandas_labs.ipynb
```

El cuaderno funciona tanto si se abre desde la raíz del proyecto como desde `notebooks/`.

## Contenido de `pandas_labs.ipynb`

| Lección | Ejemplos | Laboratorio guiado | Reto |
| --- | --- | --- | --- |
| 2. Limpieza y estandarización | duplicados, tipos, nulos, texto, duplicados implícitos | Pipeline de limpieza con informe de cambios | 7 ejercicios |
| 3. Filtrado, ordenamiento y agrupación | máscaras, `query`, `nlargest`, `groupby`, `pivot_table`, `transform` | Perfil de hábitos de compra | 7 ejercicios |
| 4. Combinación de datasets | `merge`, `validate`, `indicator`, `concat`, `melt` | Tabla de análisis por producto | 7 ejercicios |

Cada ejercicio de los retos tiene su código, su resultado y una respuesta breve.

## Archivos generados en `data/processed/`

| Archivo | Contenido | En GitHub |
| --- | --- | --- |
| `cleaning_report.csv` | Filas, nulos y memoria en cada paso de limpieza | Sí |
| `orders_clean.parquet` | Pedidos sin duplicados y con tipos optimizados | Sí |
| `products_clean.parquet` | Productos con nombres estandarizados | Sí |
| `order_products_clean.parquet` | Detalle de pedidos con tipos optimizados (32 MB) | No |
| `customer_segments.csv` | Clientes, pedidos y espera mediana por segmento | Sí |
| `segment_time_profile.csv` | % de pedidos por franja horaria en cada segmento | Sí |
| `master_table.parquet` | Tabla maestra: una fila por producto dentro de un pedido (85 MB) | No |
| `department_summary.csv` | Artículos y tasa de recompra por departamento | Sí |
| `product_profile.parquet` | Ficha de análisis con una fila por producto | Sí |
| `department_leaders.csv` | Producto con mayor recompra de cada departamento | Sí |

Los dos archivos más grandes se excluyen de Git por su tamaño y se regeneran al ejecutar el cuaderno.

Los criterios de limpieza y los resultados principales están en [`reports/processing_report.md`](reports/processing_report.md).
