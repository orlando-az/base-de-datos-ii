# Construcción de base de datos: AdmiCasas

**Asignatura:** Base de Datos II — SIS-0126 | **Sesión:** 3 | **Docente:** Orlando Isaac Aguilera

---

## Caso de estudio

AdmiCasas es una empresa que administra condominios en la ciudad.
Cada condominio tiene varios departamentos, cada departamento pertenece
a un propietario y puede ser ocupado por un inquilino. Mensualmente
se generan expensas por departamento y los propietarios registran
sus pagos.

> Durante la demostración replicar en DBeaver cada sentencia que desarrolle el docente.

---

## Requerimientos

### 1. Esquema

Crear el esquema `admicasas`.

---

### 2. Tablas

| Entidad        | Atributos                                           |
| -------------- | --------------------------------------------------- |
| `Condominio`   | id, nombre, direccion, num_pisos                    |
| `Departamento` | id, numero, piso, superficie_m2                     |
| `Propietario`  | id, nombre, ci, telefono                            |
| `Inquilino`    | id, nombre, ci, telefono                            |
| `Expensa`      | id, monto, fecha_emision, fecha_vencimiento, estado |
| `Pago`         | id, fecha_pago, monto_pagado, metodo_pago           |

---

### 3. Restricciones de integridad

| Entidad        | Restricción                                                                   |
| -------------- | ----------------------------------------------------------------------------- |
| `Condominio`   | `nombre` no puede ser nulo                                                    |
|                | `num_pisos` debe ser mayor a 0                                                |
| `Departamento` | `numero` no puede ser nulo                                                    |
|                | `piso` debe ser mayor o igual a 0                                             |
|                | `superficie_m2` debe ser mayor a 0                                            |
|                | La combinación de `id_condominio` y `numero` debe ser única                   |
| `Propietario`  | `nombre` no puede ser nulo                                                    |
|                | `ci` no puede ser nulo ni repetirse entre registros                           |
| `Inquilino`    | `nombre` no puede ser nulo                                                    |
|                | `ci` no puede ser nulo ni repetirse entre registros                           |
| `Expensa`      | `monto` debe ser mayor a 0                                                    |
|                | `estado` solo puede tomar los valores `pendiente`, `pagada` o `vencida`       |
|                | `estado` tiene como valor por defecto `pendiente`                             |
|                | `fecha_vencimiento` debe ser posterior a `fecha_emision`                      |
| `Pago`         | `monto_pagado` debe ser mayor a 0                                             |
|                | `metodo_pago` solo puede tomar los valores `efectivo`, `transferencia` o `QR` |

---

### 4. Relaciones

Definir las relaciones entre tablas mediante claves foráneas.
Tener en cuenta el orden de creación según las dependencias.
