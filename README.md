# LABORATORIO 03: Privacidad, Cumplimiento y Gobierno de Datos en PostgreSQL 16
---

## 1. Introducción
En el desarrollo y operación de sistemas de bases de datos, es común priorizar la disponibilidad y el rendimiento técnico por encima de la privacidad de la información. Comercializadora Norte S.A. de C.V. presentaba una arquitectura monolítica y un esquema de permisos laxo.

Este proyecto aborda la reestructuración del gobierno de datos en PostgreSQL 16, resolviendo las vulnerabilidades asociadas al uso indiscriminado de datos reales en entornos de prueba, la ausencia de políticas de retención, la falta de consentimiento para finalidades secundarias y la omisión de controles para garantizar los Derechos ARCO.

---

## 2. Inventario de Datos
Se auditó la estructura principal de la entidad `clientes`, identificando y clasificando los campos según su nivel de sensibilidad y su naturaleza respecto a la privacidad del titular:

| Campo | Categoría | Tipo de Dato | Clasificación / Sensibilidad |
| :--- | :--- | :--- | :--- |
| `id` | Técnico | `SERIAL / INT` | Identificador interno (No PII) |
| `nombre` | Identificación | `VARCHAR(100)` | Dato Personal Directo (PII) |
| `correo` | Contacto | `VARCHAR(100)` | Dato Personal Directo (PII) |
| `telefono` | Contacto | `VARCHAR(15)` | Dato Personal Directo (PII) |
| `rfc` | Identificación / Fiscal | `VARCHAR(13)` | Dato Sensible / Fiscal |
| `edad` | Demográfico | `INT` | Dato Personal Indirecto |
| `ciudad` | Ubicación | `VARCHAR(50)` | Dato Personal Indirecto |
| `codigo_postal` | Ubicación | `VARCHAR(10)` | Dato Personal Indirecto |

---

## 3. Finalidades
Para dar cumplimiento a los principios de proporcionalidad y limitación de la finalidad, los datos almacenados se segmentaron según su propósito funcional:

* **Finalidad Primaria (Operativa y Legal):**
  * **Emisión de Comprobantes Fiscales y Facturación:** Requiere el procesamiento de `nombre`, `correo` y `rfc`.
  * **Soporte y Atención al Cliente:** Requiere el acceso a `nombre`, `correo` y `telefono` para el seguimiento de solicitudes y entregas.
* **Finalidad Secundaria (Marketing y Analítica):**
  * **Campañas Promocionales:** Uso exclusivo de `correo` bajo autorización previa expresada por el titular (*opt-in*).
  * **Análisis Demográfico y Estadístico:** Uso de `edad`, `ciudad` y `codigo_postal` de forma disociada o agregada, sin vinculación directa con el nombre o RFC del titular.

---

## 4. Minimización
Se identificó sobre-recolección de datos y redundancia en la tabla `clientes`. Se aplicaron los siguientes criterios de limitación y eliminación:

1. **Eliminación de Campos Innecesarios/Sensibles:** Campos como `salario`, `estado_civil` u `ocupacion` fueron retirados de los esquemas de registro general por no contar con una justificación de negocio ni base jurídica.
2. **Redundancia Demográfica:** Conservar `fecha_nacimiento` y `edad` simultáneamente es innecesario. Se prefiere registrar la fecha y calcular la edad en tiempo de ejecución o trabajar únicamente con rangos etarios en el área de analítica.
3. **Restricción de Alcance por Vista:** El área de analítica no requiere ver el `nombre` ni el `rfc` para generar reportes mensuales de comportamiento de mercado, por lo que dichos campos se omiten en las vistas analíticas.

---

## 5. Control de Acceso
El control de acceso se reestructuró aplicando el principio de **mínimo privilegio** y **segregación de funciones** (RBAC) en PostgreSQL:

* **Rol `rol_facturacion`:** Acceso exclusivo a los campos fiscales (`rfc`, `nombre`, `correo`) para la emisión de facturas.
* **Rol `rol_soporte`:** Acceso limitado a datos de contacto (`nombre`, `correo`, `telefono`) para resolución de tickets.
* **Rol `rol_analitica`:** Revocación completa de acceso a la tabla base `clientes`. Únicamente se les otorga permiso de consulta sobre vistas agregadas y anonimizadas (`v_analitica_clientes`).
* **Rol `rol_dba`:** Custodio técnico de la infraestructura. No posee privilegios de consulta o manipulación sobre los datos personales en tablas de negocio.

---

## 6. Derechos ARCO
La atención técnica de las solicitudes de los titulares de los datos implica los siguientes retos e implicaciones identificadas:

