# 1. Arquitectura del sistema

# Arquitectura del sistema

El workflow automatiza la recepción y gestión de consultas inmobiliarias recibidas por Gmail.  
n8n orquesta el proceso, Airtable funciona como base de datos operativa y Groq ejecuta el análisis con IA.  
Antes de responder al cliente, el flujo incorpora una aprobación humana obligatoria (HITL).

## Diagrama de arquitectura

```mermaid
flowchart TD
    A((Gmail Trigger))
    B[Registrar lead]
    C[Buscar propiedad]
    D[Analizar consulta]
    E[Registrar análisis]
    F[Marcar error]
    G[Buscar aprobador]
    H[Solicitar aprobación]
    I{¿Aprobada?}
    J[Marcar aprobado]
    K[Responder cliente]
    L[Marcar enviado]
    M[Marcar rechazado]

    A --> B
    B --> C
    C --> D

    D -->|Correcto| E
    D -->|Error| F

    E --> G
    G --> H
    H --> I

    I -->|Sí| J
    J --> K
    K --> L

    I -->|No| M

## Componentes y responsabilidades

| Componente | Función |
|---|---|
| Gmail Trigger | Recibe consultas y utiliza el filtro `in:inbox -from:me` para evitar bucles. |
| Airtable - Leads | Registra y actualiza cada consulta, su análisis y su estado. |
| Airtable - Propiedades | Proporciona precio, disponibilidad y código de la propiedad. |
| IA - Groq | Analiza la consulta y genera un JSON con prioridad, resumen, respuesta, código de propiedad y presupuesto. |
| Airtable - Configuración | Proporciona dinámicamente el correo del responsable de aprobación. |
| HITL - Gmail | Solicita aprobación humana antes de responder al cliente. |
| Ruta de error | Registra `Estado = Error` y `Detalle Error` sin continuar hacia el envío. |

## Rutas del workflow

**Camino aprobado:** el responsable aprueba la respuesta, se actualiza el estado del lead, se responde al cliente y se marca como enviado.

**Camino rechazado:** se registra el rechazo humano y no se envía ningún correo al cliente.

**Camino de error:** si falla el análisis de IA y no se recupera mediante los reintentos configurados, se registra el error técnico en Airtable y se detiene la ruta normal.

## Evidencia de ejecución

![Workflow aprobado](01_workflow_aprobado.png)

![Ruta de error](02_ruta_error.png)

## Archivos técnicos

- [Workflow exportado de n8n](ENTREGA_8_Gestion_consultas_inmobiliaria_GitHub.json)
- [Documentación técnica completa](Entrega_Final_Automatizacion_Inmobiliaria_2026.pdf)
