# AdventureWorks (PostgreSQL) — Ejercicios de Exploración

Continuación de la exploración de las tablas principales de AdventureWorks.

- **Parte 1:** Radiografía del esquema (resuelta).
- **Parte 2:** Relaciones entre tablas (`humanresources`, `purchasing`, `production` — inventario y órdenes de trabajo), sin resolver.

---

## Parte 1 — Radiografía del esquema

### Ejercicio 1 — Volumen de datos por tabla

**Orden:** Listar las tablas de los esquemas `humanresources`, `purchasing` y `production`, junto con la cantidad aproximada de filas de cada una, para tener una idea del volumen de datos.

**Campos sugeridos:** `esquema`, `tabla`, `filas_aproximadas`

> **Nota:** `n_live_tup` se actualiza con `ANALYZE`/autovacuum. Si la base recién fue importada y los valores aparecen en 0, ejecutar primero: `ANALYZE;`

```sql
SELECT
    schemaname AS esquema,
    relname AS tabla,
    n_live_tup AS filas_aproximadas
FROM pg_stat_user_tables
WHERE schemaname IN ('humanresources', 'purchasing', 'production')
ORDER BY filas_aproximadas DESC;
```

---

### Ejercicio 2 — Estructura de una tabla

**Orden:** Mostrar el nombre, tipo de dato, si permite nulos y la posición de cada columna de la tabla `humanresources.employee`, ordenadas según su posición original.

**Campos sugeridos:** `columna`, `tipo_dato`, `es_nulable`, `posicion`

```sql
SELECT
    column_name AS columna,
    data_type AS tipo_dato,
    is_nullable AS es_nulable,
    ordinal_position AS posicion
FROM information_schema.columns
WHERE table_schema = 'humanresources'
  AND table_name = 'employee'
ORDER BY ordinal_position;
```

---

### Ejercicio 3 — Claves primarias por tabla

**Orden:** Listar las claves primarias definidas en los esquemas `humanresources` y `purchasing`, mostrando el esquema, la tabla y la(s) columna(s) que forman la clave.

**Campos sugeridos:** `esquema`, `tabla`, `columna_clave`

```sql
SELECT
    tc.table_schema AS esquema,
    tc.table_name AS tabla,
    kcu.column_name AS columna_clave
FROM information_schema.table_constraints tc
INNER JOIN information_schema.key_column_usage kcu
    ON tc.constraint_name = kcu.constraint_name
    AND tc.table_schema = kcu.table_schema
WHERE tc.constraint_type = 'PRIMARY KEY'
  AND tc.table_schema IN ('humanresources', 'purchasing')
ORDER BY esquema, tabla;
```

---

### Ejercicio 4 — Relaciones (foreign keys) de una tabla

**Orden:** Mostrar todas las claves foráneas de la tabla `purchasing.purchaseorderheader`, indicando la columna de origen y la tabla/columna referenciada.

**Campos sugeridos:** `columna_origen`, `tabla_referenciada`, `columna_referenciada`

```sql
SELECT
    kcu.column_name AS columna_origen,
    ccu.table_schema || '.' || ccu.table_name AS tabla_referenciada,
    ccu.column_name AS columna_referenciada
FROM information_schema.table_constraints tc
INNER JOIN information_schema.key_column_usage kcu
    ON tc.constraint_name = kcu.constraint_name
INNER JOIN information_schema.constraint_column_usage ccu
    ON tc.constraint_name = ccu.constraint_name
WHERE tc.constraint_type = 'FOREIGN KEY'
  AND tc.table_schema = 'purchasing'
  AND tc.table_name = 'purchaseorderheader';
```

---

## Parte 2 — Relaciones entre tablas (sin resolver)

### Ejercicio 5 — Empleados por departamento (grupo Manufacturing/QA)

**Orden:** Mostrar el nombre completo, el puesto, el nombre del departamento y su grupo, considerando únicamente la asignación vigente (`enddate IS NULL`), y filtrando solo los departamentos cuyo grupo sea 'Manufacturing' o 'Quality Assurance'.

