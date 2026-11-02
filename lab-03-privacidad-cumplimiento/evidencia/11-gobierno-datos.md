# Evidencia 11: Diseñar una matriz de gobierno de datos

## Objetivo
Construir una matriz de gobierno de datos para definir los roles, responsabilidades, finalidades, usuarios autorizados, controles de protección, tiempos de retención y acciones al finalizar el ciclo de vida de los datos para los principales campos de la tabla `clientes`.

## Resultado

### Matriz de Gobierno de Datos

| Dato | Finalidad | Responsable | Usuarios autorizados | Protección | Retención | Acción al finalizar |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **nombre** | Identificación formal del titular | Propietario de datos (Dirección Operativa) | Atención al cliente, Facturación | Restricción por rol / Vistas sin PII para analítica | Duración de la relación comercial + 5 años fiscal | Eliminación o anonimización |
| **correo** | Canal principal de contacto y envío de facturas | Custodio de datos / Operaciones | Atención al cliente, Facturación | Control de acceso por rol y enmascaramiento | Duración de la relación comercial | Supresión segura |
| **telefono** | Contacto de soporte técnico y atención directa | Custodio de datos / Operaciones | Atención al cliente | Restricción a nivel de columna / Vistas limitadas | Duración de la relación comercial | Supresión segura |
| **rfc** | Identificación tributaria para emisión de facturas | Propietario de datos (Dirección Financiera) | Facturación | Cifrado de columna / Acceso restringido | 5 años (Requisito Código Fiscal) | Purga definitiva |
| **edad** | Análisis y segmentación demográfica agregada | Propietario de datos (Analítica) | Analítica | Agrupación por rangos en vistas (sin acceso a tabla base) | No requiere conservación individual | Desasociación completa de PII |
| **ciudad** | Logística regional y estadísticas geográficas | Custodio de datos / Operaciones | Atención al cliente, Analítica | Vistas con datos agregados para analítica | Duración de la relación comercial | Conservación en datos consolidados |
| **codigo_postal** | Validación de envíos y análisis de cobertura | Custodio de datos / Operaciones | Atención al cliente | Ocultamiento parcial / Vistas restringidas | Duración de la relación comercial | Eliminación |
