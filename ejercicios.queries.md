¡Claro! 😄 Te puedo preparar ejercicios de consultas SQL como los que suelen poner en exámenes o prácticas, empezando por los más sencillos y subiendo la dificultad poco a poco.

Vamos a usar una base de datos de una tienda, con clientes, productos y pedidos, para practicar situaciones reales.

# 1. Base de datos para los ejercicios

Imagina que tienes estas tres tablas:

### Tabla: Clientes

| id_cliente | nombre | ciudad   |
| ---------- | ------ | -------- |
| 1          | Ana    | Madrid   |
| 2          | Luis   | Sevilla  |
| 3          | Carlos | Madrid   |
| 4          | María  | Valencia |

### Tabla: Productos

| id_producto | nombre   | precio |
| ----------- | -------- | ------ |
| 1           | Portátil | 800    |
| 2           | Ratón    | 20     |
| 3           | Teclado  | 50     |
| 4           | Monitor  | 200    |

### Tabla: Pedidos

| id_pedido | id_cliente | id_producto | cantidad |
| --------- | ---------- | ----------- | -------- |
| 1         | 1          | 1           | 1        |
| 2         | 2          | 2           | 3        |
| 3         | 1          | 3           | 2        |
| 4         | 3          | 4           | 1        |
| 5         | 4          | 2           | 2        |

# 2. Ejercicios de SQL

Intenta resolverlos sin mirar las soluciones. Están ordenados por dificultad.

## Nivel 1: Consultas básicas

Practicarás `SELECT`, `WHERE`, `ORDER BY` y `DISTINCT`.

Ejercicio 1. Mostrar todos los clientes.

Pista: `SELECT` y `FROM`.

Copiar solución de referencia Solución: `SELECT * FROM Clientes;`

Ejercicio 2. Mostrar el nombre de los clientes que viven en Madrid.

Pista: utiliza `WHERE`.

Ejercicio 3. Mostrar los productos cuyo precio sea superior a 100.

Pista: `WHERE precio > 100`.

Ejercicio 4. Mostrar todos los productos ordenados por precio de mayor a menor.

Pista: `ORDER BY ... DESC`.

Ejercicio 5. Mostrar las ciudades diferentes en las que viven los clientes.

Pista: `DISTINCT`.

## Nivel 2: Funciones de agrupación

Practicarás `COUNT`, `SUM`, `AVG`, `GROUP BY` y `HAVING`.

Ejercicio 6. Contar cuántos clientes hay en total.

Pista: `COUNT(*)`.

Ejercicio 7. Calcular el precio medio de todos los productos.

Pista: `AVG(precio)`.

Ejercicio 8. Mostrar cuántos pedidos ha realizado cada cliente.

Pista: agrupa por `id_cliente` con `GROUP BY`.

Ejercicio 9. Mostrar los clientes que han realizado más de un pedido.

Pista: necesitas `GROUP BY` y `HAVING`.

Ejercicio 10. Calcular el importe total vendido de cada producto, teniendo en cuenta su precio y la cantidad vendida.

Pista: relaciona `Productos` con `Pedidos` y utiliza `SUM(precio * cantidad)`.

## Nivel 3: Consultas con JOIN

Aquí tendrás que combinar varias tablas.

Ejercicio 11. Mostrar el nombre de cada cliente junto con el identificador de sus pedidos.

Pista: `Clientes` e `INNER JOIN Pedidos`.

Ejercicio 12. Mostrar el nombre de cada cliente y el nombre de los productos que ha comprado.

Pista: necesitas relacionar las tres tablas.

Ejercicio 13. Mostrar los clientes que han comprado un portátil.

Pista: combina las tres tablas y filtra por el nombre del producto.

Ejercicio 14. Mostrar todos los clientes, incluidos los que nunca han realizado pedidos.

Pista: utiliza `LEFT JOIN`.

## Nivel 4: Retos de examen

Ejercicio 15. Mostrar el nombre de los clientes que han comprado productos cuyo importe total supera los 100 €.

Pista: `JOIN`, `GROUP BY` y `HAVING`.

Ejercicio 16. Encontrar el producto más caro.

Pista: `MAX(precio)` o una subconsulta.

Ejercicio 17. Mostrar los clientes que nunca han comprado ningún producto.

Pista: `LEFT JOIN` y `IS NULL`.

Ejercicio 18. Mostrar el producto más vendido según la cantidad total de unidades.

Pista: `SUM(cantidad)`, `GROUP BY` y `ORDER BY`.

# 3\. ¿Cómo quieres practicar?

Puedo adaptarte los ejercicios al tipo de consultas que te van a pedir en clase.

¿Qué nivel tienes?

