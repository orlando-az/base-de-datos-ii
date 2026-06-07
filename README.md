# Base de Datos II — SIS-0126

Repositorio oficial de la asignatura **Base de Datos II**, correspondiente al quinto semestre de la carrera de Ingeniería de Sistemas, Facultad de Ingeniería — Universidad Privada Domingo Savio, Tarija, Bolivia.

---

## Docente

**Orlando Isaac Aguilera Zambrana**  
Ingeniería de Sistemas · UPDS Tarija  
tj.orlando.aguilera.z@upds.net.bo

---

## Stack tecnológico

| Herramienta       | Versión         | Uso                              |
| ----------------- | --------------- | -------------------------------- |
| PostgreSQL        | 16+             | Motor principal de base de datos |
| DBeaver Community | Última estable  | Cliente SQL e IDE                |
| AdventureWorks    | Para PostgreSQL | Base de datos de práctica        |

---

## Estructura del repositorio

```
Base_de_Datos_II/
│
├── 01_clases/              # Scripts desarrollados en clase por el docente
│   ├── 01_basico/
│   ├── 02_joins/
│   ├── 03_vistas_indices/
│   ├── 04_subqueries/
│   ├── 05_ctes/
│   ├── 06_funciones/
│   ├── 07_procedimientos/
│   └── 08_triggers/
│
├── 02_laboratorios/        # Consignas de laboratorio para los estudiantes
│   ├── lab_01_basico/
│   ├── lab_02_joins/
│   ├── lab_03_vistas_indices/
│   ├── lab_04_subqueries/
│   ├── lab_05_ctes/
│   ├── lab_06_funciones/
│   ├── lab_07_procedimientos/
│   └── lab_08_triggers/
│
├── 03_database/            # Scripts de base de datos
│   ├── esquemas/
│   ├── datos/
│   └── backup/
│
├── 04_evaluaciones/        # Instrumentos de evaluación
│   ├── diagnostica/
│   └── examen_final/
│
├── 05_proyecto_final/      # Proyecto final de la asignatura
│   └── fase1/
│
└── 06_recursos/            # Material de referencia y guías
```

---

## Contenidos del módulo

| Bloque       | Tema                                   | Sesiones |
| ------------ | -------------------------------------- | -------- |
| Introducción | Presentación, repaso DER, repaso DDL   | 1 – 3    |
| Bloque 2     | SQL simple, SQL avanzado, Vistas, CTEs | 4 – 12   |
| Bloque 3     | Procedimientos, Funciones, Triggers    | 13 – 17  |
| Cierre       | Proyecto final, Evaluación             | 18 – 20  |

---

## Convenciones de los scripts SQL

- Palabras reservadas SQL en `MAYÚSCULAS`.
- Nombres de objetos en `snake_case`.
- Esquema explícito en todas las referencias (`public.tabla`).
- Comentarios en español.
- Encabezado obligatorio en cada script:

```sql
-- ============================================================
-- Asignatura : Base de Datos II (SIS-0126)
-- Tema       :
-- Docente    : Orlando Isaac Aguilera Zambrana
-- Fecha      :
-- ============================================================
```

---

## Módulo 5/1/2026

**Duración:** 20 sesiones · 8 de junio – 3 de julio de 2025  
**Horario:** Lunes a viernes · 3 horas por sesión  
**Total:** 100 horas (40 teóricas + 60 prácticas)
