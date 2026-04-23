# analisis_precios_SEPA

Proyecto de análisis de precios basado en datos de Precios Claros (SEPA). Automatiza la ingesta de archivos ZIP, normaliza y unifica datos de productos, comercios y sucursales, calcula métricas de precios y genera reportes en Excel, incluyendo análisis por ubicación y filtros temáticos.

---

## 📊 Descripción

Este proyecto permite procesar de forma automática los datos publicados por SEPA (Sistema Electrónico de Publicidad de Precios Argentinos), integrando múltiples archivos comprimidos diarios en una estructura unificada para su análisis.

Se enfoca en:
- Consolidación de datos de productos, comercios y sucursales
- Limpieza y tipado de variables
- Generación de métricas de precios
- Análisis específicos por categorías (ej: verduras de hoja)
- Exportación de resultados a Excel

---

## ⚙️ Funcionalidades

- 🔍 Detección automática de archivos `sepa_*.zip`
- 📦 Extracción de ZIPs anidados en memoria
- 🧹 Limpieza y normalización de datos
- 🔗 Integración entre productos, comercios y sucursales
- 📈 Cálculo de:
  - Precio promedio
  - Precio mínimo
  - Precio máximo
- 🛒 Generación de catálogo de productos únicos
- 🥬 Filtros por palabras clave (ej: "lechuga", "ensalada")
- 📍 Análisis geográfico por sucursal
- 📤 Exportación a Excel con formato

---

## 🗂️ Estructura de datos

El pipeline genera tres DataFrames principales:

- `df_productos`: información de precios y productos
- `df_comercios`: datos de cadenas comerciales
- `df_sucursales`: ubicación y características de sucursales

---

## 📁 Outputs

El proyecto genera los siguientes archivos:

- `productos_unicos.xlsx`  
  → Catálogo deduplicado de productos

- `productos_precios_promedio.xlsx`  
  → Métricas agregadas por producto

- `verduras_hoja_por_sucursal.xlsx`  
  → Análisis filtrado por keywords con detalle geográfico

---

## 🚀 Uso

1. Subir archivos `sepa_*.zip` al entorno (por ejemplo, Google Colab)
2. Ejecutar el script completo
3. Revisar los archivos Excel generados en `/content`

---

## 🧰 Requisitos

- Python 3.9+
- pandas
- openpyxl
- tqdm

Instalación rápida:

```bash
pip install pandas openpyxl tqdm
