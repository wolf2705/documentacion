>Puedes mostrarme un ejemplo de sql usando with recursive dentro de una tabla que tiene tres columnas id, name, managerid que es Fk de id.

---

¡Claro! Un caso típico para usar `WITH RECURSIVE` es una tabla de empleados donde cada empleado tiene un jefe, y ese jefe también puede tener otro jefe, formando una jerarquía.

## 1. Ejemplo de la tabla `Employee`

Supongamos que tienes estas tres columnas:

SQL

```sql
CREATE TABLE Employee (
    id        INT PRIMARY KEY,
    name      VARCHAR(100),
    managerid INT NULL,
    FOREIGN KEY (managerid) REFERENCES Employee(id)
);
```

* `id`: identificador del empleado.

* `name`: nombre del empleado.

* `managerid`: identificador de su jefe, que referencia a `id`.

* Si `managerid` es `NULL`, el empleado no tiene jefe.

Datos de ejemplo:

| id  | name   | managerid |
| --- | ------ | --------- |
| 1   | Ana    | NULL      |
| 2   | Carlos | 1         |
| 3   | María  | 1         |
| 4   | Pedro  | 2         |
| 5   | Laura  | 2         |
| 6   | José   | 4         |

La jerarquía sería:


<div style="border: 1px solid #999; border-radius: 12px; padding: 16px; display: flex; flex-direction: column; align-items: center; gap: 16px;">

  <!-- Directora -->
  <div style="background-color: #DCEAFE; color: #222; border-radius: 8px; padding: 12px; width: 300px; box-sizing: border-box; text-align: center;">
    <strong>Ana &mdash; CEO (1)</strong>
    <div style="font-size: 12px; margin-top: 4px;">Directora</div>
  </div>

  <!-- Segundo nivel -->
  <div style="display: flex; justify-content: center; gap: 20px; width: 500px; box-sizing: border-box;">
    <div style="background-color: #DDF4E4; color: #222; border-radius: 8px; padding: 10px; flex: 1; text-align: center; min-width: 0;">
      <strong>Carlos &mdash; Manager (2)</strong>
    </div>
    <div style="background-color: #DDF4E4; color: #222; border-radius: 8px; padding: 10px; flex: 1; text-align: center; min-width: 0;">
      <strong>Maria &mdash; Manager (3)</strong>
    </div>
  </div>

  <!-- Tercer nivel -->
  <div style="display: flex; justify-content: center; gap: 12px; width: 500px; box-sizing: border-box;">
    <div style="background-color: #FCE8D5; color: #222; border-radius: 8px; padding: 10px; flex: 1; text-align: center; min-width: 0;">
      Pedro &mdash; (4)
    </div>
    <div style="background-color: #FCE8D5; color: #222; border-radius: 8px; padding: 10px; flex: 1; text-align: center; min-width: 0;">
      Laura (5)
    </div>
  </div>

  <!-- Último nivel -->
  <div style="background-color: #F3E5F5; color: #222; border-radius: 8px; padding: 10px; width: 200px; box-sizing: border-box; text-align: center;">
    José (6)
  </div>

</div>


Jerarquía conceptual: Ana → Carlos → Pedro → José. Laura también reporta a Carlos.

## 2. Ejemplo de `WITH RECURSIVE`

Objetivo: obtener todos los empleados que dependen directa o indirectamente de Ana (`id = 1`).

SQL

```sql
WITH RECURSIVE EmployeeHierarchy AS (
    -- 1. Caso base: encontrar al empleado raíz
    SELECT
        id,
        name,
        managerid,
        0 AS level
    FROM Employee
    WHERE id = 1

    UNION ALL

    -- 2. Parte recursiva: encontrar sus subordinados
    SELECT
        e.id,
        e.name,
        e.managerid,
        eh.level + 1 AS level
    FROM Employee e
    INNER JOIN EmployeeHierarchy eh
        ON e.managerid = eh.id
)
SELECT
    id,
    name,
    managerid,
    level
FROM EmployeeHierarchy
ORDER BY level, id;
```

### ¿Cómo funciona?

1. Caso base: busca a Ana (`id = 1`). La encuentra en el nivel `0`.

2. Primera iteración: busca empleados cuyo `managerid = 1`. Encuentra a Carlos y María, ambos en el nivel `1`.

3. Segunda iteración: busca empleados que reportan a Carlos o María. Encuentra a Pedro y Laura, en el nivel `2`.

4. Tercera iteración: encuentra a José, que reporta a Pedro, en el nivel `3`.

