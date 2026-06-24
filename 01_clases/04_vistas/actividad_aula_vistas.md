# Base de Datos II (SIS-0126) — Vistas: utilidad práctica

**Tema:** Vistas (`CREATE VIEW`) — encapsular y reutilizar lógica

---

```sql
create view vw_producto as
(
select p.productid, p.name as producto
from production.product p
)

select * from vw_producto vp
```

## Ejercicio 1 — Una vista, muchas preguntas

**Contexto:** El equipo comercial pregunta lo mismo de formas distintas ("ventas por producto", "los 10 que más venden", "los que superan cierto monto"). En lugar de reescribir el join y la agregación cada vez, encapsulá el cálculo una sola vez.

**Paso 1.** Crear una vista que, por cada producto, reúna su nombre, categoría, unidades vendidas y monto total generado (cantidad por precio unitario de cada línea).

**Paso 2.** Usando únicamente la vista creada, responder:

- **a)** Los 10 productos con mayor monto vendido.
- **b)** El monto total vendido por cada categoría.
- **c)** Los productos cuyo monto vendido supere 50.000.

---

## Ejercicio 2 — Reutilizar la lógica de "cliente activo"

**Contexto:** Definir qué es un "cliente con actividad" implica un join entre cliente, persona y órdenes que se repite en muchos reportes. Encapsulalo para que el resto de consultas no tenga que repetirlo.

**Paso 1.** Crear una vista con el identificador del cliente, su nombre completo, la cantidad de órdenes que realizó y el monto total de sus compras (`totaldue` a nivel de orden). Considerar solo clientes que son personas.

**Paso 2.** Sobre la vista, responder:

- **a)** Cantidad de clientes con más de 5 órdenes.
- **b)** El cliente con mayor monto total de compras.
- **c)** Listar clientes con monto total entre 10.000 y 50.000.

---

## Ejercicio 3 — Esconder un join de varias tablas

**Contexto:** Consultar la plantilla de empleados con su departamento y turno actuales exige un join de varias tablas del esquema `humanresources` que pocos recuerdan de memoria. La vista lo resuelve una vez y deja una "tabla" simple para consultar.

**Paso 1.** Crear una vista que muestre, por cada empleado vigente, su nombre completo, puesto, departamento, turno y edad. Solo asignaciones vigentes (sin fecha de finalización).

**Paso 2.** Sobre la vista, responder:

- **a)** Cantidad de empleados por departamento.
- **b)** Empleados del turno `'Night'` mayores de 40 años.
- **c)** Edad promedio por departamento.

---

## Ejercicio 4 — Desempeño de proveedores

**Contexto:** El área de adquisiciones consulta repetidamente el gasto por proveedor. El monto de una orden de compra no es una columna directa: se calcula sumando subtotal, impuestos y flete. Encapsular ese cálculo en una vista evita reescribir la fórmula en cada reporte.

**Paso 1.** Crear una vista con el nombre del proveedor, la cantidad de órdenes de compra que registró y el monto total ordenado (suma de subtotal, impuesto y flete). Considerar solo proveedores activos.

**Paso 2.** Sobre la vista, responder:

- **a)** Los 10 proveedores con mayor monto total ordenado.
- **b)** Los proveedores con más de 20 órdenes de compra.
- **c)** El monto promedio por orden de cada proveedor.
