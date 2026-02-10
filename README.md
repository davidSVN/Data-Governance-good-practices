# Data-Governance-good-practices
shows documentation of good practices when creating branches and doing PR to sandbox or production environments

1. Estrategia de Ramas (Branching Strategy)
Nunca se trabaja directamente sobre la rama main o master. Cada tarea debe tener su propia rama aislada.

1.1 Estructura de Nombres
El nombre de la rama debe ser descriptivo y vincularse automáticamente con el sistema de tickets (Jira).

Sintaxis: tipo/TICKET-ID_descripcion-corta

Tipos permitidos:

feat: Una nueva funcionalidad (ej. crear una tabla nueva, añadir una métrica).

fix: Corregir un error en producción (bug fix).

refactor: Mejorar código existente sin cambiar el resultado (limpieza).

docs: Cambios solo en documentación.

test: Añadir o modificar tests.

Ejemplos Correctos:

feat/DATA-342_tabla-retencion-mensual (Vinculado al ticket DATA-342 en Jira).

fix/REV-101_corregir-nulls-ventas.

refactor/migracion-dbt-utils.

Ejemplos Incorrectos:

juan/nueva-tabla (No dice qué ticket es).

arreglo-rapido (No dice qué arregla).

DATA-342 (Falta descripción).

2. Antes de crear el Pull Request (Pre-Work)
Antes de subir tu código y pedir revisión, debes validar localmente lo siguiente para no hacer perder tiempo a tus compañeros.

Checklist de Validación Local:
Sincronización: ¿Está tu rama actualizada con lo último de main?

Comando: git pull origin main (Resuelve conflictos en tu máquina, no en el PR).

Ejecución Exitosa: El modelo debe correr en tu entorno local (Dev) sin errores.

Comando: dbt run --select mi_modelo_nuevo

Testing Básico: Los tests primarios deben pasar.

Comando: dbt test --select mi_modelo_nuevo

Linting (Estilo): El código SQL debe seguir la guía de estilo (mayúsculas, indentación, comas).

Herramienta: SQLFluff o el linter configurado en el proyecto.

Limpieza: Borra código comentado, print statements o archivos temporales que no sirven.

3. Creación del Pull Request (PR)
El PR es la presentación formal de tu trabajo. Un PR vacío o mal descrito será rechazado automáticamente.

3.1 Título del PR
Debe seguir el mismo formato del ticket para rastreabilidad.

[DATA-342] Implementación de Tabla Canónica de Retención

3.2 Descripción (Template Obligatorio)
Todo PR debe contener las siguientes secciones en su descripción:

A. Contexto (El "Por qué"):

Explica brevemente qué problema resuelve este cambio. Enlaza el ticket de Jira aquí. Ejemplo: "Growth necesita medir la retención cohortizada por semana. Este modelo crea la tabla fct_retention."

B. Cambios Realizados (El "Qué"):

Creación de modelo int_user_orders.

Adición de tests de unicidad en user_id.

Actualización del archivo schema.yml.

C. Plan de Pruebas / Evidencia (Crucial en Datos): Adjunta capturas de pantalla o resultados de consultas que demuestren que los datos son correctos.

Screenshot 1: Resultado de dbt test pasando en verde. Screenshot 2: Query en BigQuery mostrando que no hay duplicados. Screenshot 3: Comparación de totales vs. mes anterior.

D. Checklist de Auto-Revisión:

[ ] He actualizado la documentación (.yml).

[ ] He añadido tests (unique, not_null).

[ ] El código sigue las guías de estilo SQL.

[ ] No contiene credenciales ni datos sensibles (PII).
