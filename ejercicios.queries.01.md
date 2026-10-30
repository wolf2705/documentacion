# Prácticas de SQL con MySQL: ejercicios, ejemplos y script de datos

Te propongo un material de prácticas de SQL desde nivel básico hasta intermedio, con una base de datos de ejemplo, ejercicios para resolver, consultas de referencia y guías paso a paso.

Utilizaremos una base de datos de ventas de una tienda, porque permite practicar `SELECT`, `ORDER BY`, `GROUP BY`, `JOIN`, subconsultas y funciones de agregación con datos relacionados entre sí.

## 1. ¿Qué vas a practicar?

Nivel 1. Consultas básicas

`SELECT`, `WHERE`, `DISTINCT`, `ORDER BY`, `LIMIT`.

Consultar productos, filtrar precios y ordenar resultados.

Nivel 2. Funciones de agregación

`COUNT()`, `SUM()`, `AVG()`, `MIN()`, `MAX()`.

Contar ventas, calcular ingresos y obtener promedios.

Nivel 3. Agrupaciones

`GROUP BY`, `HAVING`.

Calcular ventas por cliente, categoría o vendedor.

Nivel 4. Consultas relacionadas

`INNER JOIN`, `LEFT JOIN`, subconsultas.

Relacionar clientes, pedidos, productos y detalles de venta.

## 2. Estructura de la base de datos

La base tendrá cinco tablas relacionadas:

- `clientes`: información de los clientes.
- `categorias`: clasificación de los productos.
- `productos`: nombre, precio y categoría.
- `pedidos`: fecha, cliente y estado de cada pedido.
- `detalle_pedido`: productos, cantidades y precios de cada pedido.

clientes

id_cliente · nombre · ciudad

1 a muchos

pedidos

id_pedido · id_cliente · fecha · estado

1 a muchos

detalle_pedido

id_pedido · id_producto · cantidad · precio_unitario

Cada detalle corresponde a un producto

productos

id_producto · nombre · precio · id_categoria

categorias

id_categoria · nombre

Los datos incluirán diferentes ciudades, categorías, precios, cantidades y estados de pedidos para que puedas practicar consultas con resultados variados.

## 3. Script SQL completo para crear y cargar la base de datos

Copia el siguiente script en MySQL Workbench o en el editor SQL que utilices. Está pensado para MySQL 8.0 o superior.

El script crea la base de datos `practicas_sql`, sus cinco tablas y datos de ejemplo. Puedes ejecutarlo completo desde el principio.

Importante: si ya existe una base de datos con el nombre `practicas_sql`, el script la elimina y la vuelve a crear. No lo ejecutes sobre una base que contenga datos que quieras conservar.

sql

