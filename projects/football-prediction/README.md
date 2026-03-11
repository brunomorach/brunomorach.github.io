# From Data to Goal - Prediccion de Partidos de Futbol

<p align="center">
  <img src="https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white" alt="Python"/>
  <img src="https://img.shields.io/badge/Scikit--learn-F7931E?style=for-the-badge&logo=scikit-learn&logoColor=white" alt="Scikit-learn"/>
  <img src="https://img.shields.io/badge/XGBoost-EC6C35?style=for-the-badge" alt="XGBoost"/>
  <img src="https://img.shields.io/badge/LightGBM-02569B?style=for-the-badge" alt="LightGBM"/>
</p>

## Descripcion del Proyecto

Sistema de prediccion de resultados de partidos de futbol de **La Liga espanola** utilizando tecnicas de Machine Learning. El proyecto explora la posibilidad de predecir outcomes (victoria local, empate, victoria visitante) usando datos historicos y odds de casas de apuestas.

### Problema y Motivacion

> "Las personas quieren obtener ganancias del fútbol y conocer los resultados con anticipación, pero la naturaleza impredecible de este deporte hace que esto sea casi imposible; por lo tanto, creamos un modelo de Machine Learning para predecir los partidos de fútbol."

El futbol es inherentemente impredecible, lo que hace de este un desafio interesante para Machine Learning.

---

## Dataset

| Metrica | Valor |
|---------|-------|
| **Observaciones** | 3,591 partidos |
| **Features** | 116 variables |
| **Temporadas** | 10 (2016-2025) |
| **Fuentes** | Football DataCo, Transfermarkt |

### Fuentes de Datos

1. **Football DataCo**: Estadisticas de partidos, goles, tarjetas, tiros, corners
2. **Transfermarkt**: Valor de mercado, edad promedio, jugadores extranjeros

### Features Principales

| Categoria | Variables |
|-----------|-----------|
| **Informacion del Partido** | Fecha, equipo local, equipo visitante |
| **Estadisticas** | Goles (FT/HT), tiros, corners, faltas, tarjetas |
| **Odds de Apuestas** | Cuotas de casas de apuestas |
| **Mercado** | Valor de mercado total, valor promedio por jugador |
| **Demografico** | Edad promedio del equipo, jugadores extranjeros |

---

## Analisis Exploratorio

### Distribucion de Clases (Desbalanceada)
- **Victoria Local**: 45.1%
- **Empate**: 28.5%
- **Victoria Visitante**: 26.4%

### Hallazgos Clave

1. **Dominancia Historica**: Real Madrid y Barcelona dominan acumulativamente desde 1929
2. **Resultado mas comun**: 1-1 (12.7% de ocurrencia)
3. **Ventaja Local**:
   - Equipos locales tienen mas tiros, tiros al arco y corners
   - Equipos visitantes reciben mas tarjetas amarillas
   - El local gana el 45.2% de los partidos
4. **Efecto COVID**: La ventaja local disminuyo significativamente durante la pandemia (estadios vacios)
5. **Odds de Apuestas**: Las casas de apuestas tienen modelos bastante precisos

### Correlaciones Importantes

| Feature 1 | Feature 2 | Correlacion |
|-----------|-----------|-------------|
| Home Win Odds | Away Total Market Value | 0.68 |
| Corners | Shots | 0.53 |
| Avg Age | Total Market Value | -0.45 |
| Yellow Cards | Fouls | 0.36 |
| Goals | Total Market Value | 0.29 |

---

## Preprocesamiento

### Limpieza de Datos
- Eliminacion de columnas con >20% valores nulos
- Imputacion de nulos restantes por mediana
- Sin duplicados

### Limitaciones del Dataset
- Desbalance de clases (45.1% H - 28.5% D - 26.4% A)
- Sin informacion de jugadores individuales
- Sin datos de lesiones/suspensiones
- Sin informacion meteorologica
- Sin datos de otros torneos

### Feature Engineering

**Modelo Pre-Partido:**
- Promedios de goles, tiros, tarjetas, puntos (local/visitante)
- Diferencias entre equipos
- Odds de casas de apuestas
- Valor de mercado

**Modelo In-Play (adicionales):**
- Goles al medio tiempo
- Diferencia de goles al HT
- Resultado parcial

---

## Modelos

### Configuracion del Entrenamiento

```
Train: 2016-2024 (90%)
Test:  2025 (10%)
```

### Modelos Evaluados

1. **Random Forest**
2. **LightGBM**
3. **XGBoost**

Todos optimizados con **Optuna** para hiperparametros.

### Por que estos modelos?
- Problema de clasificacion multiclase
- Manejo nativo de desbalance de clases
- Capacidad de capturar relaciones no lineales

---

## Resultados

### Modelo Pre-Partido

| Modelo | F1-Score | Accuracy |
|--------|----------|----------|
| **Random Forest** | **0.52** | 0.51 |
| LightGBM | 0.50 | 0.54 |
| XGBoost | 0.47 | 0.48 |

### Modelo In-Play

| Modelo | F1-Score | Accuracy |
|--------|----------|----------|
| **Random Forest** | **0.57** | 0.56 |
| LightGBM | 0.55 | 0.54 |
| XGBoost | 0.56 | 0.54 |

> **Nota**: F1-Score es mejor metrica para clases desbalanceadas.

### Mejor Modelo
- **Pre-Partido**: Random Forest (F1 = 0.52)
- **In-Play**: Random Forest (F1 = 0.57)

### Importancia de Features

**Pre-Partido**: Dominado por odds de casas de apuestas

**In-Play**: Dominado por el marcador al medio tiempo

---

## Conclusiones

1. **Los modelos funcionan** - Predicen mejor que el baseline (probabilidad de victoria local)
2. **In-Play > Pre-Partido** - Logicamente, tener informacion del primer tiempo mejora las predicciones
3. **Alta varianza del futbol** - Lograr F1 > 0.6 es extremadamente dificil
4. **Empates son dificiles** - Predecir empates es el mayor desafio
5. **El futbol es futbol** - La impredecibilidad es parte de su esencia

### Trabajo Futuro
- Incorporar informacion de jugadores individuales
- Agregar datos de lesiones y suspensiones
- Incluir informacion de otros torneos
- Datos meteorologicos

---

## Tecnologias Utilizadas

- **Python**: Lenguaje principal
- **Pandas / NumPy**: Manipulacion de datos
- **Matplotlib / Seaborn**: Visualizacion
- **Scikit-learn**: Preprocesamiento y Random Forest
- **XGBoost**: Gradient Boosting
- **LightGBM**: Gradient Boosting
- **Optuna**: Optimizacion de hiperparametros

---

## Equipo

- **Nicolas Fripp**
- **Emiliano Braña**
- **Bruno Moraes**

---

*Proyecto Final - Taller MIT Enero 2026 | Especializacion en Ciencia de Datos e IA - UTEC/MIT*