**Campos sugeridos:** `person.firstname`, `person.lastname`, `employee.jobtitle`, `department.name`, `department.groupname`

```sql
select p.firstname ,p.lastname,e.jobtitle , d."name" as department,
d.groupname , edh.enddate
from humanresources.employee e
inner join person.person p on e.businessentityid =p.businessentityid
inner join humanresources.employeedepartmenthistory edh
on edh.businessentityid = e.businessentityid
inner join humanresources.department d
on d.departmentid =edh.departmentid
where edh.enddate is null and
d.groupname in ('Manufacturing','Quality Assurance');
```

---

### Ejercicio 6 — Empleados por turno, contratados desde 2010

**Orden:** Contar la cantidad de empleados asignados actualmente a cada turno, considerando únicamente a los empleados cuya fecha de contratación sea posterior al 1 de enero de 2010.

**Campos sugeridos:** `shift.name`, `COUNT(*)`
**Filtro adicional sobre:** `employee.hiredate`

```sql
select s."name" as Turno,count( e.businessentityid) as Cantidad
from humanresources.shift s
inner join humanresources.employeedepartmenthistory edh
on edh.shiftid = s.shiftid
inner join humanresources.employee e
on e.businessentityid = edh.businessentityid
where e.hiredate >='2010-01-01'
group by s."name"
order by 2;
```

---

### Ejercicio 7 — Empleados con más de 15 años de antigüedad

**Orden:** Mostrar el nombre, la fecha de contratación y la antigüedad en años de los empleados cuya antigüedad sea mayor a 15 años, ordenados de mayor a menor.

**Campos sugeridos:** `person.firstname`, `person.lastname`, `employee.hiredate`, `AGE(CURRENT_DATE, employee.hiredate)`

```sql
select
EXTRACT(year FROM AGE(current_date,e.hiredate )) as antiguedad
,e.hiredate as "fecha_contratacion"
from humanresources.employee e
where EXTRACT(year FROM AGE(current_date,e.hiredate )) > 15
order by 1 desc
```

---

### Ejercicio 8 — Proveedores preferidos con nombre específico

**Orden:** Listar los proveedores activos cuya calificación de crédito sea 1 o 2, que además sean proveedores preferidos y cuyo nombre contenga la palabra 'Bike' o 'Sport'.

**Campos sugeridos:** `vendor.businessentityid`, `vendor.name`, `vendor.creditrating`, `vendor.preferredvendorstatus`
**Filtros adicionales sobre:** `vendor.activeflag`, `vendor.name`

```sql
select v.businessentityid , v."name" ,
v.creditrating ,v.preferredvendorstatus
from purchasing.vendor v
where v.creditrating between 1 and 2
and v."name"  like '%Bike%' or v."name" like '%Sport%'
```

---

### Ejercicio 9 — Productos con precio acordado superior a 500

**Orden:** Mostrar el nombre del producto, el proveedor y el precio estándar acordado, únicamente para productos cuyo precio estándar sea mayor a 50.

**Campos sugeridos:** `product.name`, `vendor.name`, `productvendor.standardprice`

```sql
SELECT
    p.name             AS producto,
    v.name             AS proveedor,
    pv.standardprice   AS precio_estandar
FROM purchasing.productvendor pv
INNER JOIN production.product p ON pv.productid = p.productid
INNER JOIN purchasing.vendor v  ON pv.businessentityid = v.businessentityid
WHERE pv.standardprice > 50
ORDER BY pv.standardprice DESC;
```

---

### Ejercicio 10 — Gasto por proveedor entre 2013 y 2014

**Orden:** Calcular la cantidad de órdenes de compra y el monto total por proveedor, considerando solo las órdenes realizadas entre el 1 de enero de 2013 y el 31 de diciembre de 2014, mostrando solo los proveedores con más de 5 órdenes en ese rango, ordenados por monto total descendente.