```sql

-- =====================================================
-- BASE DE DATOS PARA PRACTICAS DE SQL
-- Compatible con MySQL 8.0+
-- =====================================================

DROP DATABASE IF EXISTS practicas_sql;
CREATE DATABASE practicas_sql
    CHARACTER SET utf8mb4
    COLLATE utf8mb4_unicode_ci;

USE practicas_sql;

-- =====================================================
-- 1. TABLA CLIENTES
-- =====================================================

CREATE TABLE clientes (
    id_cliente INT AUTO_INCREMENT PRIMARY KEY,
    nombre VARCHAR(100) NOT NULL,
    ciudad VARCHAR(60) NOT NULL,
    email VARCHAR(120) UNIQUE,
    fecha_registro DATE NOT NULL
);

INSERT INTO clientes
(nombre, ciudad, email, fecha_registro)
VALUES
('Ana Lopez', 'CDMX', 'ana@email.com', '2025-01-10'),
('Carlos Perez', 'Guadalajara', 'carlos@email.com', '2025-01-15'),
('Maria Garcia', 'Monterrey', 'maria@email.com', '2025-02-01'),
('Luis Hernandez', 'CDMX', 'luis@email.com', '2025-02-12'),
('Sofia Martinez', 'Puebla', 'sofia@email.com', '2025-03-05'),
('Jorge Ramirez', 'Guadalajara', 'jorge@email.com', '2025-03-18'),
('Elena Torres', 'Merida', 'elena@email.com', '2025-04-02'),
('Pedro Sanchez', 'CDMX', 'pedro@email.com', '2025-04-20'),
('Laura Diaz', 'Monterrey', 'laura@email.com', '2025-05-11'),
('Diego Flores', 'Queretaro', 'diego@email.com', '2025-06-01');

-- =====================================================
-- 2. TABLA CATEGORIAS
-- =====================================================

CREATE TABLE categorias (
    id_categoria INT AUTO_INCREMENT PRIMARY KEY,
    nombre VARCHAR(60) NOT NULL UNIQUE
);

INSERT INTO categorias (nombre)
VALUES
('Computo'),
('Accesorios'),
('Telefonia'),
('Oficina'),
('Audio'),
('Videojuegos');

-- =====================================================
-- 3. TABLA PRODUCTOS
-- =====================================================

CREATE TABLE productos (
    id_producto INT AUTO_INCREMENT PRIMARY KEY,
    nombre VARCHAR(100) NOT NULL,
    precio DECIMAL(10,2) NOT NULL,
    stock INT NOT NULL DEFAULT 0,
    id_categoria INT NOT NULL,
    FOREIGN KEY (id_categoria)
        REFERENCES categorias(id_categoria),
    CHECK (precio >= 0),
    CHECK (stock >= 0)
);

INSERT INTO productos
(nombre, precio, stock, id_categoria)
VALUES
('Laptop Lenovo', 12500.00, 10, 1),
('Laptop HP', 14800.00, 7, 1),
('Monitor 24 pulgadas', 3200.00, 15, 1),
('Teclado mecanico', 950.00, 30, 2),
('Mouse inalambrico', 450.00, 50, 2),
('Disco SSD 1TB', 1800.00, 20, 2),
('Smartphone Samsung', 8900.00, 12, 3),
('Smartphone Xiaomi', 6200.00, 18, 3),
('Tablet Samsung', 5400.00, 8, 3),
('Silla ergonomica', 2800.00, 9, 4),
('Escritorio', 4100.00, 6, 4),
('Impresora laser', 3600.00, 11, 4),
('Audifonos Bluetooth', 1250.00, 25, 5),
('Bocina Bluetooth', 780.00, 22, 5),
('Consola videojuegos', 11500.00, 5, 6),
('Control videojuegos', 850.00, 16, 6),
('Webcam HD', 1100.00, 14, 2),
('Microfono USB', 1650.00, 13, 5);

-- =====================================================
-- 4. TABLA PEDIDOS
-- =====================================================

CREATE TABLE pedidos (
    id_pedido INT AUTO_INCREMENT PRIMARY KEY,
    id_cliente INT NOT NULL,
    fecha DATE NOT NULL,
    estado ENUM(
        'Pendiente',
        'Pagado',
        'Enviado',
        'Entregado',
        'Cancelado'
    ) NOT NULL DEFAULT 'Pendiente',
    FOREIGN KEY (id_cliente)
        REFERENCES clientes(id_cliente)
);

INSERT INTO pedidos
(id_cliente, fecha, estado)
VALUES
(1, '2025-06-02', 'Entregado'),
(2, '2025-06-03', 'Entregado'),
(3, '2025-06-04', 'Pagado'),
(1, '2025-06-08', 'Enviado'),
(4, '2025-06-10', 'Entregado'),
(5, '2025-06-12', 'Pendiente'),
(6, '2025-06-15', 'Entregado'),
(2, '2025-06-18', 'Cancelado'),
(7, '2025-06-20', 'Pagado'),
(8, '2025-06-22', 'Entregado'),
(3, '2025-06-25', 'Enviado'),
(9, '2025-07-01', 'Entregado'),
(10, '2025-07-03', 'Pendiente'),
(4, '2025-07-05', 'Pagado'),
(5, '2025-07-08', 'Entregado'),
(1, '2025-07-10', 'Entregado'),
(6, '2025-07-12', 'Cancelado'),
(8, '2025-07-15', 'Pagado'),
(2, '2025-07-18', 'Entregado'),
(9, '2025-07-20', 'Pendiente'),
(7, '2025-07-22', 'Entregado'),
(10, '2025-07-25', 'Pagado');

-- =====================================================
-- 5. TABLA DETALLE_PEDIDO
-- Cada fila representa un producto de un pedido.
-- El precio_unitario es el precio al momento de la venta.
-- =====================================================

CREATE TABLE detalle_pedido (
    id_detalle INT AUTO_INCREMENT PRIMARY KEY,
    id_pedido INT NOT NULL,
    id_producto INT NOT NULL,
    cantidad INT NOT NULL,
    precio_unitario DECIMAL(10,2) NOT NULL,
    FOREIGN KEY (id_pedido)
        REFERENCES pedidos(id_pedido),
    FOREIGN KEY (id_producto)
        REFERENCES productos(id_producto),
    CHECK (cantidad > 0),
    CHECK (precio_unitario >= 0),
    UNIQUE (id_pedido, id_producto)
);

INSERT INTO detalle_pedido
(id_pedido, id_producto, cantidad, precio_unitario)
VALUES
(1, 1, 1, 12500.00),
(1, 5, 2, 450.00),
(2, 4, 1, 950.00),
(2, 5, 2, 450.00),
(3, 7, 1, 8900.00),
(3, 13, 1, 1250.00),
(4, 3, 2, 3200.00),
(4, 6, 1, 1800.00),
(5, 10, 1, 2800.00),
(5, 11, 1, 4100.00),
(6, 12, 1, 3600.00),
(6, 17, 1, 1100.00),
(7, 15, 1, 11500.00),
(7, 16, 2, 850.00),
(8, 8, 1, 6200.00),
(9, 9, 1, 5400.00),
(9, 13, 2, 1250.00),
(10, 2, 1, 14800.00),
(10, 5, 1, 450.00),
(11, 7, 1, 8900.00),
(11, 8, 1, 6200.00),
(12, 14, 2, 780.00),
(12, 13, 1, 1250.00),
(13, 1, 1, 12500.00),
(13, 4, 1, 950.00),
(14, 3, 1, 3200.00),
(14, 6, 2, 1800.00),
(15, 10, 2, 2800.00),
(15, 5, 3, 450.00),
(16, 7, 1, 8900.00),
(16, 13, 2, 1250.00),
(17, 15, 1, 11500.00),
(18, 12, 1, 3600.00),
(18, 17, 2, 1100.00),
(19, 4, 2, 950.00),
(19, 6, 1, 1800.00),
(20, 2, 1, 14800.00),
(20, 11, 1, 4100.00),
(21, 9, 1, 5400.00),
(21, 14, 2, 780.00),
(22, 8, 1, 6200.00),
(22, 16, 1, 850.00);

-- =====================================================
-- VERIFICAR QUE TODO SE CARGO CORRECTAMENTE
-- =====================================================

SELECT 'clientes' AS tabla, COUNT(*) AS registros
FROM clientes
UNION ALL
SELECT 'categorias', COUNT(*) FROM categorias
UNION ALL
SELECT 'productos', COUNT(*) FROM productos
UNION ALL
SELECT 'pedidos', COUNT(*) FROM pedidos
UNION ALL
SELECT 'detalle_pedido', COUNT(*) FROM detalle_pedido;
```

