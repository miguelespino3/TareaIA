### Herramientas para el diseño de contenidos y actividades
1. Para comprobar la correcta implementación de la ontología por parte del agente de IA, es buena verla relacionada en forma de gráficos, para ello, JSON Crack nos facilita el trabajo, ya que le adjuntamos el archivo .json y nos muestra el gráfico con las interrelaciones:
<img src="Datos tarea IA/JSONCrack.png" alt="Imagen JSONCrack" width="800"/>

2. Otra herramienta útil puede ser el uso de BD relacionadas como MySQL para importar la ontología y permite consultas rápidas sobre las relaciones, como, por ejemplo, qué lenguajes son multiparadigma.
Para ello, adjuntamos el fichero “lenguaje-programacion-onto-y-instancias.json” a ChatGPT y le preguntamos:

```
¿Como importamos la ontología a una Base de Datos llamada pruebasOntología en MySQL?
```

<details>
<summary>Ver respuesta</summary>
Pasos para Importar la Ontología a MySQL

# 1. Crear la Base de Datos
Abre tu cliente MySQL (como MySQL Workbench o la línea de comandos) y crea la base de datos:
CREATE DATABASE pruebasOntologia;
USE pruebasOntologia;

# 2. Definir las Tablas
Crea las tablas necesarias para almacenar la información de la ontología. Por ejemplo:
-- Tabla para las clases principales
CREATE TABLE Clases (
    id INT AUTO_INCREMENT PRIMARY KEY,
    nombre VARCHAR(255) NOT NULL
);

-- Tabla para subclases
CREATE TABLE Subclases (
    id INT AUTO_INCREMENT PRIMARY KEY,
    clase_id INT,
    nombre VARCHAR(255),
    FOREIGN KEY (clase_id) REFERENCES Clases(id)
);

-- Tabla para relaciones
CREATE TABLE Relaciones (
    id INT AUTO_INCREMENT PRIMARY KEY,
    nombre VARCHAR(255) NOT NULL,
    descripcion TEXT,
    dominio VARCHAR(255),
    rango VARCHAR(255)
);

-- Tabla para instancias
CREATE TABLE Instancias (
    id INT AUTO_INCREMENT PRIMARY KEY,
    nombre VARCHAR(255),
    tipo VARCHAR(255),
    subtipo VARCHAR(255),
    descripcion TEXT
);

-- Tabla para relaciones de instancias
CREATE TABLE RelacionInstancias (
    id INT AUTO_INCREMENT PRIMARY KEY,
    instancia_id INT,
    relacion VARCHAR(255),
    objetivo VARCHAR(255),
    FOREIGN KEY (instancia_id) REFERENCES Instancias(id)
);

# 3. Convertir el JSON a un Formato SQL Compatible
El archivo JSON debe transformarse en sentencias INSERT que se puedan ejecutar en MySQL. Puedes usar una herramienta de transformación o un script en Python. Aquí tienes un ejemplo básico en Python:

import json
import pymysql
#Cargar el archivo JSON
with open("lenguaje-programacion-onto-y-instancias.json", "r") as file:
    data = json.load(file)

#Conectar a MySQL
connection = pymysql.connect(
    host="localhost",
    user="tu_usuario",
    password="tu_contraseña",
    database="pruebasOntologia"
)
cursor = connection.cursor()

#Insertar clases principales
for clase in data["Ontologia"]["Clases"]:
    cursor.execute("INSERT INTO Clases (nombre) VALUES (%s)", (clase["nombre"],))
    clase_id = cursor.lastrowid
    # Insertar subclases
    if "subclases" in clase:
        for subclase in clase["subclases"]:
            if isinstance(subclase, dict):
                cursor.execute("INSERT INTO Subclases (clase_id, nombre) VALUES (%s, %s)", (clase_id, subclase["nombre"]))
            else:
                cursor.execute("INSERT INTO Subclases (clase_id, nombre) VALUES (%s, %s)", (clase_id, subclase))

#Insertar relaciones
for relacion in data["Ontologia"]["Relaciones"]:
    cursor.execute(
        "INSERT INTO Relaciones (nombre, descripcion, dominio, rango) VALUES (%s, %s, %s, %s)",
        (relacion["nombre"], relacion["descripcion"], relacion["dominio"], relacion["rango"])
    )

#Insertar instancias
for instancia in data["Ontologia"]["Instancias"]:
    cursor.execute(
        "INSERT INTO Instancias (nombre, tipo, subtipo, descripcion) VALUES (%s, %s, %s, %s)",
        (instancia["nombre"], instancia.get("tipo", ""), instancia.get("subtipo", ""), instancia.get("descripcion", ""))
    )
    instancia_id = cursor.lastrowid
    # Insertar relaciones de instancias
    if "relaciones" in instancia:
        for relacion, objetivo in instancia["relaciones"].items():
            cursor.execute(
                "INSERT INTO RelacionInstancias (instancia_id, relacion, objetivo) VALUES (%s, %s, %s)",
                (instancia_id, relacion, objetivo)
            )

