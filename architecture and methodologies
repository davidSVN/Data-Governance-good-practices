ARQUITECTURA DE DATOS END-TO-END: HERRAMIENTAS Y METODOLOGIAS
=============================================================

1. GENERACION Y CAPTURA (FUENTE)
-------------------------------------------------------------
Herramienta: Amplitude (SDK en App/Web).
Metodologia: 
   - Object-Action Framework: Estandarizar nombres de eventos (ej. "Order Completed").
   - Tracking Plan Governance: "Contrato de datos" estricto antes de implementar codigo.
   - Validation: Bloqueo de eventos que no cumplan el esquema de tipos.

2. ALMACENAMIENTO CRUDO (DATA LAKE)
-------------------------------------------------------------
Herramienta: AWS S3.
Metodologia: 
   - Inmutabilidad: Principio Append-Only (nunca sobrescribir archivos, solo agregar nuevos).
   - Formato: Parquet (Columnar y comprimido para eficiencia).
   - Estructura: Particionamiento estilo Hive (s3://bucket/v1/events/year=2026/month=02/day=10/).

3. PROCESAMIENTO Y MODELADO (ETL)
-------------------------------------------------------------
Herramientas: Databricks (Computo) + dbt (Logica de Transformacion).
Metodologia: 
   - Medallion Architecture:
     * Bronze: Copia exacta cruda (Raw).
     * Silver: Limpieza, deduplicacion y estandarizacion (Intermediate).
     * Gold: Tablas de negocio agregadas y listas para consumo (Marts).
   - Idempotencia: Procesos repetibles que no duplican datos.
   - Testing Automatico: Validacion de `unique` y `not_null` en cada ejecucion.

4. CONSUMO Y TABLAS CANONICAS (DATA WAREHOUSE)
-------------------------------------------------------------
Herramienta: Google BigQuery.
Metodologia: 
   - One Big Table (OBT): Tablas anchas desnormalizadas para analisis rapido de Growth.
   - Optimizacion de Costos: Partitioning (por fecha de evento) y Clustering (por user_id/pais).
   - Single Source of Truth: Una sola tabla "Gold" por entidad (ej. dim_users, fct_sessions).

5. GOBIERNO TRANSVERSAL (DATAOPS)
-------------------------------------------------------------
Principios:
   - Version Control: Todo el codigo SQL/Python vive en Git.
   - CI/CD: Automatizacion de tests y despliegues (GitHub Actions).
   - Ambientes: Separacion fisica estricta entre Desarrollo (Dev) y Produccion (Prod).
