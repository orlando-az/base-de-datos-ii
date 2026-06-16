# Laboratorio — Consultas Multitabla sobre AdventureWorks

**Nombre:** **********\*\***********\_\_\_**********\*\***********

---

## Ejercicio 1

Se necesita conocer quiénes realizaron las compras más altas registradas en el sistema. Obtén las 15 órdenes completadas (`status = 5`) con mayor total a pagar, mostrando el nombre completo del cliente, el ID de orden y el monto. Considera únicamente clientes registrados como personas naturales.

**Tablas:** `sales.salesorderheader`, `sales.customer`, `person.person`

## Ejercicio 2

El área de ventas necesita revisar el detalle completo de la orden `43659`. Muestra cada producto vendido en esa orden junto con su subcategoría, su categoría y el valor bruto de la línea calculado como `orderqty * unitprice`. Incluye únicamente las líneas cuya cantidad sea mayor o igual a 2 y cuyo valor bruto supere 500.

**Tablas:** `sales.salesorderdetail`, `production.product`, `production.productsubcategory`, `production.productcategory`

## Ejercicio 3

Se requiere identificar a los clientes que realizaron compras enviadas a 'Seattle', 'Portland' o 'Dallas' durante el primer semestre de 2013. Muestra el nombre completo del cliente y la ciudad de destino. Considera únicamente clientes registrados como personas naturales.

**Tablas:** `sales.salesorderheader`, `sales.customer`, `person.person`, `person.address`

## Ejercicio 4

El departamento comercial necesita revisar las compras más representativas de clientes de tipo tienda durante el primer trimestre de 2013. Lista las órdenes cuyo subtotal supere 1000, mostrando el nombre de la tienda, el ID de orden, la fecha y el subtotal. Considera solo clientes que tengan tienda asociada.

**Tablas:** `sales.salesorderheader`, `sales.customer`, `sales.store`

## Ejercicio 5

Para entender qué subcategorías concentran más movimiento, cuenta las líneas de venta por subcategoría. Considera solo las líneas con precio unitario mayor a 50 y muestra únicamente las subcategorías que superen las 200 líneas vendidas. Ordena el resultado de mayor a menor.

**Tablas:** `sales.salesorderdetail`, `production.product`, `production.productsubcategory`

## Ejercicio 6

El área de fidelización necesita identificar a los clientes más valiosos del periodo 2012–2014. Muestra el nombre completo, el correo electrónico y el gasto total de cada cliente, considerando solo las órdenes realizadas en ese rango de años. Incluye únicamente a los clientes cuyo gasto total supere 80 000.

**Tablas:** `sales.salesorderheader`, `sales.customer`, `person.person`, `person.emailaddress`

## Ejercicio 7

La gerencia comercial requiere un reporte de los vendedores con órdenes asignadas, mostrando su nombre, el monto total vendido y una clasificación: 'Alto' si supera 5 000 000, 'Medio' entre 1 000 000 y 5 000 000, y 'Bajo' por debajo de 1 000 000. Incluye solo a los vendedores cuyo total vendido sea mayor a 500 000.

**Tablas:** `sales.salesorderheader`, `sales.salesperson`, `person.person`

## Ejercicio 8

El equipo de despacho necesita un listado de las órdenes completadas (`status = 5`) realizadas desde el 1 de enero de 2014, con la dirección de envío completa. Muestra el ID de orden, la fecha, la primera línea de dirección y la segunda línea; si la segunda línea no existe, mostrar 'Sin detalle'. Considera solo órdenes cuyo flete (`freight`) sea mayor a 10.

**Tablas:** `sales.salesorderheader`, `person.address`

## Ejercicio 9

Para analizar la evolución del negocio, construye un reporte que muestre el total vendido (`totaldue`) por categoría de producto en los años 2012, 2013 y 2014, cada uno en una columna separada. Muestra solo las categorías cuyo total acumulado en los tres años supere 500 000.

**Tablas:** `sales.salesorderheader`, `sales.salesorderdetail`, `production.product`, `production.productsubcategory`, `production.productcategory`

## Ejercicio 10

El equipo de pricing quiere conocer la dispersión de precios reales de venta por categoría. Calcula el precio unitario mínimo, máximo y promedio de las líneas de detalle, considerando solo líneas con precio mayor a 0 y cantidad menor o igual a 10. Muestra únicamente las categorías con más de 100 líneas que cumplan esas condiciones.

**Tablas:** `sales.salesorderdetail`, `production.product`, `production.productsubcategory`, `production.productcategory`

## Entregable

Subir a la plataforma **Moodle** un único archivo con el siguiente formato de nombre:

```
laboratorio_4.sql
```

El archivo debe contener las 11 consultas SQL. Cada ejercicio debe incluir obligatoriamente la consigna copiada como comentario, seguida de la consulta. Ejemplo:

```sql
-- Ejercicio 1
-- Se necesita conocer quiénes realizaron las compras más altas...
SELECT ...

-- Ejercicio 2
-- El área de ventas necesita revisar el detalle completo...
SELECT ...
```