### Cómo ejecutar el script

1. Abre MySQL Workbench y conéctate a tu servidor.
2. Crea una pestaña SQL nueva.
3. Pega el script completo.
4. Ejecuta el script con el botón del rayo.
5. Comprueba que se hayan creado las cinco tablas y que la consulta final muestre los registros.

Deberías obtener estos conteos:

| Tabla            | Registros |
| ---------------- | --------- |
| `clientes`       | 10        |
| `categorias`     | 6         |
| `productos`      | 18        |
| `pedidos`        | 22        |
| `detalle_pedido` | 42        |

Para verificar la base, también puedes ejecutar:

sql

```sql
USE practicas_sql;

SHOW TABLES;

SELECT * FROM clientes;
SELECT * FROM productos;
SELECT * FROM pedidos;
```

## 4. Ejercicios de práctica

Intenta resolverlos primero sin mirar las soluciones. Todos utilizan las tablas del script anterior.

### Nivel 1 — SELECT, WHERE y ORDER BY

Ejercicios 1–5

1. Mostrar todos los clientes registrados.
2. Mostrar el nombre y el precio de todos los productos.
3. Mostrar los productos cuyo precio sea mayor a $3,000.
4. Mostrar los productos ordenados por precio de mayor a menor.
5. Mostrar los cinco productos más caros.

### Nivel 2 — COUNT, SUM y AVG

Ejercicios 6–10

6. Contar cuántos clientes hay registrados.
7. Calcular el precio promedio de los productos.
8. Encontrar el precio mínimo y máximo de los productos.
9. Contar cuántos pedidos existen para cada estado.
10. Calcular el importe total de cada pedido multiplicando cantidad por precio unitario.

