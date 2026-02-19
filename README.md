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





-----------------------------------------------------------------------------------------------------





# 📝 Semana 1 - Entrenamiento 3: DML y Manipulación de Datos
**Clan:** Hamilton  
**Tema:** Data Manipulation Language (CRUD)  
**Duración:** 2 - 2.5 Horas

---

## 🎯 Objetivo de la Sesión
Ya tenemos la estructura (las tablas). Ahora aprenderemos a llenarlas de información, consultarlas, corregirlas y eliminarlas. Dominaremos el **CRUD** usando SQL Estándar.

---

## 1. ¿Qué es DML?
**DML (Data Manipulation Language)** son los verbos que usamos para interactuar con los datos **dentro** de las tablas.

| Letra CRUD | Comando SQL | Descripción |
| :--- | :--- | :--- |
| **C**reate | `INSERT` | Agrega nuevas filas. |
| **R**ead | `SELECT` | Consulta y busca información (El más usado). |
| **U**pdate | `UPDATE` | Modifica datos existentes. |
| **D**elete | `DELETE` | Borra filas. |

---

## 2. INSERT: Agregando Datos
Es importante respetar el orden de las columnas y los tipos de datos.

### Sintaxis Básica
```sql
INSERT INTO nombre_tabla (columna1, columna2) 
VALUES (valor1, valor2);
```

### Ejemplos Prácticos
Supongamos que tenemos la tabla `videojuegos` creada ayer.

```sql
-- Insertar un solo registro
INSERT INTO videojuegos (titulo, genero, precio, id_desarrollador)
VALUES ('Super Mario Odyssey', 'Plataforma', 59.99, 1);

-- Insertar múltiples registros (Batch Insert) - ¡Más eficiente!
INSERT INTO videojuegos (titulo, genero, precio, id_desarrollador)
VALUES 
    ('Zelda: BOTW', 'Aventura', 59.99, 1),
    ('Halo Infinite', 'Shooter', 40.00, 2),
    ('God of War', 'Accion', 35.50, 3);
```

> **⚠️ Ojo:** Las cadenas de texto (Strings) SIEMPRE van entre comillas simples `'Texto'`. Las comillas dobles `"` se usan para nombres de tablas o columnas (a veces), pero para datos: comillas simples.

---

## 3. SELECT: Consultando Datos (El Arte de Preguntar)
El comando más potente de SQL.

### Lo Básico
```sql
-- Traer TODAS las columnas (No recomendado en producción por rendimiento)
SELECT * FROM videojuegos;

-- Traer solo columnas específicas (Buena práctica)
SELECT titulo, precio FROM videojuegos;
```

### El Filtro Mágico: `WHERE`
Es como el filtro de Excel. Nos permite traer solo lo que cumpla una condición.

```sql
-- Juegos baratos (menores a 40 dolares)
SELECT * FROM videojuegos WHERE precio < 40;

-- Juegos de un género específico
SELECT * FROM videojuegos WHERE genero = 'Aventura';
```

### Operadores Lógicos
| Operador | Significado | Ejemplo |
| :--- | :--- | :--- |
| `=` | Igual a | `genero = 'Accion'` |
| `< >` o `!=` | Diferente de | `genero != 'RPG'` |
| `>` / `<` | Mayor / Menor que | `precio > 100` |
| `AND` | Y (Ambas se cumplen) | `precio > 20 AND genero = 'Shooter'` |
| `OR` | O (Alguna se cumple) | `genero = 'RPG' OR genero = 'Aventura'` |
| `LIKE` | Búsqueda parcial | `titulo LIKE 'Super%'` (Empieza por Super) |

---

## 4. UPDATE: Actualizando Datos (¡PELIGRO! 💀)
Permite corregir información.

### Sintaxis
```sql
UPDATE nombre_tabla 
SET columna = nuevo_valor 
WHERE condicion;
```

### El error del Junior 🤡
```sql
-- ¡ERROR GRAVE! (Olvidé el WHERE)
UPDATE videojuegos SET precio = 0; 
-- Resultado: TODOS los juegos ahora son gratis. Tu jefe te despide.

-- FORMA CORRECTA
UPDATE videojuegos 
SET precio = 25.00 
WHERE titulo = 'God of War';
```

---

## 5. DELETE: Borrando Datos (¡MÁS PELIGRO! 💀💀)
Elimina registros físicos de la base de datos.

### Sintaxis
```sql
DELETE FROM nombre_tabla 
WHERE condicion;
```

### La advertencia
Igual que el UPDATE, si olvidas el `WHERE`, borras **toda** la tabla.
```sql
-- Borrar solo un juego específico
DELETE FROM videojuegos 
WHERE id = 5;
```

---

## 🛠️ Reto Práctico: "Poblando Hamilton"
**Contexto:**
Ayer creaste las tablas `mentores`, `clanes` y `coders`. Hoy vamos a llenarlas de vida.

**Instrucciones:**
Escribe un script SQL que haga lo siguiente en orden estricto (recuerda las llaves foráneas):

1.  **INSERT:**
    * Agrega 2 Mentores (Ej: 'Juan Pérez' de 'Java', 'Maria Gomez' de 'Python').
    * Agrega 2 Clanes (Ej: 'Hamilton' asignado a Maria, 'Lovelace' asignado a Juan).
    * Agrega 5 Coders (3 para Hamilton, 2 para Lovelace). Inventa sus datos.
2.  **UPDATE:**
    * El Coder con ID 1 se cambió de nombre a "Coder Editado". Actualízalo.
    * El Clan 'Lovelace' se mudó al salón "B202". Actualízalo.
3.  **DELETE:**
    * El Coder con ID 3 decidió abandonar el curso. Elimínalo de la base de datos.
4.  **SELECT:**
    * Muestra todos los Coders que pertenecen al Clan 1 (Hamilton).

**Entregable:**
Archivo `.sql` con todas las sentencias ejecutadas y comentadas.
