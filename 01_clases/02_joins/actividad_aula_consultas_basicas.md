# Laboratorio 3 — Sistema de Gestión de Condominio

---

## Ejercicio 1

Listar todos los departamentos del piso 2 o 3 mostrando su número, piso y superficie, ordenados por piso ascendente y superficie descendente.

```sql

select d.numero, d.piso, d.superficie_m2
from departamento d
where d.piso=2 or d.piso=3;

```

---

## Ejercicio 2

Mostrar los propietarios cuyo nombre contenga la letra 'o' y tengan teléfono registrado, ordenados alfabéticamente.

```sql

select p.nombre,p.telefono
from propietario p
where p.nombre Ilike 'c%' and p.telefono is not null
order by p.nombre asc;

```

---

## Ejercicio 3

Listar las expensas con estado 'pendiente' o 'vencida' cuyo monto esté entre 400 y 700 Bs, mostrando id de departamento, monto, fecha de vencimiento y estado, ordenadas por fecha de vencimiento ascendente.

```sql

select e.id_departamento ,e.monto ,e.fecha_vencimiento,e.estado
from expensa e
where e.estado in ('pendiente','vencida')
and e.monto between 400 and 700
order by e.fecha_vencimiento;

```

---

## Ejercicio 4

Mostrar los pagos cuyo monto pagado sea mayor a 400 Bs y menor a 650 Bs, realizados con método 'transferencia' o 'QR', ordenados por fecha descendente.

```sql

select  *
from pago p
where (p.monto_pagado >= 400 and p.monto_pagado <=650)
and (p.metodo_pago ='transferencia' or p.metodo_pago ='QR')
order by p.fecha_pago desc;

```

---

## Ejercicio 5

Listar número de departamento, piso, superficie y nombre del condominio para departamentos del piso 3 o con superficie menor a 60 m², ordenados por nombre del condominio ascendente.

```sql

select d.numero , c.nombre
from departamento d, condominio c
where d.id_condominio = c.id ;

select d.numero ,d.piso,d.superficie_m2  ,c.nombre
from departamento d
inner join condominio c on c.id= d.id_condominio
where d.piso =3 and d.superficie_m2 <= 65
order by c.nombre;

```

---

## Ejercicio 6

Mostrar todos los propietarios y — si tienen — sus departamentos con el nombre del condominio. Los propietarios sin departamentos deben aparecer con NULL.

```sql

select * from propietario p
insert into propietario(nombre,ci,telefono)
values('Juan Flores','58469125','78963512')

select p.nombre ,d.numero ,c.nombre
from propietario p
left join departamento d on p.id =d.id_propietario
left join condominio c on c.id =d.id_condominio

```

---

## Ejercicio 7

Mostrar todas las expensas y — si tienen — sus pagos registrados, incluyendo número de departamento y nombre del condominio. Las expensas sin pagos deben aparecer con NULL en los campos del pago.

```sql



```

---

## Ejercicio 8

Mostrar nombre del inquilino, número de departamento, nombre del condominio, nombre del propietario y fecha de inicio y fin, solo para alquileres que ya hayan finalizado (activo = FALSE), ordenados por fecha de fin descendente.

```sql



```

---

## Ejercicio 9

Contar cuántas expensas tiene cada departamento por estado, mostrando número de departamento, nombre del condominio, estado y cantidad, ordenado por cantidad descendente.

```sql



```

---

## Ejercicio 10

Mostrar nombre del condominio, número de departamento, nombre del propietario, nombre del inquilino activo y el total de expensas pendientes del departamento, solo para condominios ubicados en La Paz.

```sql



```