### Nivel 3 — GROUP BY y HAVING

Ejercicios 11–14

11. Contar cuántos clientes hay por ciudad.
12. Calcular el precio promedio de los productos por categoría.
13. Mostrar las categorías que tengan más de dos productos.
14. Calcular cuánto se vendió por producto, considerando las cantidades y los precios de los detalles de pedido.

### Nivel 4 — JOIN y LEFT JOIN

Ejercicios 15–18

15. Mostrar el número de pedido, la fecha y el nombre del cliente.
16. Mostrar cada producto con el nombre de su categoría.
17. Mostrar los productos de cada pedido, con cantidad, precio unitario e importe por línea.
18. Mostrar todos los clientes, incluidos los que no tengan pedidos, y contar cuántos pedidos tiene cada uno.

### Nivel 5 — Subconsultas

Ejercicios 19–22

19. Mostrar los productos cuyo precio sea superior al precio promedio de todos los productos.
20. Encontrar el producto o los productos con el precio más alto.
21. Mostrar los clientes que hayan realizado al menos un pedido.
22. Mostrar los productos cuyo precio sea superior al precio promedio de su propia categoría.

## 5. Guía de resolución con ejemplos explicados

Aquí tienes soluciones comentadas para que entiendas cómo construir las consultas, no solamente cómo copiar el código.

### Ejercicio 1. Consultar y filtrar productos

Objetivo: mostrar los productos que cuestan más de $3,000, ordenados del más caro al más barato.

sql

```sql

SELECT nombre, precio
FROM productos
WHERE precio > 3000
ORDER BY precio DESC;
```

Cómo resolverlo:

1. `SELECT nombre, precio`: elige las columnas que necesitas.
2. `FROM productos`: indica la tabla.
3. `WHERE precio > 3000`: filtra los productos.
4. `ORDER BY precio DESC`: ordena de mayor a menor.

Si utilizas `ASC`, el orden será de menor a mayor.

### Ejercicio 6. Contar clientes con COUNT

Objetivo: conocer cuántos clientes están registrados.

sql

```sql

SELECT COUNT(*) AS total_clientes
FROM clientes;
```

`COUNT(*)` cuenta las filas. `AS total_clientes` asigna un nombre al resultado.

### Ejercicio 7. Calcular promedios con AVG

Objetivo: calcular el precio promedio de todos los productos.

sql

```sql

SELECT ROUND(AVG(precio), 2) AS precio_promedio
FROM productos;
```

- `AVG(precio)` calcula el promedio.
- `ROUND(..., 2)` limita el resultado a dos decimales.

### Ejercicio 9. Agrupar con GROUP BY

Objetivo: contar los pedidos que existen para cada estado.

sql

```sql

SELECT
    estado,
    COUNT(*) AS total_pedidos
FROM pedidos
GROUP BY estado
ORDER BY total_pedidos DESC;
```

La clave es que `GROUP BY estado` forma un grupo por cada estado. Después, `COUNT(*)` cuenta las filas de cada grupo.

Para practicar más, cambia `estado` por `id_cliente` y observa cómo cambia el resultado.

### Ejercicio 10. Calcular importes con SUM

Objetivo: calcular el importe total de cada pedido.

sql

```sql

SELECT
    id_pedido,
    SUM(cantidad * precio_unitario) AS total_pedido
FROM detalle_pedido
GROUP BY id_pedido
ORDER BY total_pedido DESC;
```

La fórmula de cada línea es:

$$
\text{Importe}=\text{cantidad}\times\text{precio unitario}
$$

Después, `SUM()` suma los importes de las líneas pertenecientes al mismo pedido.

### Ejercicio 12. JOIN y GROUP BY

Objetivo: calcular el precio promedio de los productos por categoría.

sql

```sql

SELECT
    c.nombre AS categoria,
    ROUND(AVG(p.precio), 2) AS precio_promedio,
    COUNT(p.id_producto) AS total_productos
FROM categorias c
INNER JOIN productos p
    ON c.id_categoria = p.id_categoria
GROUP BY c.id_categoria, c.nombre
ORDER BY precio_promedio DESC;
```

Guía de resolución:

- `categorias c` crea el alias `c`.
- `productos p` crea el alias `p`.
- `INNER JOIN` relaciona ambas tablas.
- `ON` establece la condición de relación.
- `GROUP BY` agrupa los productos por categoría.
- `AVG()` calcula el promedio y `COUNT()` cuenta los productos.

