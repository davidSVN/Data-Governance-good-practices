# Advanced Data Governance & Security Architecture

Este documento define los estándares técnicos para la gobernanza de datos, seguridad y observabilidad dentro de nuestra arquitectura de datos (Modern Data Stack).

**Filosofía:** "Governance as Code".
Nos alejamos de la gobernanza burocrática basada en documentos estáticos para adoptar una gobernanza automatizada, integrada en el pipeline de CI/CD, donde las políticas de calidad y seguridad se ejecutan y validan programáticamente.

---

## 1. Data Observability & Reliability Engineering (SRE for Data)

Tratamos los datos con la misma disciplina que el software en producción, monitoreando la salud del pipeline en tiempo real.

### 1.1 Pilares de Observabilidad
Implementamos monitoreo activo sobre los siguientes vectores:

* **Freshness (Frescura):**
    * *Definición:* Tiempo transcurrido desde la generación del evento hasta su disponibilidad en el Data Warehouse.
    * *Implementación:* `dbt source freshness`. Alertas en Slack si la latencia supera el SLA definido (ej. > 1 hora).
* **Volume (Volumen & Anomalías):**
    * *Definición:* Detección de picos o caídas drásticas en la cantidad de filas ingestadas.
    * *Implementación:* Tests de desviación estándar (Z-score) sobre el histórico de ingestión diaria.
* **Schema Drift (Deriva del Esquema):**
    * *Definición:* Cambios no autorizados en la estructura de la fuente (ej. cambio de tipo de dato `INT` a `STRING`).
    * *Implementación:* Validación estricta de esquema en la capa de ingestión.
* **Distribution:**
    * *Definición:* Validación de rangos lógicos de negocio (ej. `precio > 0`, `null rate < 1%`).

### 1.2 Testing Automatizado (dbt)
Ningún modelo se promueve a producción sin pasar los siguientes tests bloqueantes en el CI/CD:
* `unique` & `not_null` en Primary Keys.
* `accepted_values` para columnas de estado/categoría.
* `relationships` para integridad referencial entre tablas.

---

## 2. Security & Access Control (FGAC)

Implementamos **Fine-Grained Access Control** para proteger PII (Información Personal Identificable) sin obstaculizar la operatividad del negocio.

### 2.1 Column-Level Security (CLS)
Uso de **Policy Tags** (Taxonomías en BigQuery) para restringir el acceso a columnas sensibles (`email`, `phone`, `address`).
* **Política:** Solo roles `Data Engineer` y `Admin` pueden desencriptar columnas con tag `pii_sensitive`.
* **Analistas:** Al consultar `SELECT *`, reciben `null` o valores enmascarados en estas columnas, permitiendo la ejecución de la query sin exponer datos.

### 2.2 Row-Level Security (RLS)
Filtrado dinámico de filas basado en el usuario que ejecuta la consulta.
* *Caso de Uso:* Un Country Manager solo visualiza filas donde `country_code = 'USER_ASSIGNED_REGION'`.

### 2.3 Hashing & Dynamic Masking
* **Hashing:** Los identificadores sensibles se transforman mediante **SHA-256** (`user_email_hash`) para permitir cruces (JOINs) y análisis de unicidad sin revelar el dato crudo.
* **Masking:** Ofuscación dinámica en tiempo de lectura (ej. `jos***@gmail.com`).

---

## 3. Data Contracts & Architecture (Shift-Left)

Movemos la responsabilidad de la calidad del dato "a la izquierda" (hacia la fuente), evitando que datos corruptos entren al Data Lake.

### 3.1 Data Contracts (Contratos de Datos)
Acuerdos de esquema (JSON Schema) definidos entre los equipos de Producto (App/Backend) y Datos.
* **Validación:** Si un evento emitido por la App no cumple con el contrato (ej. falta una propiedad obligatoria), es rechazado en la entrada.
* **Dead Letter Queue (DLQ):** Los eventos rechazados se envían a una cola de aislamiento para su revisión y reprocesamiento, sin detener el pipeline principal.

### 3.2 Data Lineage (Linaje)
Mantenimiento de un Grafo Acíclico Dirigido (DAG) completo que mapea las dependencias desde la fuente hasta el dashboard.
* **Impact Analysis:** Antes de cualquier cambio, se evalúa automáticamente qué modelos y reportes "aguas abajo" (downstream) serán afectados.

---

## 4. Glosario Técnico (DataOps)

* **Idempotencia:** Capacidad de un proceso ETL de ejecutarse múltiples veces sin duplicar datos ni cambiar el resultado final.
* **Shift-Left Governance:** Validar la calidad en la fuente (productor), no en el destino (consumidor).
* **Immutable Logs:** Los datos crudos en S3 (Bronze Layer) nunca se modifican (Append-Only).
* **CI/CD for Data:** Integración y Despliegue Continuo de modelos de datos, asegurando tests automáticos en cada Pull Request.

---
*Estándar mantenido por el equipo de Data Engineering.*
