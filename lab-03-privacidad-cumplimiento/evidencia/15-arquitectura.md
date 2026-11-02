# Evidencia 15: Propuesta de Arquitectura de Datos Protegidos

## Objetivo
Diseñar una propuesta conceptual de arquitectura de base de datos en PostgreSQL para separar la información de acuerdo con su finalidad (Operación, Analítica y Auditoría), incorporando controles técnicos de acceso, minimización, protección de datos sensibles, retención, trazabilidad, eliminación y atención de solicitudes ARCO.

### Controles Técnicos Incorporados

#### 1. Control de Acceso
* **Segregación de Esquemas:** Creación de esquemas diferenciados (`operacion`, `analitica`, `auditoria`).
* **Control de Acceso Basado en Roles (RBAC):** Asignación estricta de permisos mediante `GRANT`/`REVOKE` según el rol funcional (`soporte`, `facturacion`, `analista`, `auditor`).
* **Seguridad a Nivel de Fila (RLS):** Implementación de políticas de `Row Level Security` para restringir la visibilidad de registros por sucursal o estado activo.

#### 2. Minimización
* **Estructuras Especializadas:** Tablas reducidas para cada proceso operativo (ej. `clientes_soporte` sin RFC ni datos de localización innecesarios).
* **Vistas Limitantes:** Exposición de únicamente los campos requeridos mediante vistas que excluyen PII no justificada.

#### 3. Protección de Información Sensible
* **Cifrado de Columna:** Cifrado simétrico/asimétrico de campos críticos (como el RFC) usando la extensión `pgcrypto`.
* **Anonimización Dinámica:** Transformación de datos identificables en rangos o categorías demográficas dentro del esquema `analitica`.

#### 4. Retención
* **Definición de Timestamps:** Inclusión de campos de control como `fecha_alta`, `ultima_actividad` y `estado`.
* **Particionamiento por Fechas:** Particionado de tablas históricas para facilitar la gestión y posterior archivado de datos antiguos.

#### 5. Trazabilidad
* **Auditoría Especializada:** Configuración de la extensión `pgaudit` para registrar todas las sentencias DDL y DML ejecutadas sobre tablas con datos sensibles.
* **Esquema de Auditoría Separado:** Almacenamiento inmutable de bitácoras en el esquema `auditoria`, accesible únicamente por el rol de auditoría.

#### 6. Eliminación
* **Funciones de Purga Segura:** Procedimientos almacenados para ejecutar borrados definitivos (`HARD DELETE`) de registros que han cumplido su ciclo de retención legal.
* **Propagación en Réplicas y Archivos:** Protocolos para asegurar que la eliminación se aplique de forma coordinada en réplicas de lectura y respaldos descartados.

#### 7. Atención de Solicitudes (Derechos ARCO)
* **Funciones de Búsqueda para Acceso:** Procedimientos para consultar la totalidad de datos almacenados de un titular mediante su identificador único.
* **Mecanismos para Rectificación:** Triggers de auditoría que registran el autor, fecha y valor previo de cualquier cambio autorizado sobre PII.
* **Procesos para Cancelación u Oposición:** Módulos de bloqueo lógico (`estado = 'INACTIVO_OPOSICION'`) para excluir los datos de finalidades secundarias sin alterar la trazabilidad requerida legalmente.