Los alias permiten escribir consultas más cortas y legibles.

### Ejercicio 13. Filtrar grupos con HAVING

Objetivo: mostrar las categorías que tienen más de dos productos.

sql

```sql

SELECT
    c.nombre AS categoria,
    COUNT(p.id_producto) AS total_productos
FROM categorias c
INNER JOIN productos p
    ON c.id_categoria = p.id_categoria
GROUP BY c.id_categoria, c.nombre
HAVING COUNT(p.id_producto) > 2;
```

Diferencia importante:

- `WHERE` filtra filas antes de agrupar.
- `HAVING` filtra los grupos después de agrupar.

Por eso se utiliza `HAVING` para condiciones sobre `COUNT()`, `SUM()` o `AVG()` calculados por grupo.

### Ejercicio 15. Relacionar clientes con pedidos

Objetivo: mostrar el número de pedido, la fecha y el nombre del cliente.

sql

```sql

SELECT
    p.id_pedido,
    p.fecha,
    c.nombre AS cliente,
    p.estado
FROM pedidos p
INNER JOIN clientes c
    ON p.id_cliente = c.id_cliente
ORDER BY p.fecha;
```

La columna `id_cliente` existe en las dos tablas, pero desempeña funciones distintas:

- En `clientes`, es la clave primaria.
- En `pedidos`, es una clave foránea que identifica al cliente que hizo el pedido.

### Ejercicio 18. LEFT JOIN para incluir todos los clientes

Objetivo: contar los pedidos de cada cliente, incluso cuando no tenga ninguno.

sql

```sql

SELECT
    c.id_cliente,
    c.nombre,
    COUNT(p.id_pedido) AS total_pedidos
FROM clientes c
LEFT JOIN pedidos p
    ON c.id_cliente = p.id_cliente
GROUP BY c.id_cliente, c.nombre
ORDER BY total_pedidos DESC;
```

`LEFT JOIN` conserva todos los clientes, aunque no haya coincidencia en `pedidos`.

Usamos `COUNT(p.id_pedido)` y no `COUNT(*)`, porque `COUNT(*)` contaría también la fila generada por el `LEFT JOIN` cuando un cliente no tiene pedidos.

### Ejercicio 19. Subconsulta con AVG

Objetivo: encontrar los productos cuyo precio sea superior al promedio general.

sql

```sql

SELECT nombre, precio
FROM productos
WHERE precio > (
    SELECT AVG(precio)
    FROM productos
)
ORDER BY precio DESC;
```

La consulta interna:

sql

```sql
SELECT AVG(precio)
FROM productos;
```

calcula un único valor. La consulta externa utiliza ese valor en el filtro `WHERE`.

### Ejercicio 22. Subconsulta correlacionada

Objetivo: encontrar los productos cuyo precio supera el promedio de su categoría.

sql

```sql

SELECT
    p.nombre,
    p.precio,
    c.nombre AS categoria
FROM productos p
INNER JOIN categorias c
    ON p.id_categoria = c.id_categoria
WHERE p.precio > (
    SELECT AVG(p2.precio)
    FROM productos p2
    WHERE p2.id_categoria = p.id_categoria
)
ORDER BY c.nombre, p.precio DESC;
```

Esta consulta es un poco más avanzada:

- La consulta externa recorre los productos.
- La subconsulta calcula el promedio de la categoría del producto actual.
- La condición compara el precio del producto con ese promedio.

La condición `p2.id_categoria = p.id_categoria` relaciona la subconsulta con la fila de la consulta externa.

## 6\. Ejercicios adicionales de nivel intermedio

Cuando termines los 22 ejercicios, intenta resolver estos retos combinados.

| N.º | Reto                                                                                         |
| --- | -------------------------------------------------------------------------------------------- |
| 23  | Mostrar los clientes cuyo gasto total en pedidos entregados supere los $10,000.              |
| 24  | Encontrar la categoría con mayores ingresos por líneas de pedido.                            |
| 25  | Mostrar los tres productos más vendidos por unidades.                                        |
| 26  | Calcular el promedio de importe de los pedidos, utilizando los detalles de cada pedido.      |
| 27  | Mostrar los clientes que nunca han realizado un pedido.                                      |
| 28  | Encontrar los clientes que han realizado más pedidos que el promedio de pedidos por cliente. |

