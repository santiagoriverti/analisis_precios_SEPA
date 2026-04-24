# 🛒 Análisis de Precios SEPA (Precios Claros)

Pipeline optimizado para procesamiento masivo de datos del sistema SEPA (Sistema Electrónico de Publicidad de Precios Argentinos), con foco en eficiencia de memoria, reproducibilidad y generación de canastas comparables.

---

## 📊 Descripción

Este proyecto automatiza la ingesta, limpieza, integración y análisis de los datos publicados por SEPA.

A diferencia de enfoques tradicionales, este pipeline está diseñado para trabajar con datasets muy grandes (millones de registros), minimizando el uso de RAM y permitiendo recuperación rápida ante fallos mediante persistencia intermedia en disco.

---

## 🚀 Características principales

- 📦 Descompresión automática de ZIPs anidados
- ⚡ Lectura optimizada con tipos de datos eficientes (`int32`, `float32`, `category`)
- 💾 Cacheo en formato **Parquet** para evitar reprocesamientos
- 🧹 Limpieza y filtrado de datos irrelevantes
- 🔗 Integración de:
  - Productos
  - Comercios
  - Sucursales
- 🏷️ Clasificación automática de productos por rubro
- 🧠 Selección de productos **canastables** (comparables entre cadenas)
- 🔁 Deduplicación inteligente por **concepto de producto**
- 📍 Preparado para análisis geográfico
- ☁️ Exportación a Google Drive

---

## 🧱 Arquitectura del pipeline

El flujo se divide en dos etapas principales:

### 🔹 ETAPA A — Ingesta y consolidación

1. Descompresión de archivos diarios
2. Carga optimizada de CSVs
3. Filtrado de:
   - Farmacias
   - Estaciones de servicio
4. Construcción de `df_precios` (tabla unificada)
5. Persistencia en cache (`parquet`)

**Outputs:**
- `df_comercio`
- `df_sucursales`
- `df_precios`

---

### 🔹 ETAPA B — Selección de canasta

1. Cálculo de métricas por producto:
   - Cobertura (banderas, provincias)
   - Distribución de precios (p10, p50, p90)
2. Filtros de calidad:
   - Presencia nacional
   - Estabilidad de precios
3. Clasificación por rubro (modelo heurístico)
4. Generación de **productos canastables**

**Output:**
- `canastables.parquet`

---

### 🔹 ETAPA C — Deduplicación (concepto de producto)

Se eliminan variantes redundantes (ej: tamaños distintos del mismo producto):

- Extracción de **concepto** (marca + tipo)
- Eliminación de:
  - Unidades (kg, ml, etc.)
  - Formatos (pack, botella, etc.)
  - Palabras irrelevantes
- Selección del mejor representante por cobertura

**Output final:**
- `canasta_dedup.parquet`

---

## 📂 Estructura de datos

### DataFrames principales

- `df_precios`  
  → Tabla consolidada con precios + ubicación + cadena

- `canastables`  
  → Productos comparables entre cadenas

- `canasta_dedup`  
  → Canasta final sin duplicados conceptuales

---

## 📁 Outputs

Archivos generados (formato Parquet):

- `df_comercio.parquet`
- `df_sucursales.parquet`
- `df_precios.parquet`
- `canastables.parquet`
- `canasta_dedup.parquet`

---

## ⚙️ Optimizaciones clave

- ✅ Uso de `category` para strings repetidos
- ✅ Conversión temprana de tipos numéricos (`float32`, `int32`)
- ✅ Eliminación temprana de columnas innecesarias (promos)
- ✅ Liberación explícita de memoria (`del` + `gc.collect()`)
- ✅ Pipeline idempotente con cache en disco
- ✅ Evita duplicación de DataFrames grandes en RAM

---

## 🧪 Criterios de selección de canastables

Un producto entra en la canasta si cumple:

- ≥ 8 cadenas
- ≥ 5 provincias
- Ratio p90/p10 ≤ 3
- Precio mediano ≥ $50

---

## 🏷️ Rubros utilizados

- Almacén
- Bebidas
- Lácteos
- Carnes y fiambres
- Panadería y harinas
- Limpieza
- Higiene personal
- Bebé
- Snacks y golosinas
- Alcohol

---

## ☁️ Uso en Google Colab

### 1. Ejecutar pipeline principal

```python
df_comercio, df_sucursales, df_precios, canastables = pipeline()
