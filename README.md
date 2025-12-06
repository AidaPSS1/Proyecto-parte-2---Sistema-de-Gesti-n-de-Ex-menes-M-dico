# Sistema de Gestión de Exámenes Médicos

Este proyecto implementa un asistente inteligente para la gestión y consulta de información relacionada con los exámenes médicos de la Universidad Nacional Mayor de San Marcos (UNMSM). Utiliza tecnologías de Inteligencia Artificial como LangChain y LangGraph para interactuar con bases de datos SQL y sistemas de recuperación de información (RAG) basados en documentos PDF, ofreciendo una experiencia personalizada según el rol del usuario (Estudiante, Médico, Administrativo).

## Características Principales

*   **Asistente Conversacional Multi-rol**: Responde preguntas y gestiona tareas según el perfil del usuario.
    *   **Estudiantes**: Consulta cronogramas de exámenes, requisitos, y procedimientos.
    *   **Médicos**: Registra resultados de evaluaciones, consulta protocolos, y ve el estado de las evaluaciones.
    *   **Administrativos**: Genera reportes estadísticos, consulta el estado de exámenes por facultad/escuela, y revisa normativas.
*   **Consulta de Base de Datos SQL**: Interacción con tablas como `matriculas`, `evaluacionesmedicas` y `cronogramaexamenesmedicos` para obtener y registrar datos estructurados.
*   **Recuperación Aumentada de Generación (RAG)**: Utiliza un sistema de búsqueda vectorial (Elasticsearch) sobre documentos PDF (normativas y procedimientos) para responder preguntas complejas basadas en el contenido de los documentos.
*   **Identificación de Usuario**: Identifica y saluda al usuario por su nombre y rol basándose en un código de matrícula o DNI.
*   **Manejo de Dependencias**: Utiliza pip para gestionar las librerías necesarias.

## Tecnologías Utilizadas

*   **Python 3.x**
*   **LangChain**: Framework para el desarrollo de aplicaciones basadas en LLMs.
*   **LangGraph**: Para construir agentes robustos y con estado.
*   **OpenAI GPT Models**: Para la generación de lenguaje natural y embeddings.
*   **PostgreSQL**: Base de datos relacional para almacenar información de matrículas, evaluaciones y cronogramas.
*   **Elasticsearch**: Base de datos de búsqueda distribuida para el sistema RAG.
*   **PyPDFLoader**: Para la carga de documentos PDF.
*   **Pydantic**: Para la validación de datos.
*   **psycopg2**: Adaptador de PostgreSQL para Python.

## Configuración e Instalación

### 1. Entorno de Colab y Dependencias

Este proyecto está diseñado para ejecutarse en Google Colab. Asegúrate de tener un entorno de Python compatible.

```python
# Instalar librerías necesarias
!pip install -qU langchain langchain_openai langchain-core>=1.0.0 langchain-community langgraph langchain-text-splitters langchain-elasticsearch langchain-classic psycopg[binary,pool]==3.2.6 psycopg2-binary pypdf
```

### 2. Conexión a Google Drive

El proyecto requiere acceso a archivos almacenados en Google Drive, incluyendo claves API y documentos PDF.

```python
from google.colab import drive
drive.mount('/content/drive')
```

### 3. Configuración de API Keys y Base de Datos

Deberás tener los siguientes archivos en tu Google Drive en las rutas especificadas:

*   `/content/drive/MyDrive/api_key_openAI.txt`: Contiene tu clave API de OpenAI.
*   `/content/drive/MyDrive/postgrest.txt`: Contiene la URI de conexión a tu base de datos PostgreSQL (ej. `postgresql://user:password@host:port/dbname`).

```python
import os

# Cargar API Key de OpenAI
with open("/content/drive/MyDrive/api_key_openAI.txt") as archivo:
    apikey = archivo.read().strip()
os.environ["OPENAI_API_KEY"] = apikey

# Cargar URI de PostgreSQL
with open("/content/drive/MyDrive/postgrest.txt") as archivo:
    uribd = archivo.read().strip()
```

### 4. Configuración de Elasticsearch

Se requiere una instancia de Elasticsearch para el componente RAG. La configuración se define en el siguiente diccionario:

```python
ES_CONFIG = {
    "es_url": "http://<your_elastic_ip>:9200",
    "es_user": "elastic",
    "es_password": "<your_elastic_password>",
    "index_normativas": "medical_normativas",
    "index_procedimientos": "medical_procedimientos"
}
```

### 5. Documentos PDF para RAG

Crea las siguientes carpetas en tu Google Drive y coloca los archivos PDF correspondientes:

*   `/content/drive/MyDrive/proy/Normativa`: Documentos PDF con normativas.
*   `/content/drive/MyDrive/proy/Procedimientos`: Documentos PDF con procedimientos.

```python
RUTA_NORMATIVAS = "/content/drive/MyDrive/proy/Normativa"
RUTA_PROCEDIMIENTOS = "/content/drive/MyDrive/proy/Procedimientos"

# Inicializa la base vectorial de Elasticsearch
inicializar_base_vectorial()
```

## Uso

El sistema se maneja a través de la clase `SistemaExamenesMedicos`. Primero, crea una instancia, luego inicia sesión con un código de usuario (matrícula o DNI), y finalmente puedes hacer preguntas.

```python
# 1. Crear sistema
sistema = SistemaExamenesMedicos()

# 2. Iniciar sesión con un código de usuario (ej. estudiante)
mensaje_bienvenida = sistema.iniciar_sesion("22030158") # Usa un código de matrícula o DNI real de tu BD
print(mensaje_bienvenida)

# 3. Hacer preguntas
respuesta = sistema.preguntar("¿Qué requisitos debo llevar al examen médico?")
print(respuesta)

# 4. Cerrar sesión
sistema.cerrar_sesion()
```

Ejemplos de códigos de usuario para diferentes roles (deben existir en tu base de datos `matriculas`):

*   **Estudiante**: `"22030158"` (o un código similar)
*   **Administrativo**: `"56789012"` (o un DNI similar)
*   **Médico**: `"63636363"` (o un DNI similar)

## Estructura del Proyecto

```
proyecto2.ipynb           # Notebook principal con todo el código
api_key_openAI.txt        # (En Google Drive) Clave API de OpenAI
postgrest.txt             # (En Google Drive) URI de conexión a PostgreSQL
proy/                     # Carpeta para documentos
├── Normativa/            # (En Google Drive) Documentos PDF de normativas
│   └── normativa1.pdf
│   └── ...
└── Procedimientos/       # (En Google Drive) Documentos PDF de procedimientos
    └── procedimiento1.pdf
    └── ...
```