Pistas para resolverlos:

- Ejercicios 23 y 24: combina `JOIN`, `SUM()` y `GROUP BY`.
- Ejercicios 25 y 26: utiliza `SUM()` y una consulta agrupada.
- Ejercicio 27: practica `LEFT JOIN` con `IS NULL` o `NOT EXISTS`.
- Ejercicio 28: necesitarás una subconsulta que calcule el promedio de pedidos por cliente.

Importante: el gasto de un cliente debe calcularse sumando `cantidad * precio_unitario` de los detalles de sus pedidos, y debes decidir explícitamente qué estados de pedido incluir. Para el ejercicio 23, se consideran únicamente los pedidos entregados.

## 7\. Orden recomendado para resolver cualquier consulta

Puedes utilizar esta guía como lista de comprobación cada vez que recibas un problema de SQL.

1\. Identifica los datos que necesitas.

¿Qué columnas quieres mostrar y de qué tablas proceden?

2\. Determina los filtros.

¿Necesitas `WHERE`, `LIKE`, `IN`, `BETWEEN` o `IS NULL`?

3\. Comprueba las relaciones.

Si utilizas varias tablas, identifica sus claves primarias y foráneas para construir el `JOIN`.

4\. Decide si debes agrupar.

Si te piden totales, cantidades o promedios por categoría, cliente o estado, probablemente necesites `GROUP BY`.

5\. Ordena y valida el resultado.

Añade `ORDER BY` si se requiere y comprueba algunos resultados manualmente.

## 8\. Prueba rápida de conocimientos

1. ¿Qué cláusula ordena los resultados?

GROUP BY

ORDER BY

HAVING

2. ¿Qué función calcula un promedio?

SUM()

COUNT()

AVG()

3. ¿Qué JOIN conserva todas las filas de la tabla izquierda?

INNER JOIN

LEFT JOIN

CROSS JOIN

4. ¿Qué cláusula filtra grupos después de GROUP BY?

WHERE

HAVING

ON

5. ¿Qué consulta permite comparar un precio con el promedio general?

Una subconsulta con AVG()

Solo ORDER BY

Solo DISTINCT

Comprobar respuestas Reiniciar

## 9\. Cómo continuar

Mi recomendación es trabajar en este orden: primero los ejercicios 1–5, luego las funciones de agregación, después los `JOIN` y finalmente las subconsultas. Ejecuta cada consulta en MySQL Workbench y modifica una condición para observar cómo cambia el resultado.

Con esta base de datos tienes material para practicar desde las consultas más sencillas hasta consultas combinadas de nivel intermedio, sin tener que crear datos adicionales para cada ejercicio.

---

---

---

> En un ejercicio que me pusieron era una consulta multi nivel...
> devolver en el query

- Top level manager's id
- total numer of employees on that managaer
- exclude managers with no employees

puedes generar un script para este requerimiento?

---

Sí. Este ejercicio es de SQL de nivel intermedio y trata sobre una jerarquía de empleados y gerentes, utilizando una consulta multinivel (subconsulta o consulta jerárquica).

El objetivo es devolver:

- `top_level_manager_id`: el ID del gerente de nivel superior.
- `total_employees`: el total de empleados que dependen de ese gerente.
- Excluir a los gerentes que no tienen empleados.

Voy a utilizar una estructura compatible con MySQL 8.0 o superior.

## 1. Script para crear los datos de prueba

Cada empleado tiene un `manager_id` que apunta al ID de su gerente. Si `manager_id` es `NULL`, se considera que la persona está en el nivel superior de la organización.

sql

```sql

DROP DATABASE IF EXISTS empresa_jerarquia;
CREATE DATABASE empresa_jerarquia;
USE empresa_jerarquia;

CREATE TABLE employees (
    employee_id INT PRIMARY KEY,
    employee_name VARCHAR(100) NOT NULL,
    manager_id INT NULL,
    FOREIGN KEY (manager_id)
        REFERENCES employees(employee_id)
);

INSERT INTO employees (employee_id, employee_name, manager_id)
VALUES
(1, 'Ana - CEO', NULL),
(2, 'Carlos - Director', 1),
(3, 'Maria - Directora', 1),
(4, 'Luis - Gerente', 2),
(5, 'Sofia - Gerente', 2),
(6, 'Pedro - Analista', 4),
(7, 'Laura - Analista', 4),
(8, 'Diego - Analista', 5),
(9, 'Elena - Analista', 3),
(10, 'Jorge - Analista', 3),
(11, 'Pablo - Especialista', 6),
(12, 'Marta - Especialista', 6),
(13, 'Raul - Empleado sin equipo', NULL),
(14, 'Lucia - Empleada', 13);
```

