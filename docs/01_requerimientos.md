# Requerimientos

| | |
|---|---|
| **Proyecto** | Tablero ejecutivo de ventas, rentabilidad y clientes de Moda Mukumbarí |
| **Solicitante** | Gerencia General |
| **Responsable** | Equipo de Analytics |
| **Versión** | 1.0 |
| **Fecha** | 2026-11-09 |

## Contexto

Moda Mukumbarí es una cadena de moda con 35 tiendas en siete países: Estados Unidos, China, Alemania, Reino Unido, Francia, España y Portugal. Cada país reporta sus ventas en Excel y en su propia moneda, así que la Gerencia General no tiene una cifra consolidada ni una forma rápida de comparar países. Además, las campañas de descuento se lanzan sin medir cuánto margen cuestan, y no se hace seguimiento a los clientes que dejan de comprar. La Gerencia pidió al área de Analytics un tablero ejecutivo para las reuniones mensuales de resultados.

## Objetivo

Dar a la Gerencia General una vista única y consolidada en USD de las ventas, la rentabilidad y los clientes de los dos últimos años. Con ella debe poder decidir en qué países, tiendas y categorías invertir, cómo ajustar la política de descuentos y a qué clientes dirigir acciones de retención.

## Usuarios

| Usuario | Uso |
|---|---|
| Gerencia General | Seguimiento mensual del negocio y decisiones de inversión por país y tienda |
| Gerencia Comercial | Desempeño de categorías y efecto de los descuentos en el margen |
| Mercadeo y CRM | Segmentación de clientes y seguimiento de clientes inactivos |

## Requerimientos funcionales

| ID | Requerimiento | Origen |
|---|---|---|
| RF-01 | Ventas netas, órdenes y ticket promedio, cada uno con su variación frente al año anterior | Solicitud de la Gerencia |
| RF-02 | Tendencia mensual de ventas netas comparada con el año anterior | Solicitud de la Gerencia |
| RF-03 | Mapa de ventas netas por país y ciudad | Solicitud de la Gerencia |
| RF-04 | Ranking de tiendas por ventas netas y margen | Propuesta de Analytics |
| RF-05 | Ventas netas y unidades por categoría y subcategoría, con su participación sobre el total | Solicitud de la Gerencia |
| RF-06 | Margen bruto por categoría | Solicitud de la Gerencia |
| RF-07 | Ventas y margen con descuento frente a ventas y margen sin descuento | Solicitud de la Gerencia |
| RF-08 | Ventas y margen por campaña de descuento | Propuesta de Analytics |
| RF-09 | Tasa de devolución por categoría | Propuesta de Analytics |
| RF-10 | Clientes totales, activos e inactivos | Solicitud de la Gerencia |
| RF-11 | Clientes por país, género y rango de edad | Solicitud de la Gerencia |
| RF-12 | Valor promedio por cliente según rango de edad | Solicitud de la Gerencia |
| RF-13 | Top 10 de clientes por ventas netas | Solicitud de la Gerencia |
| RF-14 | Lista de clientes inactivos con los días desde su última compra | Propuesta de Analytics |
| RF-15 | Filtros por año, país, tienda y categoría | Solicitud de la Gerencia |

## Requerimientos no funcionales

| ID | Requerimiento |
|---|---|
| RNF-01 | El proyecto se reconstruye completo desde el repositorio: el ETL usa rutas relativas y Power BI una sola ruta local, el parámetro `RutaProyecto` |
| RNF-02 | Las cifras del reporte coinciden con las de la capa gold y con las cifras de control |
| RNF-03 | El reporte se versiona en formato PBIP |
| RNF-04 | El reporte sigue la plantilla visual definida en `assets/Template.png` |
| RNF-05 | El ETL procesa el dataset completo, sin muestreo |
| RNF-06 | El README y el reporte citan la fuente de los datos y su licencia CC BY 4.0 |

## Alcance

**Incluido**

- Todas las ventas y devoluciones de las 35 tiendas en los dos años que trae la fuente
- Consolidación en USD con una tabla de tasas de cambio
- ETL por capas (bronze, silver, gold) a partir de los archivos fuente
- Modelo estrella y medidas en Power BI
- Reporte de tres páginas: resumen ejecutivo, rentabilidad y descuentos, y clientes

**Fuera de alcance**

- Ventas por canal: el tablero cubre solo la venta en tienda física
- Análisis de inventario: existencias, valor y rotación
- Desempeño de empleados
- Metas de venta, pronósticos y alertas
- Seguridad por filas

## Fuente de datos

| Fuente | Contenido | Actualización |
|---|---|---|
| `data/raw/transactions.csv` | Líneas de venta y devolución por factura, con cliente, producto, tienda, cantidad, descuento, moneda y método de pago | Estática |
| `data/raw/customers.csv` | Clientes con ciudad, país, género, fecha de nacimiento y ocupación | Estática |
| `data/raw/products.csv` | Productos con categoría, subcategoría, color, talla y costo de producción | Estática |
| `data/raw/stores.csv` | Tiendas con país, ciudad y número de empleados | Estática |
| `data/raw/discounts.csv` | Campañas de descuento con fechas, porcentaje y categoría | Estática |
| `data/raw/tasas_cambio.xlsx` | Tasa de cambio mensual a USD de EUR, CNY y GBP | Estática |

Las ventas, clientes, productos, tiendas y descuentos provienen del dataset [Global Fashion Retail Sales](https://www.kaggle.com/datasets/ricgomes/global-fashion-retail-stores-dataset) publicado por Ric. G. en Kaggle, con licencia CC BY 4.0. Las tasas de cambio las define el equipo de Analytics para el caso de estudio.

## Supuestos

- Los datos son sintéticos: simulan la operación de una cadena de moda multinacional.
- Cada fila de `transactions.csv` es un producto dentro de una factura. Las facturas con prefijo `RET` son devoluciones y restan de las ventas.
- Ventas netas = ventas − devoluciones, después de aplicar el descuento de cada línea.
- Margen bruto = ventas netas − costo de producción × unidades. Se asume que el costo de producción está en USD, y esto se valida en la exploración.
- Un cliente está activo si compró en los últimos 12 meses del periodo; si no, está inactivo.
- El periodo exacto y el número de filas se confirman en la exploración de datos.
- Todos los usuarios ven la totalidad de los datos.

## Criterios de aceptación

| Cifra de control | Valor |
|---|---|
| Ventas netas totales en USD | Por calcular en la exploración de datos |
| Ventas netas por año | Por calcular |
| Total de filas de transacciones, ventas y devoluciones | Por calcular |
| Órdenes por año | Por calcular |
| Clientes con al menos una compra | Por calcular |
| Ventas netas por país | Por calcular |

Además:

- Los porcentajes del reporte suman 100 % con cualquier combinación de filtros.
- El margen bruto más el costo es igual a las ventas netas en cualquier nivel de detalle.
- El reporte abre y se actualiza desde un clon limpio del repositorio.

## Control de cambios

| Versión | Fecha | Cambio |
|---|---|---|
| 1.0 | 2026-11-09 | Versión inicial |
