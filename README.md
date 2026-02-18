# entregables_BD

# 🏗️ Semana 1 - Entrenamiento 2: DDL y Creación de Tablas
**Clan:** Hamilton  
**Tema:** Data Definition Language (DDL) - SQL Nativo  
**Duración:** 2 - 2.5 Horas

---

## 🎯 Objetivo de la Sesión
Pasar del papel (Diagrama MER) al código. Aprenderemos a escribir las sentencias SQL para crear tablas, definir tipos de datos y, lo más importante, **aplicar restricciones (constraints)** para proteger la integridad de la información.

---

## 1. ¿Qué es SQL? (Breve Contexto)
SQL (Structured Query Language) es el idioma estándar para hablar con las bases de datos. Se divide en varios "dialectos". Hoy veremos **DDL**.

### 🏗️ DDL (Data Definition Language)
Son los comandos que definen la **estructura** de la base de datos, no los datos en sí.
* `CREATE`: Para crear objetos (tablas, bases de datos, vistas).
* `ALTER`: Para modificar estructuras existentes (agregar columnas).
* `DROP`: Para eliminar objetos (¡Cuidado con este!).

---

## 2. La Sentencia `CREATE TABLE`
Es el comando fundamental. Traduce una "Clase" o "Entidad" del diagrama a una Tabla real.

### Sintaxis Básica
```sql
CREATE TABLE nombre_tabla (
    nombre_columna1 TIPO_DATO RESTRICCIONES,
    nombre_columna2 TIPO_DATO RESTRICCIONES,
    ...
);
```

---

## 3. Tipos de Datos (Los Materiales)
Aunque varían levemente entre motores (Postgres vs MySQL vs SQLite), estos son los estándares que usarán el 99% del tiempo:

| Tipo | Descripción | Ejemplo |
| :--- | :--- | :--- |
| `INTEGER` / `INT` | Números enteros. (Sin decimales). | Edad, Stock, ID, Año. |
| `VARCHAR(n)` | Texto variable. `n` es el límite de caracteres. Ahorra espacio. | `VARCHAR(100)` para un email. |
| `TEXT` | Texto largo sin límite específico. | Comentarios, Descripciones. |
| `BOOLEAN` | Verdadero o Falso. | `true`, `false`, `1`, `0`. |
| `DECIMAL(m, d)` | Números exactos con decimales. | Precios, Salarios. (`10.50`). |
| `DATE` | Fechas (Año-Mes-Día). | `'2024-12-31'`. |
| `TIMESTAMP` | Fecha y Hora exacta. | `'2024-12-31 14:30:00'`. |

---

## 4. Las Restricciones (Constraints) - Las Reglas del Juego
Aquí es donde la base de datos se vuelve inteligente. Las restricciones impiden que entre "basura" al sistema.

1.  **`PRIMARY KEY` (PK):**
    * Identifica de forma única a cada fila.
    * Implica que el dato es `UNIQUE` y `NOT NULL`.
    * *Tip:* Usualmente es autoincremental (`SERIAL` en Postgres, `AUTOINCREMENT` en SQLite).
2.  **`NOT NULL`:**
    * Prohíbe dejar el campo vacío. (Ej: Un usuario *debe* tener email).
3.  **`UNIQUE`:**
    * Prohíbe valores repetidos en toda la columna. (Ej: Dos usuarios no pueden tener el mismo email).
4.  **`DEFAULT`:**
    * Si no envías el dato al insertar, la base de datos pone uno por defecto. (Ej: `activo` por defecto es `true`).
5.  **`CHECK`:**
    * Valida una condición lógica antes de guardar. (Ej: `CHECK (precio > 0)`).
6.  **`FOREIGN KEY` (FK):**
    * La regla de oro de la integridad referencial. Asegura que el dato que pongas aquí *exista* en la otra tabla.

---

## 5. Práctica Guiada: "Base de Datos Arcade"
*Vamos a crear la estructura para un salón de videojuegos paso a paso.*

### Paso 1: Crear la tabla independiente (Sin FK)
Primero creamos las tablas que no dependen de nadie (Tablas Padre).

```sql
-- Tabla de Desarrolladores (Ej: Nintendo, Sega)
CREATE TABLE desarrolladores (
    id INTEGER PRIMARY KEY,
    nombre VARCHAR(50) NOT NULL,
    pais VARCHAR(30)
);
```

### Paso 2: Crear la tabla dependiente (Con FK)
Ahora creamos los Juegos, que pertenecen a un Desarrollador (Tablas Hija).

```sql
-- Tabla de Videojuegos
CREATE TABLE videojuegos (
    id INTEGER PRIMARY KEY,
    titulo VARCHAR(100) NOT NULL UNIQUE, -- No queremos juegos repetidos
    genero VARCHAR(50),
    lanzamiento DATE,
    precio DECIMAL(10, 2) CHECK (precio >= 0), -- El precio no puede ser negativo
    id_desarrollador INTEGER, -- Primero creamos la columna vacía
    
    -- Luego definimos la relación (El puente)
    FOREIGN KEY (id_desarrollador) REFERENCES desarrolladores(id)
);
```

> **Nota:** La línea `FOREIGN KEY` dice: *"La columna `id_desarrollador` de esta tabla apunta a la columna `id` de la tabla `desarrolladores`"*. Si intentas crear un juego para un desarrollador que no existe (ej: ID 999), la base de datos dará error.

---

## 6. Modificando y Borrando (`ALTER` y `DROP`)
¿Te equivocaste? No pasa nada (mientras no sea en producción 😅).

* **Borrar una tabla completa:**
    ```sql
    DROP TABLE videojuegos;
    ```
    *(¡Ojo! Esto borra la estructura y TODOS los datos dentro para siempre).*

* **Agregar una columna olvidada:**
    ```sql
    ALTER TABLE videojuegos ADD COLUMN es_multijugador BOOLEAN DEFAULT false;
    ```

---

## 🛠️ Reto Práctico: "Sistema de Gestión de Clanes"
**Contexto:**
Como Team Leader, necesitas una base de datos para gestionar Hamilton. Tienes 3 entidades: Mentores, Clanes y Coders.

**Instrucciones:**
Escribe el código SQL para crear las siguientes 3 tablas con sus respectivas reglas.

### 1. Tabla `mentores`
* `id`: Entero, Llave Primaria.
* `nombre`: Texto, Obligatorio.
* `especialidad`: Texto (Ej: 'Java', 'Python').

### 2. Tabla `clanes`
* `id`: Entero, Llave Primaria.
* `nombre`: Texto, Obligatorio y Único (No puede haber dos clanes llamados 'Hamilton').
* `salon`: Texto (Ej: 'Apolo', 'Artemis').
* `id_mentor`: Entero (Llave Foránea que conecta con Mentores).

### 3. Tabla `coders`
* `id`: Entero, Llave Primaria.
* `nombre`: Texto, Obligatorio.
* `documento`: Texto, Obligatorio y Único.
* `fecha_ingreso`: Fecha.
* `id_clan`: Entero (Llave Foránea que conecta con Clanes).

**Entregable:**
Un archivo `.sql` (o bloque de texto) con las 3 sentencias `CREATE TABLE` en el orden correcto.
*(Pregunta trampa: ¿Qué tabla debes crear primero? ¿Coders o Clanes?)*