**Campos sugeridos:** `vendor.name`, `COUNT(*)`, `SUM(purchaseorderheader.totaldue)`
**Filtro adicional sobre:** `purchaseorderheader.orderdate`

```sql
SELECT
    v.name                  AS proveedor,
    COUNT(*)                AS cantidad_ordenes,
    SUM(poh.totaldue)       AS monto_total
FROM purchasing.purchaseorderheader poh
INNER JOIN purchasing.vendor v ON poh.vendorid = v.businessentityid
WHERE poh.orderdate >= '2013-01-01'
  AND poh.orderdate <  '2015-01-01'
GROUP BY v.name
HAVING COUNT(*) > 5
ORDER BY monto_total DESC;
```

---

### Ejercicio 11 — Inventario por ubicación con costo asociado

**Orden:** Mostrar el total de unidades en inventario agrupado por ubicación, incluyendo el nombre y el costo de la ubicación, mostrando solo aquellas ubicaciones cuyo costo sea mayor a 0 y cuyo total de inventario supere las 5000 unidades.

**Campos sugeridos:** `productinventory.locationid`, `location.name`, `location.costrate`, `SUM(productinventory.quantity)`

```sql
SELECT
    pi.locationid              AS ubicacion_id,
    l.name                     AS ubicacion,
    l.costrate                 AS costo,
    SUM(pi.quantity)           AS total_inventario
FROM production.productinventory pi
INNER JOIN production.location l ON pi.locationid = l.locationid
WHERE l.costrate > 0
GROUP BY pi.locationid, l.name, l.costrate
HAVING SUM(pi.quantity) > 5000
ORDER BY total_inventario DESC;
```

---

### Ejercicio 12 — Órdenes de trabajo retrasadas con alto volumen

**Orden:** Listar las órdenes de trabajo cuya fecha de finalización sea posterior a la fecha de vencimiento y cuya cantidad ordenada sea mayor a 20, junto al nombre del producto correspondiente.

**Campos sugeridos:** `workorder.workorderid`, `product.name`, `workorder.orderqty`, `workorder.duedate`, `workorder.enddate`

```sql
SELECT
    w.workorderid              AS orden_trabajo,
    p.name                     AS producto,
    w.orderqty                 AS cantidad,
    w.duedate                  AS fecha_vencimiento,
    w.enddate                  AS fecha_fin
FROM production.workorder w
INNER JOIN production.product p ON w.productid = p.productid
WHERE w.enddate > w.duedate
  AND w.orderqty > 20
ORDER BY w.enddate DESC;
```

## Ejercicio 13

El área de recursos humanos necesita saber cuántos empleados tiene cada departamento. Muestra el nombre del departamento, su grupo y la cantidad de empleados, considerando solo las asignaciones vigentes (`enddate IS NULL`) y departamentos con más de 5 empleados. Ordena por cantidad descendente y muestra únicamente los registros del 3° al 7°.

**Tablas:** `humanresources.employeedepartmenthistory`, `humanresources.department`, `humanresources.employee`

```sql
select d."name" as Departamento,
d.groupname as Grupo,
count(edh.businessentityid) as Empleados
from humanresources.department d
inner join humanresources.employeedepartmenthistory edh
on edh.departmentid = d.departmentid
where edh.enddate is null
group by d."name", d.groupname
having count(edh.businessentityid ) >5
limit 5 offset 5
```

---

## Ejercicio 14

El área de abastecimiento necesita conocer el precio mínimo, máximo y promedio acordado con los proveedores por subcategoría de producto. Muestra el nombre del proveedor, la subcategoría y los tres precios. Considera solo proveedores activos con calificación de crédito menor o igual a 2. Ordena por subcategoría y precio promedio descendente.

**Tablas:** `purchasing.vendor`, `purchasing.productvendor`, `production.product`, `production.productsubcategory`

