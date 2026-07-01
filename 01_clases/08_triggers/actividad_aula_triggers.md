# Base de Datos II (SIS-0126) — Triggers (Disparadores)

**Tema:** Triggers (`CREATE TRIGGER`, funciones de trigger `RETURNS TRIGGER`)

---

## Introducción

Un **trigger** (disparador) es un mecanismo que ejecuta automáticamente una función cuando ocurre un evento sobre una tabla: un `INSERT`, `UPDATE` o `DELETE`. A diferencia de un procedimiento o una función, el trigger **no se invoca manualmente**: la base de datos lo dispara sola cada vez que el evento ocurre, sin que el usuario tenga que acordarse de llamarlo.

Un trigger se arma en dos partes:

**1. La función de trigger**, que contiene la lógica y siempre devuelve `TRIGGER`:

```sql
CREATE OR REPLACE FUNCTION nombre_funcion()
RETURNS TRIGGER
LANGUAGE plpgsql
AS $$
BEGIN
    -- lógica, usando NEW y/o OLD
    RETURN NEW;   -- o RETURN OLD, o RETURN NULL
END;
$$;
```

**2. El trigger propiamente dicho**, que asocia la función a un evento de una tabla:

```sql
CREATE TRIGGER nombre_trigger
BEFORE INSERT OR UPDATE ON esquema.tabla
FOR EACH ROW
EXECUTE FUNCTION nombre_funcion();
```

### Elementos clave

- **`BEFORE` vs. `AFTER`:** `BEFORE` se ejecuta antes de que el cambio se aplique (permite modificar o rechazar la fila); `AFTER` se ejecuta una vez que el cambio ya ocurrió (útil para registrar lo sucedido, no para modificarlo).
- **`NEW`:** representa la fila tal como queda después del `INSERT` o `UPDATE`. Disponible en `BEFORE`/`AFTER INSERT` y `BEFORE`/`AFTER UPDATE`.
- **`OLD`:** representa la fila tal como estaba antes del cambio. Disponible en `UPDATE` y `DELETE`.
- **`RETURN NEW`:** en un trigger `BEFORE`, permite modificar los valores que se van a guardar antes de que se escriban.

---

## Ejercicio 1

El área de producción quiere asegurarse de que ninguna ubicación de inventario quede con un costo negativo, sin depender de que cada persona que inserte datos recuerde validarlo a mano.

Crear una función de trigger que, antes de insertar o actualizar un registro en `production.location`, verifique si `costrate` es menor que 0 y, en ese caso, lo ajuste automáticamente a 0. Luego, crear el trigger que ejecute esta función en cada fila.

```sql
create or replace function production.validar_costo()
returns trigger
language plpgsql
as $$
	begin
		if new.costrate <0 then
			new.costrate := 0;
		end if;
		return new;
	end;
$$

create trigger tg_validar_costo
before insert or update on production.location
for each row
execute function production.validar_costo()

insert into production."location" (name,costrate ,availability,modifieddate)
values('prueba ubicacion',-15,0,current_timestamp)

update production."location"  set costrate = -15 where locationid=10
```

## Ejercicio 2

El equipo de calidad de datos quiere impedir que se registre una ubicación sin nombre, en lugar de descubrirlo después en un reporte con datos vacíos.

Crear una función de trigger que, antes de insertar un registro en `production.location`, verifique si el campo `name` es nulo o está vacío y, en ese caso, impida la inserción lanzando un error con un mensaje claro. Luego, crear el trigger correspondiente.

```sql
create or replace function production.validar_nombre()
returns trigger
language plpgsql
as $$
begin
	if new.name is null or trim(new.name) = '' then
	 raise exception 'El nombre de la ubicacion no puede estar vacio';
	end if;

	return new;
end;
$$

create trigger tg_validar_nombre
before insert or update on production.location
for each row
execute function production.validar_nombre()

insert into production."location" (name,costrate,availability,modifieddate)
values('',150,0,current_timestamp)

select * from production."location" l
```

## Ejercicio 3

El área de auditoría quiere llevar un registro de cada ubicación nueva que se da de alta, sin tener que confiar en que cada procedimiento de inserción lo anote manualmente.

Crear una tabla `production.location_audit` con columnas para el identificador de la ubicación, su nombre y la fecha y hora del registro. Crear una función de trigger que, después de insertar un registro en `production.location`, inserte automáticamente una fila en `production.location_audit` con esos datos. Luego, crear el trigger correspondiente.

```sql

```

## Ejercicio 4

El área de producción quiere saber cada vez que cambia el costo de una ubicación, para detectar ajustes de precio sin revisar manualmente el historial.

Crear una función de trigger que, después de actualizar un registro en `production.location`, verifique si el valor de `costrate` cambió y, en ese caso, muestre un mensaje indicando el costo anterior y el nuevo. Luego, crear el trigger correspondiente.

```sql

```
