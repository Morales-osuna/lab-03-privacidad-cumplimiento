# Evidencia 14: Evaluación Integral de Riesgos de Privacidad

## Objetivo
Identificar, clasificar y evaluar al menos 10 problemas o riesgos presentes en el escenario integral de la organización, relacionándolos con los principios de privacidad afectados, sus riesgos potenciales y los controles propuestos para su mitigación.


### Matriz de Evaluación Integral de Riesgos

| Problema | Principio afectado | Riesgo | Control propuesto |
| :--- | :--- | :--- | :--- |
| **1. Acceso directo de analítica a la tabla `clientes`** | Mínimo Privilegio / Minimización de Datos | Exposición innecesaria de PII (nombre, teléfono, RFC) a usuarios que solo requieren datos estadísticos. | Revocar acceso a la tabla base y crear vistas agregadas (ej. `v_analitica_clientes`). |
| **2. Exposición de identificadores personales para estadísticas** | Limitación de la Finalidad | Uso irrestricto de datos identificables para análisis demográficos mensuales. | Anonimizar datos mediante transformaciones o vistas sin columnas identificables directas. |
| **3. Conservación de datos desde hace 12 años** | Retención de Datos / Limitación del Plazo | Mantenimiento de información obsoleta que incrementa el impacto ante una filtración de datos (*data breach*). | Definir e implementar un procedimiento de purga o anonimización periódica para registros antiguos. |
| **4. Inexistencia de una política documentada de retención** | Gobierno de Datos / Responsabilidad Proactiva | Incumplimiento normativo y falta de criterios claros para la conservación y eliminación de datos. | Redactar, aprobar y formalizar una política institucional de ciclo de vida y retención de datos. |
| **5. Entrega de copias completas de la base a desarrolladores** | Confidencialidad / Privacidad por Defecto | Exposición masiva de la base de datos de producción en entornos con menores controles de seguridad. | Prohibir el uso de respaldos de producción en desarrollo; entregar únicamente bases con datos sintéticos. |
| **6. Uso de información real en ambientes de desarrollo** | Privacidad por Diseño / Proporcionalidad | Fuga de datos personales por parte de desarrolladores o vulnerabilidades en entornos de prueba. | Implementar un proceso automatizado de enmascaramiento, seudonimización o *seeding* ficticio. |
| **7. Uso de correos por marketing para campañas promocionales** | Consentimiento / Limitación de Finalidad | Envíos no solicitados (SPAM) e infracciones legales por utilizar datos de operación para fines comerciales no aceptados. | Implementar un sistema de gestión de preferencias (*opt-in/opt-out*) e integrar listas de exclusión. |
| **8. Inexistencia de documentación sobre la finalidad de los datos** | Transparencia / Calidad de Datos | Imposibilidad de justificar la recolección de información ante auditorías o solicitudes de titulares. | Crear y mantener actualizado el catálogo e inventario de datos personales de la organización. |
| **9. Ausencia de segregación funcional entre roles** | Control de Acceso / Necesidad | Todos los analistas tienen los mismos permisos de consulta sobre datos operativos y sensibles. | Implementar un modelo RBAC (Control de Acceso Basado en Roles) estricto en PostgreSQL. |
| **10. Falta de trazabilidad y auditoría de consultas** | Trazabilidad / Responsabilidad Rendida | Incapacidad para detectar accesos no autorizados, exfiltración de información o mal uso de PII. | Habilitar registros de auditoría en la base de datos (mediante extensiones como `pgaudit`). |

