# Guía de Uso: Implementación de Gestión de Documentos en Chroma DB con Embeddings

## Descripción
Este proyecto utiliza **Chroma DB** para almacenar documentos, junto con sus **embeddings** generados por el modelo **Sentence-BERT**. Los documentos contienen metadatos como la categoría, autor, fecha de creación, entre otros. El proyecto está diseñado para ejecutarse en Colab y permite agregar, leer, actualizar y eliminar documentos dentro de una colección.

### Requisitos del Sistema

- **Python**: El proyecto es compatible con **Python 3.7+**. Si no tienes Python instalado, puedes obtenerlo en [python.org](https://www.python.org/downloads/).
  
- **Google Colab (opcional)**: Este proyecto está diseñado para ser ejecutado en **Google Colab**, lo que te permite ejecutarlo sin tener que configurar un entorno local. Si decides usar tu propio entorno, asegúrate de tener Python 3.7 o superior.


## Token
Asegurate de tener a la mano un token de hugging face para poder realizar el consumo de los modelos en cuestión

## Asegúrate de tener las siguientes dependencias instaladas:

1. **Chroma DB**: Base de datos vectorial para almacenar y recuperar documentos con embeddings.
2. **Sentence-Transformers**: Para la generación de embeddings de los documentos.

### Instalar las dependencias

Ejecuta el siguiente código en una celda de Colab para instalar las dependencias necesarias:

```bash
!pip install chromadb sentence-transformers
```

## Configuración Inicial
#### Importar Librerías
Para comenzar, importa las librerías necesarias para interactuar con Chroma DB y generar los embeddings:
```
import chromadb
from sentence_transformers import SentenceTransformer
from datetime import datetime
```
## Crear Cliente y Colección
Luego, configura tu cliente de Chroma DB y selecciona o crea una colección donde almacenarás los documentos:

## Crear cliente de Chroma DB
```
client = chromadb.Client()

# Seleccionar o crear una colección
collection = client.get_collection("space_x_technology")
```
## Estructuración de flujo Colab 
El Documento Colab tiene un flujo secuencial, comenzando por la instalación de dependencias, para posteriormente definir variables, clases y funciones para un correcto consumo de la base de datos. Posteriormente con todas las funciones cargadas, se crear el consumo del CRUD, comenzando por la creación y terminando por la eliminación de un documento dentro de la colección

## Estructuración de los Metadatos
Los metadatos son una parte esencial de cada documento, por lo que puedes definir una estructura que facilite su manipulación. Por ejemplo, puedes definir una clase para almacenar los metadatos de manera estructurada:

```
class MetadatosDocumento:
    def __init__(self, categoria, autor, fecha_creacion, tema, lenguaje):
        self.categoria = categoria
        self.autor = autor
        self.fecha_creacion = fecha_creacion
        self.tema = tema
        self.lenguaje = lenguaje
```
Flujo de Trabajo
1. Generación de Embeddings
Primero, debes generar un embedding del texto que será almacenado en la base de datos. El modelo Sentence-BERT se utiliza para convertir el texto en un vector numérico que representa semánticamente el contenido del documento:
```
def obtener_vector_embeddings(texto_documento: str) -> list:
    model = SentenceTransformer('paraphrase-MiniLM-L6-v2')  # Modelo ligero y rápido
    vector_documento = model.encode(texto_documento).tolist()
    return vector_documento
```

 ### 2. Agregar un Documento
Para agregar un documento a la colección, necesitarás el texto del documento, los metadatos y el embedding generado. Este proceso agrega el documento a la base de datos, donde se almacenan tanto el texto, los metadatos como el embedding generado.

```
# Crear un documento con su correspondiente embedding y metadatos
documento = collection.add(
    ids=["documento_id"],  # Identificador único
    documents=[texto_documento],
    metadatas=[metadatos_documento],
    embeddings=[obtener_vector_embeddings(texto_documento)]
)
```
### 3. Leer un Documento
Para recuperar un documento por su ID, puedes acceder a él desde la colección. El documento recuperado incluirá tanto el texto como los metadatos y el embedding:
```
documento = collection.get(ids=["documento_id"])

if documento["documents"]:
    print(f"Documento encontrado: {documento['documents'][0]}")
    print(f"Metadatos: {documento['metadatas'][0]}")
    print(f"Embeddings: {documento['embeddings'][0]}")
```
### 4. Actualizar un Documento
Si necesitas actualizar un documento, primero debes verificar si el documento existe en la colección. Luego, puedes eliminar el documento antiguo y agregar uno nuevo con texto y metadatos actualizados:

```
# Verificar si el documento existe
documento_existente = collection.get(ids=["documento_id"])

if documento_existente["documents"]:
    # Eliminar el documento anterior
    collection.delete(ids=["documento_id"])

    # Crear el nuevo documento con metadatos y embeddings actualizados
    documento_actualizado = collection.add(
        ids=["documento_id"],
        documents=[nuevo_texto],
        metadatas=[metadatos_actualizados],
        embeddings=[obtener_vector_embeddings(nuevo_texto)]
    )
```
### 5. Eliminar un Documento
Si deseas eliminar un documento de la colección, verifica que el documento exista antes de proceder con su eliminación:
```
documento_existente = collection.get(ids=["documento_id"])

if documento_existente["documents"]:
    # Eliminar el documento
    collection.delete(ids=["documento_id"])
```