5. Fin: cuando ya no encuentra más subordinados, la recursión termina.

## 3. Resultado esperado

| id  | name   | managerid | level |
| --- | ------ | --------- | ----- |
| 1   | Ana    | NULL      | 0     |
| 2   | Carlos | 1         | 1     |
| 3   | María  | 1         | 1     |
| 4   | Pedro  | 2         | 2     |
| 5   | Laura  | 2         | 2     |
| 6   | José   | 4         | 3     |

El campo `level` indica la distancia jerárquica respecto a Ana.

## 4. ¿Y si quiero encontrar al jefe de cada empleado?

Puedes recorrer la relación en sentido contrario. Por ejemplo, para obtener la cadena de jefes de José (`id = 6`), desde José hasta Ana:

SQL

```sql
WITH RECURSIVE ManagerHierarchy AS (
    -- Caso base: empleado inicial
    SELECT
        id,
        name,
        managerid,
        0 AS level
    FROM Employee
    WHERE id = 6

    UNION ALL

    -- Subir de empleado a jefe
    SELECT
        m.id,
        m.name,
        m.managerid,
        mh.level + 1
    FROM Employee m
    INNER JOIN ManagerHierarchy mh
        ON m.id = mh.managerid
)
SELECT
    id,
    name,
    managerid,
    level
FROM ManagerHierarchy
ORDER BY level;
```

Resultado:

| id  | name   | managerid | level |
| --- | ------ | --------- | ----- |
| 6   | José   | 4         | 0     |
| 4   | Pedro  | 2         | 1     |
| 2   | Carlos | 1         | 2     |
| 1   | Ana    | NULL      | 3     |

## 5. La clave para entender `WITH RECURSIVE`

La estructura general es:

SQL

```sql
WITH RECURSIVE nombre_cte AS (
    -- Caso base
    SELECT ...

    UNION ALL

    -- Consulta recursiva
    SELECT ...
    FROM tabla
    JOIN nombre_cte ON ...
)
SELECT *
FROM nombre_cte;
```

* Caso base: define dónde comienza el recorrido.

* `UNION ALL`: combina el resultado inicial con las filas que se van encontrando.

* Consulta recursiva: utiliza el resultado anterior de la CTE para encontrar el siguiente nivel.

* Condición de unión: determina cómo se conecta cada fila con la siguiente.

Este patrón funciona, por ejemplo, en PostgreSQL y MySQL 8+. En SQL Server se usa `WITH`, pero no se escribe la palabra `RECURSIVE`.

Una pregunta importante: ¿qué motor de base de datos utilizas: SQL Server, PostgreSQL, MySQL u Oracle? Puedo adaptar el ejemplo a su sintaxis y mostrarte cómo evitar ciclos si un empleado termina siendo su propio jefe indirectamente.

---
---
---
>Y si no deseo usar with recursive como sería la consulta?

---
Si no deseas usar `WITH RECURSIVE`, puedes resolverlo mediante `JOIN` sobre la misma tabla, ya que `managerid` referencia al `id` de otro empleado.

La diferencia principal es que sin recursividad debes indicar explícitamente cuántos niveles de jerarquía quieres consultar.

## 1. Usando `JOIN` para obtener los empleados debajo de Ana

Con los datos del ejemplo anterior:

| id  | name   | managerid |
| --- | ------ | --------- |
| 1   | Ana    | NULL      |
| 2   | Carlos | 1         |
| 3   | María  | 1         |
| 4   | Pedro  | 2         |
| 5   | Laura  | 2         |
| 6   | José   | 4         |

Si quieres obtener a Ana y todos sus subordinados hasta tres niveles de profundidad, puedes escribir:

SQL

```sql
SELECT
    e1.id,
    e1.name,
    e1.managerid,
    0 AS level
FROM Employee e1
WHERE e1.id = 1

UNION ALL

SELECT
    e2.id,
    e2.name,
    e2.managerid,
    1 AS level
FROM Employee e2
INNER JOIN Employee e1
    ON e2.managerid = e1.id
WHERE e1.id = 1

UNION ALL

SELECT
    e3.id,
    e3.name,
    e3.managerid,
    2 AS level
FROM Employee e3
INNER JOIN Employee e2
    ON e3.managerid = e2.id
INNER JOIN Employee e1
    ON e2.managerid = e1.id
WHERE e1.id = 1

UNION ALL

SELECT
    e4.id,
    e4.name,
    e4.managerid,
    3 AS level
FROM Employee e4
INNER JOIN Employee e3
    ON e4.managerid = e3.id
INNER JOIN Employee e2
    ON e3.managerid = e2.id
INNER JOIN Employee e1
    ON e2.managerid = e1.id
WHERE e1.id = 1

ORDER BY level, id;
```

