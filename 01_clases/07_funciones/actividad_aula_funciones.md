# Base de Datos II (SIS-0126) — Funciones Definidas por el Usuario

**Tema:** Funciones definidas por el usuario (`CREATE FUNCTION`, `RETURNS`)

---

## Introducción

Una **función definida por el usuario** (UDF) es un bloque de código con nombre, guardado en la base de datos, que recibe parámetros, ejecuta una lógica y **devuelve un resultado**. Se invoca **dentro de una consulta** (`SELECT`, `WHERE`, `ORDER BY`), no con `CALL`.

Su verdadera utilidad no está en "calcular algo una vez" —para eso basta un `SELECT`— sino en **encapsular una lógica para reutilizarla muchas veces**: aplicarla fila por fila sobre miles de registros, usarla en distintas consultas sin reescribirla, y concentrar una regla de negocio en un único lugar que, si cambia, se corrige una sola vez.

```sql
CREATE OR REPLACE FUNCTION nombre_funcion(parametro tipo)
RETURNS tipo_retorno
LANGUAGE plpgsql
AS $$
DECLARE
    -- variables locales (opcional)
BEGIN
    -- lógica
    RETURN valor;
END;
$$;
```

### Procedimiento vs. función

| Aspecto        | Procedimiento                     | Función                          |
| -------------- | --------------------------------- | -------------------------------- |
| Devuelve valor | No (opcional, vía parámetros)     | Sí, siempre (`RETURNS`)          |
| Se invoca con  | `CALL`                            | Dentro de un `SELECT`            |
| Uso típico     | Ejecutar acciones (INSERT/UPDATE) | Calcular y devolver un resultado |

---

## Ejercicio 1

El área de cobranzas necesita saber la fecha de vencimiento de cada orden: un plazo de meses contado desde la fecha de la orden. Este cálculo se usa en los recordatorios de pago y en los reportes de morosidad, y el plazo puede variar según la política comercial.

Crear una función que reciba la fecha de una orden y una cantidad de meses de plazo, y devuelva la fecha de vencimiento resultante. Luego, usándola, listar las órdenes mostrando su identificador, la fecha de orden y su fecha de vencimiento a 3 meses, ordenadas por fecha de orden descendente.

```sql
create or replace function sales.fecha_vencimiento(
	p_fecha_orden date,
	p_mes_plazo int
)
returns date
language plpgsql
as $$
begin
	return (p_fecha_orden + (p_mes_plazo || 'months')::interval)::date;
end;
$$

select s.salesorderid ,
s.orderdate::date as fecha_orden,
sales.fecha_vencimiento(s.orderdate::date,15)
from sales.salesorderheader s

```

## Ejercicio 2

La gerencia define a un cliente como "Premium" (>3.000 al año), "Regular" (entre 620 y 3.000), "Básico" (>0) o "Sin compras". Esta clasificación se necesita en el reporte de marketing, en el de fidelización y en el de ventas.

Crear una función que reciba un cliente y un año y devuelva su categoría según esos rangos. Luego, usándola, listar todos los clientes con su nombre completo y su categoría para un año dado, ordenados por categoría.

```sql
create or replace function sales.categoria_cliente(
	p_cliente_id int,
	p_anio int
)
returns text
language plpgsql
as $$
declare
	v_total numeric;
begin
	select coalesce(sum(s.totaldue),0) into v_total
	from sales.salesorderheader s
	where s.customerid=p_cliente_id
	and extract(year from s.orderdate)= p_anio;

	return case
	when v_total = 0 then 'Sin compras'
	when v_total >300 then 'Premium'
	when v_total >= 620 then 'Regular'
	else 'Basico' end;
end;
$$


select s.customerid ,
	sales.categoria_cliente(s.customerid,2013) as categoria
from sales.salesorderheader s
```

## Ejercicio 3

El equipo comercial necesita identificar a sus mejores clientes de un año: quiere **filtrar** solo a los que superan cierto monto de compra y **ordenarlos** por ese mismo monto.

Crear una función que devuelva el total comprado por un cliente en un año. Luego, usándola, listar los clientes cuyo total anual supere los 3.000, mostrando nombre y total, ordenados de mayor a menor —usando la función tanto en el filtro como en el ordenamiento.

```sql

```

## Ejercicio 4

El área de adquisiciones consulta seguido "los productos de tal categoría más vendidos en tal año", cambiando la categoría y el año cada vez. En lugar de reescribir el reporte completo en cada consulta, quieren un reporte que se invoque pasándole esos dos datos.

Crear una función que reciba una categoría de producto y un año y devuelva una tabla con los productos de esa categoría y la cantidad vendida en ese año, ordenada de mayor a menor. Luego, demostrar su uso invocándola para dos categorías distintas.

```sql

```