Principiante: SELECT, WHERE, ORDER BY

intermedio Intermedio: GROUP BY, JOIN, HAVING

Avanzado: subconsultas, EXISTS, consultas complejas

¿Qué tipo de ejercicios necesitas?

mezclados Un poco de todo

Solo JOIN entre tablas

GROUP BY, COUNT, SUM y HAVING

Subconsultas y consultas anidadas

¿Quieres las soluciones?

despues Primero los ejercicios y después las soluciones

Ejercicios con pistas, sin soluciones

Ejercicios resueltos y explicados paso a paso

Generar mis ejercicios

Si tienes una foto o un documento con ejemplos de los ejercicios que os ponen en clase, también puedes enviármelo y te preparo otros muy parecidos.

---

---

---

Para practicar consultas SQL en tu Mac sin conexión a Internet, te recomiendo SQLite o PostgreSQL, dependiendo de lo que estés estudiando. Para empezar con los ejercicios que te preparé, yo elegiría SQLite si quieres algo sencillo, o PostgreSQL si necesitas practicar consultas más completas.

## Mis 3 recomendaciones

Using llama-cookbook “Text2SQL” and really impressed! | by Alain Airom (Ayrom) | Medium

### 1\. SQLite \+ DB Browser for SQLite

Mi recomendación para empezar

- Funciona completamente sin Internet.
- No necesitas configurar un servidor.
- Puedes crear tablas, insertar datos y practicar `SELECT`, `WHERE`, `JOIN`, `GROUP BY` y subconsultas.
- Es gratuito y ligero.