#Confirmar cambios y cerrar conexión
connection.commit()
cursor.close()
connection.close()

# 4. Ejecutar el Script

Guarda el script como importar_ontologia.py y ejecútalo con Python.

python importar_ontologia.py
# 5. Verificar los Datos

Una vez importados, verifica que los datos estén correctamente en la base de datos ejecutando consultas simples en MySQL, por ejemplo:

SELECT * FROM Clases;
SELECT * FROM Instancias;

</details>


Como lo que quiero es importar directamente un archivo .sql a MySQL y que me genere la BD y además las tablas, sin tener que poner yo los comandos, pido a ChatGPT:
“¿Puedes añadirme la creación de la base de datos, la definición de tablas a un archivo .sql que pueda importar directamente en MySQL?” y me devuelve el archivo .sql con el contenido: archivo .sql.

## Creación de actividades
En clase uso Quizizz para darle dinamismo a la clase y que repasen contenidos de un tema impartido, creo que si tengo un json de con las ontologías de lenguajes de programación podría hacer 10 preguntas tipo test preguntándole a ChatGPT:
```
Crear un archivo importable en Quizizz con 10 preguntas y 4 respuestas, de las cuales solo 1 es correcta que use como base el archivo .json con las ontologías que te he pasado
```
Me devuelve las intrucciones para poder generar un archivo Excel (.xlsx) y le pido:
```
Creame un script de Python que me permita descargar el Excel
```



```python
import pandas as pd
print("hola")

# Crear las preguntas
questions = [
    {
        "Question": "¿Cuál es un paradigma de programación funcional puro?",
        "Option 1": "Imperativo",
        "Option 2": "Funcional puro",
        "Option 3": "Procedural",
        "Option 4": "Orientado a objetos",
        "Correct Answer": "Funcional puro",
    },
    {
        "Question": "¿Qué nivel de abstracción tiene el lenguaje C?",
        "Option 1": "Bajo nivel",
        "Option 2": "Medio nivel",
        "Option 3": "Alto nivel",
        "Option 4": "Específico de plataforma",
        "Correct Answer": "Medio nivel",
    },
    {
        "Question": "¿Cuál es un ejemplo de un lenguaje interpretado?",
        "Option 1": "C",
        "Option 2": "Python",
        "Option 3": "Java",
        "Option 4": "C++",
        "Correct Answer": "Python",
    },
    {
        "Question": "¿Qué algoritmo pertenece a la categoría de procesamiento de texto?",
        "Option 1": "Quicksort",
        "Option 2": "Algoritmo de Dijkstra",
        "Option 3": "Algoritmo de KMP",
        "Option 4": "Mergesort",
        "Correct Answer": "Algoritmo de KMP",
    },
    {
        "Question": "¿Qué algoritmo resuelve problemas de grafos?",
        "Option 1": "Algoritmo de Kruskal",
        "Option 2": "Búsqueda lineal",
        "Option 3": "Quicksort",
        "Option 4": "Heapsort",
        "Correct Answer": "Algoritmo de Kruskal",
    },
    {
        "Question": "¿Qué lenguaje se clasifica como multiparadigma?",
        "Option 1": "Java",
        "Option 2": "Python",
        "Option 3": "C",
        "Option 4": "Prolog",
        "Correct Answer": "Python",
    },
    {
        "Question": "¿Cuál de los siguientes algoritmos es de búsqueda?",
        "Option 1": "Quicksort",
        "Option 2": "Búsqueda binaria",
        "Option 3": "Mergesort",
        "Option 4": "Algoritmo de Prim",
        "Correct Answer": "Búsqueda binaria",
    },
    {
        "Question": "¿Qué paradigma pertenece exclusivamente a Prolog?",
        "Option 1": "Lógico",
        "Option 2": "Funcional",
        "Option 3": "Orientado a objetos",
        "Option 4": "Procedural",
        "Correct Answer": "Lógico",
    },
    {
        "Question": "¿Qué lenguaje utiliza bytecode como formato de compilación?",
        "Option 1": "Java",
        "Option 2": "Python",
        "Option 3": "C",
        "Option 4": "Swift",
        "Correct Answer": "Java",
    },
    {
        "Question": "¿Cuál es un uso típico de los algoritmos genéticos?",
        "Option 1": "Ordenamiento",
        "Option 2": "Procesamiento de texto",
        "Option 3": "Optimización",
        "Option 4": "Búsqueda",
        "Correct Answer": "Optimización",
    },
]

# Crear el DataFrame
df = pd.DataFrame(questions)

# Guardar en un archivo Excel
df.to_excel("quizizz_questions.xlsx", index=False)

```

    hola

