# Evidencia 08: Derecho de Cancelación y Ciclo de Vida de los Datos

## Objetivo
Mapear la dispersión de los datos personales en los diferentes entornos del sistema de base de datos para determinar el impacto técnico de una solicitud de cancelación (Derecho ARCO).

### Respuesta a la interrogante

**¿Por qué eliminar un registro de una tabla no necesariamente significa que toda la información relacionada con esa persona haya desaparecido de la organización?**

Un comando `DELETE FROM clientes WHERE id = X;` en la tabla principal de la base de datos de producción únicamente remueve la fila en dicho origen. Sin embargo, la información del titular sigue existiendo en los demás nodos del mapa:

1. **Respaldos:** Copias de seguridad físicas y lógicas (`.pgdump`, archivos WAL o snapshots) tomadas antes del borrado.
2. **Réplicas:** Retrasos en la sincronización, réplicas de solo lectura o instancias desconectadas.
3. **Reportes:** Documentos PDF, dashboards e informes históricos generados previamente para las áreas directivas o de análisis.
4. **Exportaciones:** Archivos planos (CSV, XLSX) descargados localmente por usuarios o áreas operativas.
5. **Sistemas externos:** CRMs, plataformas de envío de correo, facturación de terceros o sistemas integrados mediante API/ETL.