* **Acceso:** Implementación de funciones almacenadas que generan un extracto consolidado de toda la PII asociada a un titular.
* **Rectificación:** Uso de triggers de auditoría para registrar el historial de modificaciones sobre datos personales, manteniendo la trazabilidad sin violar la exactitud del dato actual.
* **Cancelación (Borrado):** Se determinó que ejecutar `DELETE FROM clientes` en producción no elimina la información de los respaldos (`.pgdump`), réplicas o exportaciones locales. Se requirió un protocolo de ciclo de vida que incluya políticas de expiración en backups.
* **Oposición:** Implementación de un flag de bloqueo lógico (`bloqueado_marketing = TRUE`) para frenar el uso de datos en finalidades secundarias sin destruir el registro requerido por obligaciones fiscales.

---

## 7. Retención
El periodo de conservación de los datos se determinó bajo los siguientes criterios normativos y operativos:

1. **Plazo Comercial Activo:** Mantenimiento de la PII durante la vigencia de la relación contractual o la actividad del cliente en la plataforma.
2. **Plazo Legal Fiscal:** Conservación de datos de facturación (`rfc`, comprobantes) durante **5 años**, atendiendo a lo estipulado en el Código Fiscal de la Federación.
3. **Purga Automatizada:** Finalizado el periodo legal obligatorio o tras una solicitud de cancelación procedente, se ejecutan scripts de depuración o anonimización irreversibles en PostgreSQL.

---

## 8. Gobierno de Datos
Se estableció la siguiente matriz de responsabilidades para definir el ownership y las reglas de uso de cada dato:

| Dato | Finalidad | Responsable | Usuarios autorizados | Protección | Retención | Acción al finalizar |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **nombre** | Identificación formal | Dirección Operativa | Atención al cliente, Facturación | Restricción por rol / Vistas sin PII | Relación comercial + 5 años fiscal | Eliminación / Anonimización |
| **correo** | Contacto y facturación | Operaciones | Atención al cliente, Facturación | Enmascaramiento y RBAC | Relación comercial | Supresión segura |
| **telefono** | Soporte técnico | Operaciones | Atención al cliente | Restricción a nivel de columna | Relación comercial | Supresión segura |
| **rfc** | Identificación tributaria | Dirección Financiera | Facturación | Cifrado (`pgcrypto`) | 5 años (Fiscal) | Purga definitiva |
| **edad** | Análisis demográfico | Analítica | Analítica | Agrupación en rangos | No individualizada | Desasociación completa |
| **ciudad** | Logística regional | Operaciones | Atención al cliente, Analítica | Vistas de agregación | Relación comercial | Conservación en agregados |
| **codigo_postal**| Validación de entregas | Operaciones | Atención al cliente | Ocultamiento parcial | Relación comercial | Eliminación |

---

## 9. Evaluación Integral
Se identificaron 10 riesgos críticos en la infraestructura y procesos de Comercializadora Norte S.A. de C.V.:

1. **Acceso irrestricto de analítica a la tabla base:** Mitigado con la creación de vistas anonimizadas.
2. **Exposición de PII en estadísticas:** Mitigado mediante la agregación de datos por grupos y regiones.
3. **Retención indefinida de datos (12 años):** Mitigado con políticas de depuración periódica.
4. **Falta de política documentada de conservación:** Mitigado mediante la formalización de manuales de retención.
5. **Uso de respaldos reales en entornos de desarrollo:** Mitigado al prohibir copias de producción y sustituirlas por *datasets* sintéticos.
6. **Ausencia de enmascaramiento en pruebas:** Mitigado con scripts de *seeding* y seudonimización.
7. **Uso de correos para marketing sin consentimiento:** Mitigado con controles de *opt-in/opt-out*.
8. **Inexistencia de un catálogo e inventario de datos:** Mitigado con la documentación activa del esquema.
9. **Ausencia de RBAC estricto:** Mitigado con roles personalizados e inhabilitación del rol `public`.
10. **Falta de auditoría de consultas:** Mitigado con la activación de la extensión `pgaudit`.

---

## 10. Conclusiones
El desarrollo de este laboratorio permitió comprender que la **privacidad**, la **seguridad** y la **administración de bases de datos** no son áreas aisladas, sino dimensiones interdependientes de un mismo ecosistema de gobernanza:

* **Privacidad por Diseño:** Administrar una base de datos requiere planificar la protección desde el modelo Entidad-Relación. Diseñar tablas bajo el principio de "guardar todo por si acaso" crea pasivos legales y técnicos innecesarios.
* **Traducción Técnica de Criterios Legales:** Las leyes de protección de datos se materializan en sentencias SQL concretas: los principios de minimización y finalidad se traducen en `CREATE VIEW` y `REVOKE`, la confidencialidad en `pgcrypto`, y la trazabilidad en `pgaudit`.
* **SeguridadProactiva:** El DBA no es un simple administrador de almacenamiento o rendimiento; es el custodio técnico de la infraestructura responsable de garantizar que los accesos responda estrictamente a una necesidad funcional verificable.

## 27. Preguntas de reflexión