La estructura resultante es:

Ana — CEO (ID 1)

Carlos (ID 2)

Director

María (ID 3)

Directora

Gerentes 4 y 5

3 empleados directos en total

Empleados 9 y 10

2 empleados directos

Además, los empleados 11 y 12 dependen indirectamente del CEO a través del gerente 4.

## 2. Solución A: contar empleados directos del gerente de nivel superior

Si el enunciado considera como empleados únicamente a las personas que reportan directamente al gerente, esta es la consulta:

sql

```sql

SELECT
    m.employee_id AS top_level_manager_id,
    COUNT(e.employee_id) AS total_employees
FROM employees m
INNER JOIN employees e
    ON e.manager_id = m.employee_id
WHERE m.manager_id IS NULL
GROUP BY m.employee_id;
```

Resultado esperado:

| top_level_manager_id | total_employees |
| -------------------- | --------------- |
| 1                    | 2               |

¿Por qué funciona?

- `m` representa al gerente de nivel superior.
- `e` representa a los empleados que reportan a ese gerente.
- `WHERE m.manager_id IS NULL` selecciona a los empleados sin gerente.
- `INNER JOIN` excluye automáticamente a los gerentes que no tienen empleados.
- `COUNT()` cuenta sus empleados directos.

En este ejemplo, el CEO tiene dos reportes directos: Carlos y María.

## 3. Solución B: contar todos los empleados de forma multinivel

Si el ejercicio pide contar todos los empleados que dependen del gerente, incluidos los indirectos, necesitamos una consulta recursiva.

Esta es la interpretación más completa de una jerarquía multinivel.

sql

```sql

WITH RECURSIVE employee_hierarchy AS (
    -- Nivel inicial: gerentes de nivel superior
    SELECT
        employee_id AS top_level_manager_id,
        employee_id
    FROM employees
    WHERE manager_id IS NULL

    UNION ALL

    -- Buscar los subordinados de cada empleado
    SELECT
        h.top_level_manager_id,
        e.employee_id
    FROM employee_hierarchy h
    INNER JOIN employees e
        ON e.manager_id = h.employee_id
)
SELECT
    h.top_level_manager_id,
    COUNT(*) - 1 AS total_employees
FROM employee_hierarchy h
GROUP BY h.top_level_manager_id
HAVING COUNT(*) > 1;
```

Resultado esperado con estos datos:

| top_level_manager_id | total_employees |
| -------------------- | --------------- |
| 1                    | 11              |
| 13                   | 1               |

La consulta recursiva empieza por cada empleado sin gerente y va buscando todos sus subordinados, nivel por nivel.

- `WITH RECURSIVE` permite recorrer la jerarquía.
- `UNION ALL` incorpora cada nivel encontrado.
- `COUNT(*) - 1` excluye al propio gerente del conteo.
- `HAVING COUNT(*) > 1` elimina los gerentes que no tienen subordinados.

En este ejemplo, Ana (ID 1) tiene 11 empleados por debajo de ella en toda la jerarquía. Raúl (ID 13) tiene una empleada, Lucía.

## 4. ¿Cuál debes utilizar en tu ejercicio?

Depende de lo que signifique total number of employees on that manager:

- Si cuenta solamente a los empleados que reportan directamente al gerente, utiliza la solución A.
- Si cuenta a todos los subordinados de cualquier nivel, utiliza la solución B.

Por la expresión multilevel query, probablemente esperan la segunda solución, especialmente si el problema incluye varios niveles de gerentes.

Una precisión importante: la consulta recursiva anterior considera como gerente de nivel superior a cualquier empleado cuyo `manager_id` sea `NULL`. Si tu empresa debe tener un único CEO, también podemos restringir la consulta para devolver exclusivamente ese gerente raíz.

---

---

---

> la tabla que tienen es

Table employees
id integer primary key
name varchar(255) not null
managerId Integer
Foreign KEY (managerId) REFERENCES employees(id)

Para este puedes generar el script
asi como el resultado esperado

