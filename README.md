# Automatización de Leads Inmobiliarios con n8n

Proyecto final de automatización para la gestión de consultas de una inmobiliaria.

El sistema recibe consultas de potenciales clientes por Gmail, registra la información en Airtable, busca la propiedad consultada, analiza el mensaje mediante Inteligencia Artificial y genera una respuesta sugerida.

Antes de enviar la respuesta al cliente, se incluye una instancia de aprobación humana (HITL - Human in the Loop).

## Herramientas utilizadas

- n8n
- Gmail
- Airtable
- Groq
- IA generativa
- GitHub

## Funcionamiento del flujo

1. Gmail detecta una nueva consulta.
2. La consulta se registra en Airtable.
3. n8n busca la propiedad mencionada en la base de datos.
4. La IA analiza el mensaje del cliente.
5. Se determina:
   - Prioridad del lead
   - Resumen de la consulta
   - Respuesta sugerida
   - Propiedad relacionada
   - Presupuesto declarado por el cliente
6. La respuesta queda pendiente de aprobación humana.
7. Si se aprueba, se responde al cliente por Gmail.
8. Si se rechaza, el lead queda registrado como "Rechazado por Humano".
9. Si ocurre un error técnico, se registra el estado "Error" junto con el detalle.

## Estados del proceso

- Pendiente
- Pendiente de aprobación
- Aprobado por Humano
- Enviado
- Rechazado por Humano
- Error

## Control humano

El sistema utiliza un esquema HITL (Human in the Loop).

La IA genera la respuesta, pero no puede enviarla directamente al cliente.

Un usuario debe aprobar o rechazar la respuesta antes del envío.

## Manejo de errores

El nodo de Inteligencia Artificial cuenta con una salida específica de error.

Cuando ocurre un fallo:

- Se detiene la ruta normal.
- El lead se actualiza en Airtable.
- El estado cambia a "Error".
- Se registra el detalle técnico del error.

## Base de datos

La base de Airtable contiene dos tablas principales:

### Leads

Registra las consultas recibidas, el análisis de IA y el estado del proceso.

### Propiedades

Contiene la información utilizada por la IA como fuente de datos:

- Código
- Nombre
- Ubicación
- Precio
- Tipo
- Disponibilidad

## Dashboard de resultados

El proyecto incluye un dashboard en Airtable para monitorear el funcionamiento de la automatización.

Los principales indicadores son:

- Tasa de error de IA: 15%
- Cantidad de aprobaciones: 7
- Volumen procesado: 13 leads
- Tiempo ahorrado: 1:57:41
- Ahorro económico estimado: USD 15,69

El ahorro económico se calcula considerando un costo estimado de USD 8 por hora de trabajo manual.

![Dashboard de resultados](03_dashboard_kpis_final.png)

## Vista pública de resultados

https://airtable.com/appoLo3gLVfHx5iIF/shrwvJbGebLtuwAh3

## Workflow de n8n

El archivo JSON incluido en este repositorio permite importar la automatización en n8n.

Por seguridad, las credenciales y datos personales fueron eliminados o reemplazados antes de publicar el workflow.

## Autor

Mateo Gosio

Proyecto realizado como entrega final del curso de automatización con Inteligencia Artificial.
