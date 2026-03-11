# Prediccion de Precios de Casas en Uruguay

<p align="center">
  <img src="https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white" alt="Python"/>
  <img src="https://img.shields.io/badge/Pandas-150458?style=for-the-badge&logo=pandas&logoColor=white" alt="Pandas"/>
  <img src="https://img.shields.io/badge/CatBoost-FFCC00?style=for-the-badge&logoColor=black" alt="CatBoost"/>
  <img src="https://img.shields.io/badge/Scikit--learn-F7931E?style=for-the-badge&logo=scikit-learn&logoColor=white" alt="Scikit-learn"/>
</p>

## Descripcion del Proyecto

Modelo de Machine Learning para predecir el precio de casas a la venta en Uruguay, utilizando datos reales de las principales plataformas inmobiliarias del pais: **Mercado Libre** y **El Gallito**.

### Objetivo

Crear un modelo predictivo que permita:
- Guiar la fijacion del precio de venta de una casa
- Identificar propiedades sobre-valuadas o infra-valuadas
- Detectar oportunidades de compra o correccion de precios
- Analizar las variables mas relevantes en la determinacion del precio

---

## Dataset

| Metrica | Valor |
|---------|-------|
| **Observaciones Iniciales** | 177,394 |
| **Variables Originales** | 33 |
| **Muestra Final** | 14,337 |
| **Variables Finales** | 11 explicativas + 1 objetivo |

### Fuentes de Datos
- **Mercado Libre**: Publicaciones de propiedades en Uruguay
- **El Gallito**: Portal inmobiliario tradicional uruguayo
- **Periodo**: 2023 - 2025

### Variables Clave

| Variable | Descripcion |
|----------|-------------|
| `NEIGHBORHOOD` | Barrio de la propiedad |
| `COVERED_AREA` | Area cubierta en m2 |
| `BEDROOMS` | Cantidad de dormitorios |
| `FULL_BATHROOMS` | Cantidad de banos |
| `GARAGE` | Cantidad de garages |
| `ADDRESS_STATE` | Departamento (Montevideo, Maldonado, Canelones, Otros) |
| `CONDITION` | Estado (usado, nuevo, en construccion) |
| `LISTING_TYPE_ID` | Tipo de publicacion |
| `WITH_VIRTUAL_TOUR` | Si tiene tour virtual |
| `HAS_AIR_CONDITIONING` | Si tiene aire acondicionado |
| `PRICE` | **Variable objetivo** - Precio en USD |

---

## Preprocesamiento de Datos

### Limpieza
- Eliminacion de variables no informativas (IDs, descripciones textuales)
- Remocion de **136,560 duplicados** (77% de los datos)
- Unificacion de datos de ambas fuentes

### Tratamiento de Nulos
- Imputacion de departamento basado en barrio/ciudad
- Imputacion de area cubierta por mediana segun dormitorios y banos
- Imputacion de tipo de publicacion para datos del Gallito

### Transformaciones
- Conversion de precios a USD segun tipo de cambio historico
- Reagrupacion de condiciones: usado, nuevo, sin especificar, en construccion
- Reagrupacion de departamentos: Montevideo, Maldonado, Canelones, Otros
- Eliminacion de observaciones con patron "111" y precios < 1000 USD

---

## Analisis Exploratorio

### Variable Objetivo (Precio)
- Distribucion log-normal
- Precio promedio: ~USD 200,000

### Correlaciones Principales
- **Area cubierta** y **baños**: alta correlacion con precio
- **Barrio**: factor determinante del precio
- **Departamento**: Maldonado con precios superiores

---

## Modelos

### 1. Regresion Lineal (Baseline)
- Variable objetivo: `log(precio)`
- Variables numericas en escala logaritmica
- Variables categoricas como dummies

**Resultados:**
- R2 = 0.52
- MAPE = 40% (~USD 80,000 de error)

### 2. Regularizacion (Ridge y Lasso)
- Resultados similares al modelo lineal
- Pocas variables y baja multicolinealidad
- La regularizacion no agrego valor significativo

### 3. CatBoost
Seleccionado por su manejo nativo de variables categoricas (6 de 10 variables son categoricas).

**Configuracion:**
- Sin agrupacion de barrios ni departamentos (mejor rendimiento con datos originales)
- Eliminacion de variable `ORIGEN` (redundante con `LISTING_TYPE_ID`)

### 4. CatBoost + Optuna
Optimizacion de hiperparametros mediante Optuna.

---

## Resultados

| Modelo | MAPE |
|--------|------|
| Regresion Lineal | 39.9% |
| CatBoost | 33.8% |
| CatBoost + Optuna | 32.0% |
| **CatBoost + Optuna (Montevideo)** | **25%** |
| CatBoost + Optuna (Canelones) | 26% |
| CatBoost + Optuna (Maldonado) | 37% |

### Interpretacion
Con un precio promedio de USD 200,000:
- MAPE de 25% = error tipico de **+/- USD 50,000** por vivienda en Montevideo

### Importancia de Variables
Las variables mas influyentes en el modelo:
1. Area cubierta (m2)
2. Barrio
3. Cantidad de banos
4. Cantidad de dormitorios
5. Departamento

---

## Conclusiones

- **El mejor modelo es CatBoost optimizado con Optuna**
- El modelo es util como **referencia inicial de precios**, no como valuacion profesional
- Los modelos por departamento (especialmente Montevideo y Canelones) muestran mejor rendimiento
- El barrio y el area cubierta son los factores mas determinantes del precio

---

## Tecnologias Utilizadas

- **Python**: Lenguaje principal
- **Pandas / NumPy**: Manipulacion de datos
- **Matplotlib / Seaborn**: Visualizacion
- **Scikit-learn**: Regresion lineal y preprocesamiento
- **CatBoost**: Modelo de gradient boosting
- **Optuna**: Optimizacion de hiperparametros

---

## Equipo

- **Nicolas Fripp** - nicolas.fripp@estudiantes.utec.com.uy
- **Diego Aguiar** - diego.aguiar@estudiantes.utec.com.uy
- **Bruno Moraes** - bruno.moraes@estudiantes.utec.com.uy

---

## Trabajo Futuro

- Incorporar mas variables (antiguedad, amenities, cercanias)
- Modelos especializados por zona geografica
- Integracion con APIs inmobiliarias para prediccion en tiempo real
- Desarrollo de aplicacion web para consultas

---

*Proyecto Final - Machine Learning 2025 | Especializacion en Ciencia de Datos e IA - UTEC/MIT*