```sql
select v."name" as Vendedor
, psc."name" as SubCategoria,
Max(pv.standardprice) precio_maximo,
MIN(pv.standardprice ) precio_minimo,
round( AVG(pv.standardprice),2) precio_promedio
from purchasing.vendor v
inner join purchasing.productvendor pv on
pv.businessentityid  = v.businessentityid
inner join production.product p
on p.productid = pv.productid
inner join production.productsubcategory psc
on psc.productsubcategoryid = p.productsubcategoryid
where v.activeflag =true and v.creditrating <=2
group by v."name" ,psc."name" ;
```

---

## Ejercicio 15

El equipo comercial necesita revisar las ventas del primer semestre de 2013. Muestra el nombre completo del cliente, el producto comprado, su categoría, el precio unitario de la línea y la segunda línea de la dirección de envío; si no tiene segunda línea registrada, mostrar 'Sin detalle'. Considera solo clientes personas naturales y productos con precio de lista mayor a 100.

**Tablas:** `sales.salesorderheader`, `sales.salesorderdetail`, `sales.customer`, `person.person`, `person.address`, `production.product`, `production.productsubcategory`, `production.productcategory`

```sql
-- Escribir consulta aquí
```

---

## Ejercicio 16

Elabore un reporte que presente el monto total de ventas (`totaldue`) por territorio comercial, desagregado en columnas independientes correspondientes a los años 2012, 2013 y 2014.

**Tablas:** `sales.salesorderheader`, `sales.salesterritory`

```sql
-- Escribir la consulta aquí
```

---

## Objetivo de la sesión

Comprender las funciones de ventana de ranking (`ROW_NUMBER`, `RANK`, `DENSE_RANK`), diferenciarlas entre sí y aplicarlas para resolver el problema de "Top N por grupo", que con agregación clásica no tiene solución directa en una sola pasada.

---

## 2. El límite de `GROUP BY`

`GROUP BY` resuelve muy bien las agregaciones, pero tiene un límite: **colapsa** las filas de cada grupo en una sola. Eso hace que ciertos cálculos no se puedan resolver en una sola pasada, por ejemplo:

- **Porcentaje sobre el total general** → necesita el total de todos los grupos (otro nivel de agregación) en la misma fila.
- **Ranking** → necesita comparar cada fila con las demás del resultado ya agregado.
- **Top N por grupo** → necesita ordenar y numerar dentro de cada grupo sin perder las filas.
  Como `GROUP BY` "pierde de vista" al resto del grupo una vez que colapsa, para estos casos necesitamos otra herramienta: las **funciones de ventana**, que agregan y comparan **sin colapsar** las filas.

---

## 3. Funciones de ventana: el problema del "Top N por grupo"

**Consigna de la sesión:** obtener los **3 productos más vendidos por cada subcategoría**.

### Por qué no alcanza con `GROUP BY` + `LIMIT`

`LIMIT` corta el resultado completo, no por grupo. Solo funciona si filtramos una subcategoría a la vez:

```sql
SELECT
    p.name AS producto,
    SUM(d.orderqty) AS cantidad_vendida
FROM sales.salesorderdetail d
INNER JOIN production.product p ON d.productid = p.productid
INNER JOIN production.productsubcategory psc ON p.productsubcategoryid = psc.productsubcategoryid
WHERE psc.name = 'Road Bikes'       -- solo una subcategoría
GROUP BY p.name
ORDER BY cantidad_vendida DESC
LIMIT 3;
```

No escala: para 37 subcategorías necesitaríamos 37 consultas. Las funciones de ventana resuelven las 37 de una vez. Vamos a construir la solución por partes, en los siguientes ejercicios.

---

### Ejercicio 1 — La base que vamos a rankear

Antes de rankear, necesitamos el dato sobre el cual se rankea. Calculá el **total de unidades vendidas por producto dentro de cada subcategoría**, ordenado para ver primero los más vendidos.

> Esto todavía es agregación clásica. Cruzá `salesorderdetail` con `product` y `productsubcategory`, sumá `orderqty` y agrupá por subcategoría y producto. Sin funciones de ventana aún.

