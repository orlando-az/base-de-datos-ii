# Base de Datos II (SIS-0126) — Procedimientos Almacenados

**Tema:** Procedimientos almacenados (`CREATE PROCEDURE`, `CALL`)

---

## Introducción

Un **procedimiento almacenado** es un bloque de código con nombre, guardado en la base de datos, que agrupa una o varias instrucciones para ejecutarlas como una sola unidad. A diferencia de una función, un procedimiento **no está obligado a devolver un valor**: su propósito habitual es **ejecutar acciones** (insertar, actualizar, eliminar, validar) en lugar de calcular y retornar un resultado.

Su forma básica en PostgreSQL es:

```sql
CREATE OR REPLACE PROCEDURE nombre_procedimiento(parametro1 tipo, parametro2 tipo)
LANGUAGE plpgsql
AS $$
DECLARE
    -- variables locales (opcional)
BEGIN
    -- instrucciones
END;
$$;
```

Y se ejecuta con:

```sql
CALL nombre_procedimiento(valor1, valor2);
```

### Nota sobre tablas con ID no autoincremental

Algunas tablas importadas conservan su clave primaria con valores cargados manualmente, sin la propiedad de autogeneración. Al insertar sin indicar el ID, la base intenta repetir un valor existente y se produce un error de clave duplicada. Si la columna tiene una secuencia asociada, se la puede sincronizar con el valor máximo actual para que continúe a partir de ahí:

```sql
SELECT setval(
    pg_get_serial_sequence('esquema.nombre_tabla', 'columna_id'),
    (SELECT MAX(columna_id) FROM esquema.nombre_tabla)
);
```

> Esto reposiciona la secuencia después del mayor ID existente. Si la columna no tiene secuencia asociada, una alternativa dentro del propio procedimiento es calcular el siguiente valor con `COALESCE(MAX(columna_id), 0) + 1` antes de insertar.

### Elementos que se usan en este avance

- **Parámetros de entrada:** valores que recibe el procedimiento al ser llamado.
- **`RAISE NOTICE`:** muestra un mensaje informativo durante la ejecución.
- **`RETURNING ... INTO`:** captura un valor generado por un `INSERT` (por ejemplo, un ID autogenerado) dentro de una variable.
- **Lógica condicional (`IF / ELSE`):** ejecuta acciones distintas según una condición.
- **Manejo de excepciones (`EXCEPTION`):** captura errores en tiempo de ejecución y permite responder de forma controlada en lugar de abortar abruptamente.

---

## Ejercicio 1

El área de catálogo necesita registrar nuevas ubicaciones de inventario sin escribir el INSERT manualmente cada vez. Crear un procedimiento que reciba el nombre de una ubicación y la registre en la tabla de ubicaciones de producción, mostrando al final un mensaje que confirme el registro.

```sql
create or replace procedure production.registrar_ubicacion(
	p_nombre text
)
language plpgsql
as $$
begin
	insert into
	production.location(name,costrate,availability,modifieddate)
	values
	(p_nombre,0,0,current_timestamp);

	raise notice 'Registro de ubicacion: %',p_nombre;
end;
$$

call production.registrar_ubicacion('Central Oruro');
```

## Ejercicio 2

Al registrar una ubicación, el equipo quiere conocer de inmediato el identificador que la base le asignó automáticamente. Crear un procedimiento que reciba el nombre de una ubicación, la inserte y capture el identificador generado, mostrándolo en un mensaje de confirmación.

```sql
create or replace procedure production.registrar_ubicacion_id(
	p_nombre text
)
language plpgsql
as $$
declare
	v_id int;
begin
	insert into
	production.location(name,costrate,availability,modifieddate)
	values(p_nombre,0,0,current_timestamp)
	returning locationid into v_id;

	raise notice
	'Registro de ubicacion: % , con el ID: %',p_nombre,v_id;
end;
$$

call production.registrar_ubicacion_id('Central Pando')
```

## Ejercicio 3

El área de producción necesita actualizar el costo estándar de una ubicación de inventario, pero solo si la ubicación existe. Crear un procedimiento que reciba el identificador de una ubicación y un nuevo costo: si la ubicación existe, actualizar su costo y mostrar un mensaje de éxito; si no existe, mostrar un mensaje indicando que no se encontró.

```sql

```

## Ejercicio 4

El procedimiento de actualización de costo debe protegerse ante valores inválidos. Crear un procedimiento que reciba el identificador de una ubicación y un nuevo costo, y que rechace la operación cuando el costo sea negativo, lanzando un error controlado con un mensaje claro. Además, capturar cualquier otro error inesperado mediante manejo de excepciones, informando que la operación no pudo completarse.

```sql

```
