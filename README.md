# Automatización de Leads Inmobiliarios con IA

## Descripción del proyecto

Proyecto final de automatización desarrollado en **n8n** para gestionar consultas inmobiliarias recibidas por Gmail.

El sistema registra cada consulta en Airtable, busca información de la propiedad y utiliza inteligencia artificial para analizar el mensaje, clasificar su prioridad y generar una respuesta propuesta.

Antes de contactar al cliente, el workflow incorpora una instancia obligatoria de **aprobación humana (Human-in-the-Loop)**.

## Tecnologías utilizadas

- **n8n:** orquestación del workflow.
- **Gmail:** recepción de consultas, aprobación humana y envío de respuestas.
- **Airtable:** almacenamiento de leads, propiedades y configuración.
- **Groq:** proveedor de inferencia del modelo de IA.
- **GPT-OSS Safeguard 20B:** análisis de consultas y generación de respuestas estructuradas.

---


## Documentación técnica

| Apartado | Documentación |
|---|---|
| Arquitectura del sistema | [Arquitectura y diagrama](01_Documentacion/01_Arquitectura.md) |
| Estructuras de datos | [Modelo de datos y JSON](01_Documentacion/02_Modelo_Datos_JSON.md) |
| Optimización de costos | [Matriz comparativa de costos](01_Documentacion/03_Matriz_de_Costos.md) |
| Seguridad y resiliencia | [Seguridad, HITL y rutas de error](01_Documentacion/04_Seguridad_y_Resiliencia.md) |
| Dashboard de control | [Dashboard y KPIs](01_Documentacion/05_Dashboard_KPIs.md) |

Cada documento contiene las explicaciones técnicas correspondientes y las evidencias del funcionamiento cuando aplica.


---

## Funcionamiento general

El proceso automatizado sigue este recorrido:

**Gmail → Registro del lead → Búsqueda de propiedad → Análisis con IA → Registro del análisis → Aprobación humana → Respuesta al cliente**

El workflow contempla tres resultados:

- **Aprobado:** se envía la respuesta al cliente y se actualiza el estado en Airtable.
- **Rechazado:** se registra la decisión humana y no se envía la respuesta.
- **Error técnico:** se registra el incidente en Airtable sin continuar hacia el envío.

---

## Archivos técnicos y evidencias

### Workflow de n8n

El archivo JSON contiene el workflow exportado y permite revisar su estructura e importarlo en n8n.

[Ver workflow JSON](03_Workflow/ENTREGA_8_Gestion_consultas_inmobiliaria_GitHub.json)

### Video demostrativo

El video presenta el funcionamiento de la automatización, desde la recepción de una consulta hasta el procesamiento y actualización de los registros.

[Ver video demostrativo](Demo_Automatizacion_Inmobiliaria_n8n.mp4)

### Evidencias de ejecución

| Evidencia | Archivo |
|---|---|
| Ejecución aprobada | [Ver captura](02_Evidencias/01_workflow_aprobado.png) |
| Rechazo humano | [Ver captura](02_Evidencias/04_workflow_rechazado.png) |
| Ruta de error técnico | [Ver captura](02_Evidencias/02_ruta_error.png) |
| Diagrama de arquitectura | [Ver diagrama](02_Evidencias/Diagrama_Arquitectura_IA.png) |
| Dashboard de control | [Ver captura](02_Evidencias/03_dashboard_kpis_final.png) |

---

## Base de datos y dashboard de Airtable

La base de datos está organizada en tres tablas: **Leads, Propiedades y Configuración**.

Leads registra las consultas y sus resultados, mientras que Propiedades almacena la información comercial utilizada por la IA. Ambas tablas están vinculadas mediante el campo `Propiedad`.

### Vistas compartidas

- [Consultar tabla Leads](https://airtable.com/appoLo3gLVfHx5iIF/shrwvJbGebLtuwAh3)
- [Consultar tabla Propiedades](https://airtable.com/appoLo3gLVfHx5iIF/shrpfKFoCHG79JeRQ)

### Dashboard público de KPIs

[Acceder al Dashboard KPIs - Entrega Final](https://airtable.com/appoLo3gLVfHx5iIF/shrj8zRblXtf9BYEq)

| Indicador | Resultado |
|---|---:|
| Tasa de aprobación | 83% |
| Volumen de salida | 5 |
| Tasa de error IA | 14% |

Los cálculos y los indicadores adicionales de ahorro operativo están documentados en [Dashboard y KPIs](01_Documentacion/05_Dashboard_KPIs.md).

---

**Proyecto final — Ecosistema de Automatización IA Autónomo para Negocios.**
