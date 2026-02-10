# Data-Governance-good-practices
shows documentation of good practices when creating branches and doing PR to sandbox or production environments

# Data Engineering & DataOps Protocols

Este repositorio documenta los estándares de ingeniería, estrategias de ramificación (branching) y protocolos de Pull Request (PR) que definen un flujo de trabajo de **DataOps** robusto y escalable.

El objetivo es estandarizar el ciclo de vida del desarrollo de datos, garantizando la calidad, la reproducibilidad y la integración continua (CI/CD).

---

## 1. Estrategia de Ramas (Branching Strategy)

Nunca se trabaja directamente sobre la rama `main` o `master`. Cada unidad de trabajo debe tener su propia rama aislada derivada de la última versión estable.

### Estructura de Nombres
El nombre de la rama debe ser descriptivo y, preferiblemente, vincularse automáticamente con el sistema de seguimiento de tickets (ej. Jira).

**Sintaxis:**
`tipo/TICKET-ID_descripcion-corta`

**Tipos permitidos:**

| Tipo | Uso | Ejemplo |
| :--- | :--- | :--- |
| `feat` | Nueva funcionalidad (tabla, métrica, modelo) | `feat/DATA-342_tabla-retencion` |
| `fix` | Corrección de errores en producción | `fix/REV-101_corrige-nulls` |
| `refactor` | Mejoras de código sin cambiar lógica | `refactor/migracion-dbt-utils` |
| `docs` | Cambios solo en documentación | `docs/actualizar-readme` |
| `test` | Adición o modificación de pruebas | `test/add-unit-tests` |

> **❌ Incorrecto:** `juan/nueva-tabla`, `arreglo-rapido`, `DATA-342` (sin descripción).

---

## 2. Checklist de Pre-Trabajo (Local)

Antes de crear un Pull Request, el ingeniero debe validar localmente los siguientes puntos para asegurar que el pipeline de CI/CD no falle innecesariamente.

1.  **Sincronización:**
    ```bash
    git checkout main
    git pull origin main
    git checkout mi-rama
    git merge main
    ```
2.  **Ejecución Exitosa:** El modelo debe compilar y correr en el entorno de desarrollo (Dev).
    ```bash
    dbt run --select mi_modelo_nuevo
    ```
3.  **Testing Básico:** Los tests de esquema (`unique`, `not_null`) deben pasar.
    ```bash
    dbt test --select mi_modelo_nuevo
    ```
4.  **Linting (Estilo SQL):** El código debe seguir la guía de estilo (SQLFluff).
5.  **Limpieza:** Eliminar código comentado, `print statements` o archivos temporales/csv locales.

---

## 3. Protocolo de Pull Request (PR)

El PR es la presentación formal del trabajo. Un PR sin contexto o evidencia será cerrado.

### Título del PR
Debe seguir el formato del ticket para rastreabilidad automática:
`[DATA-342] Implementación de Tabla Canónica de Retención`

### Descripción del PR (Template)

Al abrir un PR, se debe completar la siguiente plantilla:

#### A. Contexto (El "Por qué")
> Explica brevemente qué problema de negocio resuelve este cambio. Enlaza el ticket de Jira aquí.
> *Ejemplo: "Growth necesita medir la retención cohortizada por semana. Este modelo crea la tabla `fct_retention`."*

#### B. Cambios Realizados (El "Qué")
- [ ] Creación de modelo `int_user_orders`.
- [ ] Adición de tests de unicidad en `user_id`.
- [ ] Actualización del archivo `schema.yml`.

#### C. Plan de Pruebas / Evidencia
Adjunta capturas de pantalla o resultados de consultas que demuestren la integridad de los datos.
> *Screenshot 1: Resultado de `dbt test` pasando en verde.*
> *Screenshot 2: Query en BigQuery mostrando la eliminación de duplicados.*

#### D. Checklist de Auto-Revisión
- [ ] He actualizado la documentación (`.yml` / `description`).
- [ ] He añadido tests (`unique`, `not_null`, `accepted_values`).
- [ ] El código sigue las guías de estilo SQL (Capitalización, indentación).
- [ ] No contiene credenciales ni datos sensibles (PII).

---

## 4. Estándares de Code Review

### Para el Revisor
* **Foco:** Priorizar lógica de negocio, arquitectura, seguridad y rendimiento (performance) sobre estilo (dejamos el estilo a los linters).
* **Constructividad:** Sugerir optimizaciones específicas (ej. "Usa una CTE aquí en lugar de subquery").
* **SLA:** Los PRs deben revisarse en un plazo máximo de 24 horas hábiles.

### Para el Autor
* **Responsabilidad:** Responder a todos los comentarios.
* **Merge:** Solo el autor realiza el merge, y solo cuando:
    1.  Tiene al menos 1 aprobación (Approve).
    2.  Todos los checks del CI (GitHub Actions) están en verde.

---

*Documento mantenido por el equipo de Data Engineering*