El resultado será:

| id  | name   | managerid | level |
| --- | ------ | --------- | ----- |
| 1   | Ana    | NULL      | 0     |
| 2   | Carlos | 1         | 1     |
| 3   | María  | 1         | 1     |
| 4   | Pedro  | 2         | 2     |
| 5   | Laura  | 2         | 2     |
| 6   | José   | 4         | 3     |

## 2. ¿Qué pasa si solo quiero los subordinados directos?

En ese caso, la consulta es mucho más sencilla:

SQL

```sql
SELECT
    e.id,
    e.name,
    e.managerid
FROM Employee e
WHERE e.managerid = 1;
```

Resultado: Carlos y María.

Si quieres los subordinados de Carlos:

SQL

```sql
SELECT
    e.id,
    e.name,
    e.managerid
FROM Employee e
WHERE e.managerid = 2;
```

Resultado: Pedro y Laura.

## 3. ¿Cuál es la diferencia?

| Método           | Ventaja                                              | Desventaja                                     |
| ---------------- | ---------------------------------------------------- | ---------------------------------------------- |
| `JOIN`           | Sencillo para pocos niveles conocidos                | Debes escribir un `JOIN` por cada nivel        |
| `UNION ALL`      | Permite combinar varios niveles en una consulta      | La consulta crece con cada nivel               |
| `WITH RECURSIVE` | Recorre automáticamente todos los niveles necesarios | Su sintaxis depende del motor de base de datos |

Mi recomendación: si tu jerarquía tiene una profundidad fija de dos o tres niveles, los `JOIN` pueden ser suficientes. Si la profundidad es variable o desconocida, la consulta recursiva es mucho más mantenible.

Si me dices qué motor de base de datos utilizas y si necesitas obtener todos los subordinados o todos los jefes, puedo darte la alternativa más adecuada sin usar `WITH RECURSIVE`.

---
---
---
Lo diseñé usando componentes de distribución visual para crear un organigrama. Cada empleado está dentro de un cuadro y utilicé filas y columnas para representar los niveles de la jerarquía.

Por ejemplo, esta es una versión simplificada de cómo se construye en JSX usando los componentes visuales disponibles en esta conversación:

JavaScript

```html
<box border radius="lg" padding={3} align="center" gap={2}>
  <box background="#DCEAFE" theme="light" radius="md" padding={3} width="160px" align="center">
    **Ana (1)**
    <text size="xs">Directora</text>
  </box>

  <row justify="center" gap={5} width="100%">
    <box background="#DDF4E4" theme="light" radius="md" padding={2} flex="1" align="center">
      **Carlos (2)**
    </box>
    <box background="#DDF4E4" theme="light" radius="md" padding={2} flex="1" align="center">
      **María (3)**
    </box>
  </row>

  <row justify="center" gap={3} width="100%">
    <box background="#FCE8D5" theme="light" radius="md" padding={2} flex="1" align="center">
      Pedro (4)
    </box>
    <box background="#FCE8D5" theme="light" radius="md" padding={2} flex="1" align="center">
      Laura (5)
    </box>
  </row>

  <box background="#F3E5F5" theme="light" radius="md" padding={2} width="140px" align="center">
    José (6)
  </box>
</box>
```

## ¿Qué hace cada componente?

| Componente       | Función                                                  |
| ---------------- | -------------------------------------------------------- |
| `box`            | Crea un contenedor para cada empleado.                   |
| `row`            | Coloca varios empleados horizontalmente.                 |
| `align="center"` | Centra el contenido.                                     |
| `gap={2}`        | Define el espacio entre elementos.                       |
| `padding={3}`    | Añade espacio interior.                                  |
| `radius="md"`    | Redondea las esquinas.                                   |
| `background`     | Define el color de fondo.                                |
| `flex="1"`       | Permite que los cuadros compartan el espacio disponible. |

Los colores representan los distintos niveles visuales, no son generados automáticamente a partir de SQL.

Un detalle importante: este ejemplo organiza visualmente los empleados, pero no dibuja líneas de conexión entre ellos. Para un organigrama real con líneas que conecten automáticamente a cada jefe con sus subordinados, utilizaría una estructura de nodos y conectores, por ejemplo con SVG o una biblioteca de diagramas como React Flow.

