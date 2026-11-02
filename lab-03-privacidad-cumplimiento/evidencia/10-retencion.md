# Evidencia 10: Análisis de la Política de Retención de Datos

## Objetivo
Analizar la propuesta de la organización sobre la conservación indefinida de los datos de los clientes y determinar su impacto técnico, legal y de seguridad dentro de la administración de la base de datos[cite: 12, 26].


## Resultado

#### Pregunta 1: ¿Es compatible con el principio de minimización?
**No.** El principio de minimización establece que los datos personales recopilados deben ser adecuados, relevantes y estrictamente limitados a lo necesario para la finalidad informada[cite: 3]. Conservar información indefinidamente bajo la premisa de "uso futuro no especificado" viola directamente este principio, así como el de limitación de la finalidad

#### Pregunta 2: ¿Qué riesgos genera conservar información innecesaria?
1. **Mayor superficie de ataque:** Aumenta exponencialmente el impacto de un incidente de seguridad o filtración de datos (*data breach*), exponiendo registros obsoletos de usuarios inactivos
2. **Incumplimiento legal y regulatorio:** Exposición a sanciones y multas por violar leyes de protección de datos (como la LFPDPPP) que exigen la supresión de datos al finalizar la finalidad del tratamiento
3. **Costos y degradación de rendimiento:** Aumento innecesario de almacenamiento, tiempos de procesamiento de consultas, tamaño de respaldos (*backups*) y ventanas de mantenimiento
4. **Dificultad en gestión ARCO:** Complica la localización y atención eficiente de solicitudes de cancelación u oposición

#### Pregunta 3: ¿Qué debería determinar la organización antes de establecer un periodo de retención?
* **Marco legal y regulatorio aplicable:** Obligaciones fiscales (ej. retención de 5 años por código fiscal), contractuales o comerciales
* **Necesidad operativa real:** Tiempo promedio durante el cual el cliente mantiene una relación activa con la empresa
* **Criterios de inactividad:** Definir cuándo un cliente pasa a estar inactivo (ej. 24 meses sin transacciones)
* **Destino final de los datos:** Procedimientos para el borrado seguro o la anonimización irreversibles de la información al término del plazo

#### Pregunta 4: ¿Qué información debería documentarse para justificar el periodo seleccionado?
* **Base jurídica y legal:** Referencias normativas que imponen conservar el dato durante determinado tiempo
* **Finalidades del tratamiento:** Propósito específico que justifica cada rango de conservación
* **Matriz de retención por tipo de dato:** Tiempos diferenciados según la naturaleza del campo (ej. fiscal vs. preferencias de marketing)
* **Protocolos de eliminación y purga:** Mecanismos técnicos y administrativos utilizados para destruir o anonimizar los registros al vencer el plazo