Descarga: [DB Browser for SQLite](https://sqlitebrowser.org/)

Digital Marketing Agency in Lucknow | Deeplink Creators | Deeplink Creators

### 2\. PostgreSQL \+ DBeaver Community

Ideal si quieres aprender SQL de forma más completa.

- Funciona sin Internet una vez instalado.
- Permite practicar consultas complejas, funciones, vistas, restricciones y transacciones.
- DBeaver te ofrece una interfaz gráfica para ejecutar tus consultas.

Descargas: [PostgreSQL](https://www.postgresql.org/download/macosx/) · [DBeaver](https://dbeaver.io/download/)

Kanbel Technologies

### 3\. MySQL

Buena opción si en tu curso utilizan específicamente MySQL.

- Puedes trabajar localmente sin conexión.
- Es muy utilizado en desarrollo web.
- Puedes conectarlo a DBeaver para ejecutar consultas.

Descarga: [MySQL para macOS](https://dev.mysql.com/downloads/mysql/)

## ¿Cuál elegiría para tus ejercicios?

| Si necesitas...                                      | Te recomiendo |
| ---------------------------------------------------- | ------------- |
| Practicar SQL de forma rápida y sencilla             | SQLite        |
| Aprender SQL para exámenes y proyectos más completos | PostgreSQL    |
| Seguir las clases de un curso que usa MySQL          | MySQL         |

Importante: los tres funcionan sin conexión después de instalarlos. Solo necesitarás Internet para descargar el programa inicialmente, salvo que ya tengas el instalador.

## Te ayudo a elegir según tu Mac

1\. ¿Qué procesador tiene tu Mac?

m Apple Silicon (M1, M2, M3, M4 o M5)

Intel

No lo sé

2\. ¿Qué motor usan en tu curso?

mysql MySQL

PostgreSQL

SQLite

No lo sé / solo nos piden SQL

3\. ¿Cómo prefieres trabajar?

Con ventanas y botones, sin usar la terminal

Con la terminal

ambos Me da igual

Recomendarme la mejor opción

---

---

---

Te recomiendo instalar MySQL Community Server + MySQL Workbench en tu Mac con Apple Silicon (M1, M2, M3, M4 o M5).

Con esta combinación podrás practicar tus ejercicios de SQL sin conexión a Internet, tanto desde una interfaz gráfica como desde la terminal. Además, utilizarás el mismo motor que en tu curso.

Top 5 MySQL GUI Clients in 2026 | Bytebase

MySQL Workbench

Interfaz gráfica

Podrás escribir consultas, crear tablas, ver resultados y administrar tu base de datos sin memorizar comandos de terminal.

[Descargar MySQL Workbench](https://dev.mysql.com/downloads/workbench/)

File:Antu mysql-workbench.svg - Wikimedia Commons

MySQL Community Server

Motor de base de datos

Es el servicio local que almacena tus tablas y ejecuta las consultas SQL.

[Descargar MySQL Server](https://dev.mysql.com/downloads/mysql/)

## 1\. Descarga los instaladores

Haz estos pasos mientras tengas Internet. Después podrás desconectarte y trabajar normalmente.

1. Abre la página oficial de [MySQL Community Server](https://dev.mysql.com/downloads/mysql/).
2. Selecciona la versión 8.4 LTS si tu curso no exige una versión específica.
3. Selecciona macOS y el paquete ARM de 64 bits (`arm64`), no el paquete `x86_64`, que es para Intel. La página oficial ofrece instaladores específicos para Apple Silicon. MySQL Developer Zone
   +1
4. Descarga también [MySQL Workbench](https://dev.mysql.com/downloads/workbench/), seleccionando el paquete para macOS ARM de 64 bits si está disponible y es compatible con tu versión de macOS. MySQL Developer Zone
   +1

## 2\. Instala MySQL Server

MAC MySQL 安装与基础配置 | Hehehai @一块木头

Paso 1. Abre el archivo DMG

Entra en Descargas, abre el archivo que bajaste y ejecuta el instalador `.pkg`.

Chapter 2 : MySQL Installation - Database Basic

Paso 2. Configura la contraseña

Sigue el asistente. Cuando te lo solicite, establece una contraseña para el usuario `root` y guárdala en un lugar seguro.

MySQL :: MySQL 9.3 Reference Manual :: 2.4.3 Installing and Using the MySQL Launch Daemon

Paso 3. Inicia el servidor

Si aparece la opción de iniciar MySQL al finalizar la instalación, actívala. Si no, podrás iniciarlo desde el panel de MySQL de macOS, cuando esté disponible.

El instalador oficial para macOS incluye opciones de configuración del servidor, contraseña y arranque automático. MySQL Developer Zone

+1

## 3\. Instala MySQL Workbench

1. Abre el DMG de Workbench.
2. Arrastra el icono de MySQL Workbench a la carpeta `Applications` (Aplicaciones).
3. Abre Workbench desde Aplicaciones.

Este es el procedimiento indicado en la documentación oficial. MySQL Developer Zone

## 4\. Conéctate a tu base de datos

Abre Workbench y crea una conexión local con estos valores:

| Campo           | Valor                          |
| --------------- | ------------------------------ |
| Connection Name | `MySQL Local`                  |
| Hostname        | `127.0.0.1`                    |
| Port            | `3306`                         |
| Username        | `root`                         |
| Password        | La contraseña que configuraste |

Pulsa Test Connection. Si la conexión funciona, guarda y abre la conexión.

## 5\. Crea una base de datos para practicar

En Workbench, abre una pestaña de consulta SQL y ejecuta este código:

sql

```sql
CREATE DATABASE practicas_sql;

USE practicas_sql;

CREATE TABLE alumnos (
    id INT PRIMARY KEY AUTO_INCREMENT,
    nombre VARCHAR(50),
    edad INT,
    ciudad VARCHAR(50)
);

INSERT INTO alumnos (nombre, edad, ciudad)
VALUES
    ('Ana', 20, 'Madrid'),
    ('Luis', 22, 'Sevilla'),
    ('Carlos', 19, 'Madrid'),
    ('Maria', 24, 'Valencia');

SELECT * FROM alumnos;
```

Pulsa el icono del rayo para ejecutar las consultas. Deberías ver los cuatro alumnos en la tabla de resultados.

Ahora prueba una consulta típica de examen:

sql

```sql
SELECT nombre, edad
FROM alumnos
WHERE ciudad = 'Madrid'
ORDER BY edad DESC;
```

Esto te permitirá practicar `SELECT`, `WHERE` y `ORDER BY` con datos reales.

## 6\. Ejecuta SQL también desde la terminal

Abre Terminal en tu Mac y escribe:

bash

```bash
mysql -u root -p
```

Introduce la contraseña que configuraste. Una vez dentro, prueba:

sql

```sql
SHOW DATABASES;
USE practicas_sql;
SELECT * FROM alumnos;
```

Para salir:

sql

```sql
exit;
```

Si el comando `mysql` no se reconoce, puede que la ruta del ejecutable no esté añadida al `PATH`; puedes usar Workbench mientras configuras eso.

## 7\. ¿Cómo trabajar sin Internet?

- Descarga los instaladores antes de desconectarte.
- Una vez instalado, MySQL Server y Workbench funcionan localmente, sin necesitar Internet.
- Tus bases de datos se guardan en el disco de tu Mac.
- Para conectarte, el servidor MySQL debe estar iniciado; no hace falta que estés conectado a una red externa.

Mi consejo: instala MySQL Server 8.4 LTS y Workbench, y empieza practicando con una base de datos llamada `practicas_sql`. Así podrás trabajar desde la interfaz gráfica y la terminal sin depender de servicios en la nube.

Si durante la instalación aparece un error, envíame una captura de pantalla y te guío paso a paso.

---

---

---
