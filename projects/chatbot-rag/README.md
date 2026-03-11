# Chatbot RAG para Busqueda de Vehiculos Usados

<p align="center">
  <img src="https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white" alt="Python"/>
  <img src="https://img.shields.io/badge/LangChain-121212?style=for-the-badge" alt="LangChain"/>
  <img src="https://img.shields.io/badge/OpenAI-412991?style=for-the-badge&logo=openai&logoColor=white" alt="OpenAI"/>
  <img src="https://img.shields.io/badge/FAISS-00599C?style=for-the-badge" alt="FAISS"/>
  <img src="https://img.shields.io/badge/FastAPI-009688?style=for-the-badge&logo=fastapi&logoColor=white" alt="FastAPI"/>
  <img src="https://img.shields.io/badge/React-61DAFB?style=for-the-badge&logo=react&logoColor=black" alt="React"/>
</p>

## Descripcion del Proyecto

Chatbot conversacional que utiliza **Retrieval Augmented Generation (RAG)** para realizar busquedas semanticas inteligentes de vehiculos usados. El sistema permite a los usuarios describir lo que buscan en lenguaje natural y recibe recomendaciones personalizadas.

---

## El Problema: Limitaciones de la Busqueda Tradicional

| Problema | Descripcion |
|----------|-------------|
| **Brecha Semantica** | La busqueda por palabras clave no entiende la intencion del usuario. Ej: "auto para familia grande" no muestra camionetas si no usas la palabra exacta |
| **Consultas Complejas** | Necesidades multi-atributo dificiles de expresar con filtros (marcar 20 casillas) |
| **Sin Contexto** | Cada busqueda es independiente, sin memoria conversacional |
| **Descubrimiento Limitado** | Se pierden vehiculos relevantes sin palabras exactas |

---

## Nuestra Solucion: Arquitectura RAG

RAG combina **busqueda semantica** con **IA conversacional** para ofrecer una experiencia de busqueda inteligente.

### Pipeline del Sistema

```
1. Convertir a Texto     -> Cada vehiculo se transforma en descripcion natural
2. Crear Embeddings      -> OpenAI embeddings (1,536 dimensiones)
3. Construir Vector Store -> FAISS indexa los 38,531 vehiculos
4. Consulta del Usuario  -> Se embebe y busca por similitud
5. Recuperar + Generar   -> Top 5 resultados + GPT-4o-mini sintetiza respuesta
```

### Componentes Principales

| Componente | Tecnologia | Funcion |
|------------|------------|---------|
| **Embeddings** | text-embedding-3-small | Transforma texto en vectores semanticos |
| **Vector Store** | FAISS | Busqueda de similitud ultra-rapida |
| **LLM** | gpt-4o-mini | Genera respuestas en lenguaje natural |
| **Orquestacion** | LangChain | Framework para pipelines de LLM |

---

## Dataset

| Metrica | Valor |
|---------|-------|
| **Publicaciones** | 38,531 vehiculos |
| **Dimensiones Embedding** | 1,536 |

### Caracteristicas por Vehiculo

- Fabricante, modelo, ano, precio
- Motor (combustible, cilindrada, tipo)
- Transmision
- Tipo de carroceria, color, traccion
- Kilometraje, garantia, estado

### Ejemplo de Conversion a Texto

```
"Subaru Outback del ano 2010. Precio: $9,200 USD.
Motor: gasoline de 2.5 litros. Transmision: automatic.
Kilometraje: 190,000 km. Tipo de carroceria: universal.
Color: silver. Traccion: all. Estado: owned."
```

---

## Como Funciona

### 1. Indexacion (Una vez)
```python
# Convertir vehiculos a descripciones de texto
descriptions = [vehicle_to_text(v) for v in vehicles]

# Crear embeddings con OpenAI
embeddings = OpenAIEmbeddings(model="text-embedding-3-small")

# Indexar en FAISS
vector_store = FAISS.from_texts(descriptions, embeddings)

# Persistir en disco
vector_store.save_local("faiss_index")
```

### 2. Consulta (Por cada pregunta)
```python
# Usuario pregunta en lenguaje natural
query = "SUVs economicos para familia"

# Buscar vehiculos similares
docs = vector_store.similarity_search(query, k=5)

# Generar respuesta con contexto
response = llm.generate(
    prompt=f"Basandote en estos vehiculos: {docs}, responde: {query}"
)
```

---

## Ventajas Clave de RAG

| Ventaja | Descripcion |
|---------|-------------|
| **Super Rapido** | Busquedas en segundos |
| **Economico** | 1 embedding + 1 llamada LLM por consulta |
| **Busqueda Semantica** | Encuentra resultados sin palabras exactas |
| **Persistente** | Vector store en disco, recarga instantanea |
| **Simple** | Menos trabajo que fine-tuning |
| **Conversacional** | Mantiene historial para seguimientos |

---

## Stack Tecnologico

### Backend
- **Python**: Lenguaje principal
- **LangChain**: Framework de orquestacion LLM
- **OpenAI API**: Embeddings + GPT-4o-mini
- **FAISS**: Vector store optimizado para CPU
- **FastAPI**: API REST
- **Pandas**: Procesamiento de datos

### Frontend
- **React**: Interfaz de usuario
- **CSS**: Estilos del chatbot

### Desarrollo
- **Google Colab**: Experimentacion
- **Git**: Control de versiones

---

## Arquitectura del Sistema

```
+------------------+     +------------------+     +------------------+
|                  |     |                  |     |                  |
|   React UI       | --> |   FastAPI        | --> |   LangChain      |
|   (Chatbot)      |     |   (Backend)      |     |   (RAG Pipeline) |
|                  |     |                  |     |                  |
+------------------+     +------------------+     +--------+---------+
                                                          |
                              +---------------------------+
                              |
              +---------------+---------------+
              |                               |
    +---------v---------+           +---------v---------+
    |                   |           |                   |
    |   FAISS           |           |   OpenAI API      |
    |   (Vector Store)  |           |   (LLM + Embed)   |
    |                   |           |                   |
    +-------------------+           +-------------------+
```

---

## Aplicacion Real

El sistema fue implementado como un **chatbot para automotoras**, permitiendoles ofrecer busqueda inteligente a sus clientes.

### Casos de Uso
- "Busco un auto familiar economico"
- "Necesito una camioneta 4x4 para el campo"
- "Quiero un auto deportivo de menos de 30mil dolares"
- "Muestrame autos automaticos con bajo kilometraje"

---

## Conclusiones

1. **RAG es ideal para tareas de recuperacion de datos** - Combina lo mejor de busqueda y generacion
2. **Embeddings pre-entrenados son suficientes** - No se necesita fine-tuning para capturar relaciones semanticas
3. **FAISS permite busqueda lista para produccion** - Infraestructura minima requerida
4. **Memoria conversacional agrega valor** - Permite refinar busquedas iterativamente

---

## Equipo

- **Nicolas Fripp**
- **Diego Aguiar**
- **Bruno Moraes**

---

## Trabajo Futuro

- Integracion con bases de datos en tiempo real
- Soporte multi-idioma
- Filtros combinados (semanticos + tradicionales)
- Recomendaciones personalizadas basadas en historial

---

*Proyecto Final - Deep Learning Febrero 2026 | Especializacion en Ciencia de Datos e IA - UTEC/MIT*
