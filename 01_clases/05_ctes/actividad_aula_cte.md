# Base de Datos II (SIS-0126) — CTEs (Common Table Expressions)

**Tema:** Expresiones de tabla comunes (`WITH`)

---

## Introducción

Una **expresión de tabla común** (CTE, _Common Table Expression_) es un conjunto de resultados temporal y con nombre que se define con la cláusula `WITH` justo antes de la consulta principal y que existe solo durante la ejecución de esa consulta. En la práctica, es como darle un nombre a una subconsulta para poder referenciarla después como si fuera una tabla.

Su forma básica es:

```sql
WITH nombre_cte AS (
    SELECT ...      -- consulta que produce el resultado temporal
)
SELECT ...
FROM nombre_cte;    -- la consulta principal usa el CTE como si fuera una tabla
```

### ¿Para qué sirven?

- **Descomponer un problema en pasos.** Un cálculo complejo se parte en bloques nombrados que se leen de arriba hacia abajo, en lugar de anidar subconsultas una dentro de otra.
- **Reutilizar un resultado dentro de la misma consulta.** Un CTE puede referenciarse varias veces en la consulta principal sin tener que reescribir su lógica.
- **Encadenar cálculos.** Un CTE puede apoyarse en el resultado de otro CTE definido antes, formando una secuencia de pasos.
- **Habilitar patrones con funciones de ventana.** Como una función de ventana no puede usarse directamente en el `WHERE`, el CTE permite calcularla primero y filtrar después (por ejemplo, "top N por grupo").

### Ventajas frente a las subconsultas anidadas

- **Legibilidad:** la lógica se lee en orden lineal, no de adentro hacia afuera.
- **Mantenibilidad:** cada paso tiene un nombre descriptivo y se puede ajustar de forma aislada.
- **Menos repetición:** lo que con subconsultas habría que escribir dos veces, con un CTE se define una sola vez.
- **Depuración más simple:** se puede probar cada CTE por separado para verificar resultados intermedios.

---

## Ejercicio 1

La gerencia quiere ver la evolución de las ventas año a año. En un CTE, reunir las órdenes con el año extraído de su fecha y el monto de total de cada una. En la consulta principal, calcular el total de ventas de cada año, mostrando el año y el monto total, ordenado de mayor a menor.

```sql
with detalle as(
select
extract(year from s.orderdate) as anio,
extract(month from s.orderdate) as mes,
s.totaldue as total
from sales.salesorderheader s
)
select anio,mes, sum(total) as total_anual
from detalle
group by anio,mes
order by total_anual desc

```

## Ejercicio 2

Recursos Humanos necesita un listado de los empleados que aún se encuentran activos (asignación vigente, sin fecha de finalización). En un CTE, reunir a esos empleados con su fecha de inicio; en la consulta principal, mostrar su identificador, la fecha de inicio y la cantidad de días trabajados hasta la fecha actual, ordenados de mayor a menor antigüedad.

```sql
with antiguedad as (
select e.businessentityid as id_empleado,
	e.startdate as fecha_inicio,
 e.enddate as fecha_fin
from humanresources.employeedepartmenthistory e
),
datos as (
select id_empleado,
(current_date - fecha_inicio) as dias_antiguedad
from antiguedad
where fecha_fin is null
)
select datos.id_empleado,datos.dias_antiguedad,
	case
		when dias_antiguedad between 3000 and 4000 then 'Nuevos'
		when dias_antiguedad between 4001 and 5000 then 'Regulares'
		when dias_antiguedad > 5000 then 'Antiguos'
	end
from datos
```

## Ejercicio 3

Se quiere comparar el gasto de cada cliente contra el promedio general de gasto. En un primer CTE, calcular el monto total comprado por cada cliente (`totaldue` a nivel de orden). En un segundo CTE, calcular el promedio de esos montos entre todos los clientes. En la consulta principal, listar los clientes cuyo gasto total supere ese promedio, mostrando nombre completo y monto.

```sql
with total_cliente as (
	select s.customerid , p.firstname ,p.lastname , sum(s.totaldue)  as total
	from sales.salesorderheader s
	inner join sales.customer c on c.customerid = s.customerid
	inner join person.person p on p.businessentityid= c.personid
	group by s.customerid,p.firstname ,p.lastname
),
promedio_cliente as (
 select Round(avg(s.totaldue ),3) as promedio
 from sales.salesorderheader s
)
select total_cliente.firstname ,total_cliente.lastname  , total,promedio
from total_cliente , promedio_cliente
where total_cliente.total > promedio_cliente.promedio
```

## Ejercicio 4

El área de ventas quiere comparar el desempeño de cada vendedor contra su cuota asignada. En un CTE, calcular el monto total vendido por cada vendedor (`totaldue` a nivel de orden, agrupado por vendedor). En la consulta principal, unir ese resultado con la tabla de vendedores para mostrar el nombre del vendedor, su monto vendido y su cuota de ventas, indicando además la diferencia entre ambos. Considerar solo las órdenes que tienen vendedor asignado.

```sql

```

## Ejercicio 5

Se necesita el top 3 de productos más vendidos dentro de cada subcategoría. En un CTE, calcular la cantidad vendida por producto y asignarle un ranking dentro de su subcategoría según esa cantidad (de mayor a menor). En la consulta principal, mostrar únicamente los productos cuyo ranking sea menor o igual a 3, con nombre del producto, subcategoría, cantidad vendida y posición.

```sql

```

## Ejercicio 6

La dirección comercial quiere saber qué vendedores rinden por encima del promedio de su propio territorio. En un CTE, calcular el monto total vendido por cada vendedor junto a su territorio, y agregar el promedio de ventas de su territorio usando una función de ventana (sin agrupar, manteniendo una fila por vendedor). En la consulta principal, mostrar el territorio, el nombre del vendedor, su monto vendido y el promedio del territorio, listando únicamente a los vendedores cuyo monto supere ese promedio.

```sql

```
