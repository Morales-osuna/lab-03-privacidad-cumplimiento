# Evidencia 05: Análisis de Finalidad Secundaria

## Objetivo
Evaluar la viabilidad legal y técnica de reutilizar los datos operativos de clientes para la finalidad secundaria de enviarle promociones personalizadas de marketing.

## Resultado
1. **¿El hecho de que la organización ya tenga los datos significa automáticamente que puede utilizarlos para cualquier finalidad?**
   No. El principio de limitación de finalidad establece que los datos recopilados para un propósito operativo (atención/ventas) no pueden ser utilizados para finalidades distintas sin una base jurídica adecuada o el consentimiento explícito del titular.
2. **¿Qué preguntas debería realizar la organización antes de implementar este tratamiento?**
   - ¿Se informó esta finalidad secundaria en el Aviso de Privacidad original?
   - ¿El cliente otorgó su consentimiento expreso/opción de exclusión (*opt-out*) para uso publicitario?
   - ¿El beneficio justifica el riesgo de exposición de información sensible o perfilamiento masivo?
3. **¿Qué datos serían realmente necesarios?**
   Únicamente el canal de contacto preferido (ej. correo electrónico) y el historial/interés de compra anonimizado. Se deben excluir RFC, nombres completos o teléfonos personales para evitar perfilamiento no autorizado.
4. **¿Necesitaría el área de marketing acceso directo a la tabla `clientes`?**
   No. El acceso directo otorga visibilidad completa a datos no requeridos para marketing.
5. **¿Qué controles técnicos podrían limitar la información entregada?**
   - Creación de vistas intermedias que expongan únicamente el correo y categorías de preferencia.
   - Mecanismos de enmascaramiento o seudonimización.
   - Integración con una tabla de gestión de preferencias u oposiciones (*lista de exclusión*).