---

Claro. Con la estructura exacta de tu tabla `employees`, te dejo el script completo para MySQL 8.0+, los datos de prueba y el resultado esperado.

Voy a resolverlo como una consulta multinivel: contar todos los empleados que dependen de cada gerente de nivel superior, directa o indirectamente, excluyendo a los gerentes que no tienen empleados.

## 1. Script para crear la tabla y cargar datos

sql

```sql

-- Crear la base de datos
DROP DATABASE IF EXISTS employee_db;
CREATE DATABASE employee_db;
USE employee_db;

-- Crear la tabla con la estructura del ejercicio
CREATE TABLE employees (
    id INTEGER PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    managerId INTEGER,
    FOREIGN KEY (managerId) REFERENCES employees(id)
);

-- Insertar datos de prueba
INSERT INTO employees (id, name, managerId) VALUES
(1,  'Alice',   NULL),
(2,  'Bob',     1),
(3,  'Charlie', 1),
(4,  'David',   2),
(5,  'Eva',     2),
(6,  'Frank',   3),
(7,  'Grace',   3),
(8,  'Helen',   4),
(9,  'Ian',     4),
(10, 'Jack',    NULL),
(11, 'Kate',    10),
(12, 'Leo',     11),
(13, 'Mike',    NULL);
```

## 2. Visualización de la jerarquía

Alice (ID 1)

Gerente de nivel superior

Bob (ID 2)

David (ID 4)

Eva (ID 5)

Helen (ID 8)

Depende de David

Ian (ID 9)

Depende de David

Charlie (ID 3)

Frank (ID 6)

Grace (ID 7)

Jack (ID 10)

Kate (11) → Leo (12)

Mike (ID 13)

No tiene empleados

## 3. Consulta SQL multinivel

Esta consulta recursiva funciona en MySQL 8.0 o superior.

sql

```sql

WITH RECURSIVE employee_hierarchy AS (
    -- Obtener los gerentes de nivel superior
    SELECT
        id AS top_level_manager_id,
        id AS employee_id
    FROM employees
    WHERE managerId IS NULL

    UNION ALL

    -- Encontrar empleados de cada nivel
    SELECT
        h.top_level_manager_id,
        e.id AS employee_id
    FROM employee_hierarchy h
    INNER JOIN employees e
        ON e.managerId = h.employee_id
)
SELECT
    h.top_level_manager_id,
    COUNT(*) - 1 AS total_employees
FROM employee_hierarchy h
GROUP BY h.top_level_manager_id
HAVING COUNT(*) > 1
ORDER BY h.top_level_manager_id;
```

## 4. Resultado esperado

| top_level_manager_id | total_employees |
| -------------------- | --------------- |
| 1                    | 8               |
| 10                   | 2               |

¿Por qué?

- Alice (ID 1): tiene 8 empleados en total, incluidos Bob, Charlie y todos sus subordinados.
- Jack (ID 10): tiene 2 empleados: Kate y Leo.
- Mike (ID 13): no aparece porque no tiene subordinados.

La cláusula `HAVING COUNT(*) > 1` excluye los gerentes sin empleados, mientras que `COUNT(*) - 1` evita contar al gerente como uno de sus propios empleados.

## 5\. Si el ejercicio solo pide empleados directos

Si en tu examen no se deben contar los subordinados indirectos, puedes resolverlo con una consulta más sencilla:

sql

```sql

SELECT
    m.id AS top_level_manager_id,
    COUNT(e.id) AS total_employees
FROM employees m
INNER JOIN employees e
    ON e.managerId = m.id
WHERE m.managerId IS NULL
GROUP BY m.id;
```

El resultado sería:

| top_level_manager_id | total_employees |
| -------------------- | --------------- |
| 1                    | 2               |
| 10                   | 1               |

La diferencia clave es que la consulta recursiva cuenta todos los niveles, mientras que el `JOIN` sencillo cuenta solamente los reportes directos.

---

---

---

# Start Box

<div style="border: 1px solid #999; border-radius: 10px; padding: 16px; text-align: center;">
  <div style="display: inline-block; width: 160px; box-sizing: border-box; background-color: #DCEAFE; color: #222; border-radius: 8px; padding: 12px;">
    <strong>Ana (1)</strong>
    <div style="font-size: 12px; margin-top: 4px;">Directora</div>
  </div>
</div>

# End Box