```sql
-- Escribir la consulta aquí
```

---

### Ejercicio 2 — Numerar las filas

Sobre la consulta anterior, agregá una columna que **numere los productos dentro de cada subcategoría**, del más vendido al menos vendido, asignando un número distinto a cada fila aunque dos productos vendan lo mismo.

> Necesitás una función de ventana que asigne una numeración correlativa **sin repetir** valores. Pensá en `OVER (PARTITION BY ... ORDER BY ...)`: la partición reinicia el conteo en cada subcategoría y el orden define quién va primero. La cláusula `ORDER BY` dentro del `OVER` puede recibir directamente el `SUM(orderqty)`.

```sql
-- Escribir la consulta aquí
```

**Anatomía de `OVER()`:**

- `PARTITION BY` → divide el resultado en grupos (como un `GROUP BY` que no colapsa).
- `ORDER BY` → define el orden **dentro** de cada grupo, sobre el cual se asigna el número.

---

### Ejercicio 3 — Numerar respetando empates

Ahora agregá, **junto a la columna anterior**, otra que también ordene de mayor a menor pero que asigne **el mismo número a los productos que vendan exactamente lo mismo**. Observá qué pasa con la numeración inmediatamente después de un empate.

> Hay dos funciones que empatan; ésta es la que **salta** posiciones después del empate (por ejemplo `1, 2, 2, 4`). Mantené la columna del ejercicio anterior al lado para comparar.

```sql
-- Escribir la consulta aquí
```

---

### Ejercicio 4 — Numerar sin huecos

Sumá una **tercera columna** de ranking. Esta debe empatar igual que la anterior, pero **sin dejar huecos** en la numeración: después de un empate, el número siguiente continúa de forma consecutiva (por ejemplo `1, 2, 2, 3`).

> Es la "hermana densa" de la función del ejercicio 3. Tené las tres columnas a la vista al mismo tiempo: en una fila con empate vas a ver claramente cómo cada una resuelve distinto.

```sql
-- Escribir la consulta aquí
```

Comparar las tres columnas lado a lado es el punto central del tema: cuando aparece un empate, la diferencia entre las funciones queda a la vista en la misma fila.

---

### Ejercicio 5 — Quedarnos solo con el Top 3

Finalmente, filtrá el resultado para mostrar **únicamente los 3 primeros de cada subcategoría**.

> Si intentás poner la condición del ranking en el `WHERE`, PostgreSQL te dará error — las funciones de ventana se calculan **después** del `WHERE`. La salida es envolver la consulta del ejercicio anterior dentro de otra (una **subconsulta**) y aplicar el filtro `<= 3` en la consulta externa, sobre el alias del ranking. Elegí la columna de ranking que mejor se ajuste a "los 3 mejores incluyendo empates".

```sql
-- Escribir la consulta aquí
```

---

## 4. Resumen de diferencias

| Función        | Empates      | Salto de posición | Secuencia ejemplo |
| -------------- | ------------ | ----------------- | ----------------- |
| `ROW_NUMBER()` | Nunca empata | No aplica         | 1, 2, 3, 4        |
| `RANK()`       | Sí empata    | Sí salta          | 1, 2, 2, 4        |
| `DENSE_RANK()` | Sí empata    | No salta          | 1, 2, 2, 3        |

**¿Cuándo usar cada una?**

- `ROW_NUMBER` → cuando se necesita exactamente N filas sin repetición (paginación, deduplicación).
- `RANK` → rankings deportivos o comerciales donde el empate "ocupa" las posiciones siguientes.
- `DENSE_RANK` → rankings donde se quiere continuidad numérica aunque haya empates.

---

## Cierre

La diferencia entre `GROUP BY` y `PARTITION BY` es el concepto central de la sesión: ambos agrupan, pero `GROUP BY` colapsa las filas y `PARTITION BY` las conserva, permitiendo agregar y comparar sin perder el detalle. El "Top N por grupo" es el caso de uso que mejor muestra esta diferencia.
