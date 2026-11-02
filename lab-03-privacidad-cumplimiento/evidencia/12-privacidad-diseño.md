# Evidencia 12: Análisis de Privacidad por Diseño y por Defecto

## Objetivo
Analizar la propuesta técnica de registro de clientes presentada por el equipo de desarrollo y aplicar los principios de *Privacy by Design* y *Privacy by Default* en la base de datos.

## Resultado


#### 1. ¿Qué problemas identificas?
* **Recolección excesiva e injustificada de datos:** Se capturan atributos personales sensibles (salario, estado civil, ocupación) sin que exista un proceso de negocio que los requiera.
* **Incremento del riesgo de seguridad:** A mayor cantidad de datos almacenados innecesariamente, mayor es el impacto y la gravedad en caso de una filtración de información (*data breach*).
* **Ausencia de justificación legal y funcional:** La recolección basada en "uso futuro no definido" contraviene las bases jurídicas de la protección de datos personales.
* **Complica el cumplimiento de Derechos ARCO:** Dificulta la gestión de supresión o actualización de información no esencial.

#### 2. ¿Qué principio de privacidad se está ignorando?
Se ignoran los principios de **Minimización de datos**, **Limitación de la finalidad**, **Necesidad** y los enfoques de **Privacidad por Diseño** (*Privacy by Design*) y **Privacidad por Defecto** (*Privacy by Default*).

#### 3. ¿Qué preguntas realizarías antes de aprobar el diseño?
1. ¿Cuál es la finalidad específica de negocio para solicitar el salario, estado civil u ocupación del cliente?
2. ¿Se cuenta con el consentimiento expreso e informado del titular para tratar datos financieros y personales sensibles?
3. ¿Está contemplada esta recolección en el Aviso de Privacidad de la empresa?
4. ¿Qué área funcional es la responsable (*Data Owner*) de justificar la necesidad de cada uno de estos campos?

#### 4. ¿Qué datos eliminarías?
Eliminaría de forma inmediata los campos que no guardan relación con un registro básico de cliente:
* `salario`
* `estado_civil`
* `ocupacion`

#### 5. ¿Qué datos requerirían una justificación adicional?
* `fecha_nacimiento` y `edad`: Se debe conservar únicamente uno de los dos para evitar redundancia (de preferencia calcular la edad o registrar solo un indicador booleano de mayoría de edad si la ley lo exige).
* `direccion`, `ciudad` y `codigo_postal`: Solo se justifican si el sistema realiza envíos físicos o entrega de mercancía; de lo contrario, representan sobre-recolección.
* `rfc`: Solo si el cliente requiere la emisión de comprobantes fiscales.

#### 6. ¿Cómo aplicarías privacidad por defecto?
* **Esquema minimalista inicial:** Diseñar la tabla solo con los campos indispensables para el registro (`id`, `nombre`, `correo`, `telefono`).
* **Campos opcionales desactivados:** No solicitar ni almacenar datos adicionales a menos que el cliente active voluntariamente un servicio que lo requiera.
* **Formularios limitados:** Configurar la interfaz de usuario para que solo exija los datos estrictamente necesarios según la transacción a realizar.
