# Automatización de Leads Inmobiliarios con IA

Proyecto final de automatización desarrollado en **n8n**, orientado a la gestión automática de consultas inmobiliarias recibidas por Gmail.

El sistema registra cada consulta en Airtable, busca información de la propiedad, analiza el mensaje mediante inteligencia artificial y genera una respuesta propuesta. Antes de contactar al cliente se incorpora una instancia obligatoria de aprobación humana (HITL).

## Arquitectura

El flujo principal es:

Gmail  
→ Registro del Lead en Airtable  
→ Búsqueda de propiedad  
→ Análisis con IA  
→ Registro del análisis  
→ Aprobación humana  
→ Respuesta al cliente  
→ Actualización del estado

También existe una ruta alternativa para registrar errores producidos durante el procesamiento con IA.

## Tecnologías utilizadas

- n8n como orquestador
- Gmail como canal de entrada y salida
- Airtable como base de datos
- Groq como proveedor de inferencia
- openai/gpt-oss-safeguard-20b como modelo de IA
- Human-in-the-Loop para aprobación de respuestas

## Base de datos

La base de Airtable está compuesta principalmente por:

- `Leads`
- `Propiedades`
- `Configuración`

La tabla Leads registra la consulta y su evolución durante todo el proceso.

La tabla Propiedades contiene la información comercial utilizada como contexto.

La tabla Configuración permite almacenar parámetros operativos, como el correo del responsable de aprobación, sin hardcodearlos dentro del workflow.

## Seguridad y resiliencia

El proyecto incorpora:

- Filtro anti-loop en Gmail: `in:inbox -from:me`
- Variables dinámicas
- Thread ID de Gmail
- Aprobación humana antes del envío
- Ruta específica de error
- Registro del detalle técnico del error
- Reintentos automáticos en el nodo de IA
- Credenciales administradas mediante n8n
- Uso de tipos de datos correctos en las condiciones

## Dashboard

El Dashboard de Airtable permite visualizar:

- Tasa de aprobación
- Tasa de error IA
- Volumen de salida
- Tiempo ahorrado
- Ahorro económico estimado
- Distribución de Leads por estado

Resultados obtenidos en la muestra final:

- Volumen de salida: 5
- Tasa de error IA: 14%
- Tasa de aprobación: 83%
- Tiempo ahorrado: 1:03:00
- Ahorro económico estimado: USD 8,40

## Evidencias

### Ejecución aprobada

![Workflow aprobado](01_workflow_aprobado.png)

### Ruta de error

![Ruta de error](02_ruta_error.png)

### Dashboard final

![Dashboard](03_dashboard_kpis_final.png)

## Archivos de la entrega

- `Entrega_Final_Automatizacion_Inmobiliaria_2026.pdf`
  - Documentación completa del proyecto.
- `ENTREGA_8_Gestion_consultas_inmobiliaria_GitHub.json`
  - Exportación del workflow de n8n.
- `01_workflow_aprobado.png`
  - Evidencia de ejecución exitosa.
- `02_ruta_error.png`
  - Evidencia de manejo de errores.
- `03_dashboard_kpis_final.png`
  - Dashboard final de KPIs.

## Vistas públicas de Airtable

### Leads
https://airtable.com/appoLo3gLVfHx5iIF/shrwvJbGebLtuwAh3

### Propiedades
https://airtable.com/appoLo3gLVfHx5iIF/shrpfKFoCHG79JeRQ

## Documentación completa

La explicación detallada de la arquitectura, estructura de datos, JSON, seguridad, costos, pruebas y Dashboard se encuentra en:

`Entrega_Final_Automatizacion_Inmobiliaria_2026.pdf`

## Video demostrativo

Video de aproximadamente 3 minutos mostrando el funcionamiento completo de la automatización.

`demo_workflow_inmobiliaria.mp4`