### 1. ¿Una base de datos técnicamente segura necesariamente cumple con todos los requisitos de privacidad?
No. La seguridad garantiza que la información esté protegida contra accesos no autorizados, fugas o alteraciones (confidencialidad, integridad y disponibilidad). Sin embargo, una base de datos con cifrado robusto y cortafuegos puede violar la privacidad si almacena datos innecesarios, si se conservan indefinidamente sin justificación, o si se utilizan para finalidades distintas a las autorizadas por el titular.

### 2. ¿Por qué el principio de mínimo privilegio también contribuye a la privacidad?
Porque restringe el acceso a la información personal únicamente a los usuarios o roles que la necesitan estrictamente para cumplir con su función operativa. Al limitar las consultas directas a tablas con datos sensibles mediante el uso de roles específicos y vistas restringidas, se reduce de forma drástica la superficie de exposición y el riesgo de mal uso de los datos personales.

### 3. ¿Por qué el DBA no debería decidir por sí solo la finalidad del tratamiento de los datos?
El DBA es el custodio técnico del motor de base de datos, no el propietario ni la autoridad legal del negocio. La finalidad del tratamiento implica implicaciones jurídicas, regulatorias y comerciales que deben ser determinadas por los propietarios de los datos (*Data Owners*), el área jurídica y los oficiales de privacidad en concordancia con el Aviso de Privacidad de la empresa.

### 4. ¿Por qué los desarrolladores no deberían recibir automáticamente copias completas de bases de datos productivas?
Porque los entornos de desarrollo y pruebas no suelen contar con las mismas medidas estrictas de seguridad, auditoría y control de acceso que un entorno de producción. Entregar copias completas expone información real de miles de titulares a personal no autorizado y aumenta el riesgo de filtraciones externas o incidentes de seguridad.

### 5. ¿Por qué una copia utilizada para desarrollo también puede representar un riesgo de privacidad?
Porque aunque esté en un servidor local de pruebas o en una máquina de desarrollo, la información contenida en esa copia sigue siendo información personal real. Si el equipo sufre un robo, una infección por código malicioso o carece de control de accesos, los datos de los clientes quedan expuestos sin que los controles del servidor principal de producción puedan impedirlo.

### 6. ¿Por qué la retención debe formar parte del ciclo de vida de los datos?
Porque conservar datos personales de forma indefinida incrementa innecesariamente los riesgos operativos y legales ante un incidente de seguridad, además de contravenir los principios normativos de conservación limitada. Definir periodos de retención garantiza que la información se suprima, anonimice o purgue de forma segura una vez cumplida la finalidad para la que fue recabada.

### 7. ¿Qué relación existe entre privacidad por diseño y diseño de bases de datos?
Ambos enfoques exigen definir la estructura del modelo Entidad-Relación considerando la protección de la información desde la etapa conceptual. Implica incorporar tipos de datos adecuados, esquemas segregados, triggers de auditoría, constraints, vistas preparadas para analítica y mecanismos de cifrado desde la fase inicial de modelado, en lugar de intentar añadir seguridad de manera reactiva sobre un diseño deficiente.

### 8. ¿Qué controles técnicos implementarías para reducir el riesgo de exposición de datos personales?
* Restricción de accesos mediante Roles y Permisos estrictos (RBAC).
* Creación de Vistas Limitantes y Agregadas para áreas secundarias como Analítica.
* Cifrado de columnas con datos sensibles utilizando `pgcrypto`.
* Seguridad a Nivel de Fila (*Row Level Security* - RLS).
* Registro de eventos y consultas mediante auditorías con `pgaudit`.
* Enmascaramiento o uso de datos sintéticos en entornos que no sean de producción.

### 9. ¿Qué información debería conocer un DBA para responder técnicamente a una solicitud ARCO?
El DBA debe conocer la estructura física del modelo de datos, la ubicación exacta de los campos con información personal del titular en la base de datos, la existencia de réplicas, respaldos (`.pgdump`), tablas históricas, vistas o triggers asociados, y los procedimientos SQL requeridos para ejecutar consultas de acceso, actualización de exactitud, bloqueo o borrado seguro.

### 10. ¿Qué responsabilidades deberían corresponder al DBA, al responsable de privacidad, al área jurídica y al propietario de los datos?
* **DBA (Custodio Técnico):** Implementa y mantiene los controles técnicos de seguridad, otorga permisos en PostgreSQL, configura cifrados, ejecuta purgas autorizadas y gestiona la infraestructura.
* **Propietario de los datos (*Data Owner*):** Define la necesidad de negocio de cada campo, autoriza qué roles pueden acceder a qué datos y establece las reglas funcionales del ciclo de vida.
* **Oficial de Privacidad / Área Jurídica:** Define el marco legal aplicable, redacta el Aviso de Privacidad, dictamina las solicitudes ARCO y establece los periodos de retención obligatorios conforme a la ley.