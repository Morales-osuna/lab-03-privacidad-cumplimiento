# Evidencia 01: Inventario y Finalidad de los Datos

## Objetivo
Identificar, clasificar y documentar los datos personales almacenados en la tabla `clientes`, determinando la necesidad, finalidad, nivel de acceso y periodo de retención para cada uno.

## Procedimiento
Se analizó la estructura actual de la tabla `clientes` de Comercializadora Norte S.A. de C.V. frente a las necesidades operativas de los roles organizacionales (Atención al cliente, Facturación, Analítica y DBA).

## Resultado
| Campo | ¿Es dato personal? | Finalidad | ¿Es necesario? | Nivel de acceso | Retención |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **id** | No | Identificador único de registro de base de datos | Sí | Todos los roles | Indefinido (mientras exista la cuenta) |
| **nombre** | Sí | Identificación formal del titular en atención al cliente y facturación | Sí | Atención al cliente, Facturación | Periodo de la relación comercial + 5 años fiscal |
| **correo** | Sí | Canal principal de comunicación y envío de facturas/notificaciones | Sí | Atención al cliente, Facturación | Periodo de la relación comercial |
| **telefono** | Sí | Canal secundario de contacto directo y atención de soporte | Sí | Atención al cliente | Periodo de la relación comercial |
| **rfc** | Sí | Cumplimiento de obligaciones fiscales de facturación de productos | Sí | Facturación únicamente | 5 años (Requisito Código Fiscal) |
| **edad** | Sí | Segmentación estadística de grupos demográficos | No para operación; Sí para analítica | Solo Analítica (vistas agregadas) | No requiere conservación individualizada |
| **ciudad** | Sí | Análisis estadístico regional e identificación de zona | Sí (para logística y analítica) | Atención al cliente, Analítica | Periodo de la relación comercial |
| **codigo_postal** | Sí | Validación de domicilio de entrega y zona regional | Sí (para envíos) | Atención al cliente | Periodo de la relación comercial |
| **fecha_alta** | No (Técnico) | Auditoría interna e histórico de registro de cliente | Sí | DBA, Atención al cliente | Vida del sistema |

## Análisis
- **Justificación de decisiones:**
  - El **RFC** solo se justifica legalmente para procesos fiscalizables. No debe ser visible para atención al cliente ni para analítica.
  - La **edad** individual representa un riesgo innecesario de exposición de PII. Para fines estadísticos debe exponerse únicamente mediante rangos y datos agregados.
  - **Nombre, Correo y Teléfono** son necesarios exclusivamente para interacción operativa, debiendo limitarse el acceso directo mediante la aplicación y no otorgar lectura libre de la tabla principal.
  

## Preguntas y Respuestas

### 1. ¿Todos los campos son necesarios para atención al cliente?
**No.** La tabla original `clientes` contiene datos como `rfc`, `edad` o `codigo_postal` que no son indispensables para la atención básica y el soporte técnico al cliente.

### 2. ¿Por qué soporte necesitaría conocer el RFC?
**No lo necesita.** El RFC es un dato fiscal cuyo tratamiento se justifica únicamente para procesos de facturación e identificación tributaria. Otorgar acceso a esta información al personal de soporte expone un dato personal sensible de forma innecesaria.

### 3. ¿Necesita conocer la edad?
**No.** Conocer la edad del cliente no aporta ningún valor ni es un requisito técnico u operativo para atender consultas, reportes o brindar soporte al usuario.

### 4. ¿Necesita conocer el código postal?
**No necesariamente para soporte básico.** Salvo que el área de soporte gestione directamente entregas físicas o logística de envíos, el código postal no se requiere para la atención de solicitudes habituales.

### 5. ¿Qué principio de privacidad está relacionado con esta decisión?
Está relacionado principalmente con el **Principio de Minimización de Datos**, el cual establece que todo tratamiento de datos debe limitarse a lo estrictamente adecuado, relevante y necesario para cumplir la finalidad determinada. Asimismo, se vincula con los principios de **Limitación de Finalidad** y **Necesidad**.