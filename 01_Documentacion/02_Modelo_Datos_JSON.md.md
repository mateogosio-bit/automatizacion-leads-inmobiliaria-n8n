


# 2. Modelo de datos y estructura JSON

## Descripción general

El proyecto utiliza Airtable como base de datos para registrar las consultas inmobiliarias, consultar información de las propiedades y administrar los parámetros de aprobación humana.

La información se organiza en tres tablas:

1. Leads
2. Propiedades
3. Configuración

Esta estructura permite mantener los datos organizados y actualizar la información comercial sin modificar la lógica principal de n8n.

---

## 1. Tabla Leads

Esta tabla almacena las consultas recibidas desde Gmail y registra su evolución durante el proceso de automatización.

| Campo | Función |
|---|---|
| ID Lead | Identificador del registro |
| Fecha ingreso | Fecha y hora de recepción |
| Nombre | Nombre extraído del remitente |
| Email | Correo electrónico del cliente |
| Asunto | Asunto original del mensaje |
| Mensaje | Contenido de la consulta |
| Propiedad | Campo vinculado con la tabla Propiedades |
| Presupuesto USD | Presupuesto declarado por el cliente |
| Prioridad | Clasificación Alta, Media o Baja generada por IA |
| Resumen IA | Resumen de la consulta |
| Respuesta IA | Respuesta propuesta para el cliente |
| Estado | Estado actual del proceso |
| Thread ID Gmail | Identificador del hilo de correo |
| Detalle Error | Descripción técnica del error |
| Fecha envío | Fecha y hora de respuesta |
| Indicador de error | Campo utilizado para calcular errores |
| Índice de aprobación | Campo utilizado para calcular aprobaciones |
| Tiempo ahorrado | Estimación del tiempo ahorrado |
| Ahorro económico | Estimación económica del tiempo ahorrado |

### Estados del lead

El workflow utiliza los siguientes estados:

- Pendiente
- Procesado por IA
- Pendiente de aprobación
- Aprobado por Humano
- Enviado
- Rechazado por Humano
- Error

El estado del registro se actualiza según el resultado de cada ejecución.

---

## 2. Tabla Propiedades

Contiene la información comercial de las propiedades utilizadas por la inmobiliaria.

| Campo | Función |
|---|---|
| Código de propiedad | Identificador interno de la propiedad |
| Nombre Propiedad | Nombre utilizado para localizar coincidencias |
| Precio USD | Precio registrado en Airtable |
| Estado / disponibilidad | Indica si está disponible, vendida u otro estado comercial |

Cuando llega una consulta, el workflow busca la propiedad mencionada por el cliente.

La información obtenida se utiliza como contexto para el análisis de IA.

El modelo tiene instrucciones de no inventar precios ni disponibilidad cuando esa información no está presente en Airtable.

---

## 3. Tabla Configuración

Esta tabla permite administrar determinados parámetros del workflow sin modificar directamente los nodos de n8n.

| Campo | Función |
|---|---|
| Clave | Identifica el parámetro configurado |
| Email aprobación | Dirección del responsable de aprobar las respuestas |

El nodo "Buscar email de aprobación" consulta esta tabla para identificar a qué dirección debe enviarse la solicitud de validación humana.

Esto evita que el correo del responsable quede configurado como un valor fijo dentro del workflow.

---

## 4. Relaciones entre tablas

Las tablas Leads y Propiedades están vinculadas mediante el campo `Propiedad`, configurado en Airtable como **Linked Record**.

Esta relación permite asociar una consulta con una o varias propiedades del catálogo, ya que está habilitada la vinculación a múltiples registros.

De esta manera, los datos comerciales permanecen centralizados en Propiedades, mientras que Leads registra las consultas y sus estados.

La tabla Configuración funciona de manera independiente y almacena los parámetros operativos del workflow, como el correo del responsable de aprobación.


### Esquema de relaciones

```text
PROPIEDADES
    |
    | Linked Record
    |
  LEADS


CONFIGURACIÓN
    |
    | Consulta del email
    |
   n8n
```

---

## 5. Transferencia de datos mediante JSON

n8n utiliza estructuras JSON para transmitir información entre los diferentes nodos.

El proceso combina los datos del correo recibido con la información de Airtable antes de solicitar el análisis de IA.

### 5.1 Datos provenientes de Gmail

Ejemplo ilustrativo:

```json
{
  "nombre": "Lucía Fernández",
  "email": "lucia@example.com",
  "asunto": "Consulta por Villa Golf",
  "mensaje": "Estoy interesada en Villa Golf...",
  "thread_id": "18f..."
}
```

Estos datos permiten identificar al cliente y conservar la referencia del correo original.

### 5.2 Información de la propiedad

Ejemplo ilustrativo de un registro consultado en Airtable:

```json
{
  "codigo_propiedad": "PR013",
  "nombre_propiedad": "Villa Golf",
  "precio_usd": 670000,
  "estado": "Disponible"
}
```

Esta información se utiliza como contexto durante el análisis de la consulta.

### 5.3 Respuesta generada por la IA

El modelo GPT-OSS Safeguard 20B, ejecutado mediante Groq, genera una respuesta estructurada en JSON.

Ejemplo ilustrativo:

```json
{
  "prioridad": "Alta",
  "resumen": "Cliente interesado en Villa Golf con intención de coordinar una visita.",
  "respuesta": "Hola Lucía, gracias por tu consulta...",
  "codigo_propiedad": "PR013",
  "presupuesto_usd": 700000
}
```

### Estructura de la respuesta

| Campo | Tipo | Función |
|---|---|---|
| prioridad | String | Alta, Media o Baja |
| resumen | String | Síntesis de la consulta |
| respuesta | String | Texto propuesto para el cliente |
| codigo_propiedad | String / null | Identificador de la propiedad |
| presupuesto_usd | Number / null | Presupuesto declarado por el cliente |

El presupuesto solamente debe registrarse cuando el cliente lo declara.

---

## 6. Mapeo de variables en n8n

Una vez obtenido el resultado del análisis, el workflow utiliza expresiones para extraer los valores del JSON y guardarlos en Airtable.

### Prioridad

`{{ $json.output.parseJson().prioridad }}`

### Resumen IA

`{{ $json.output.parseJson().resumen }}`

### Respuesta IA

`{{ $json.output.parseJson().respuesta }}`

### Presupuesto USD

`{{ $json.output.parseJson().presupuesto_usd ?? null }}`

Estas expresiones permiten completar los campos de Airtable con los resultados obtenidos del modelo de IA.

---

## 7. Relación entre los datos y la aprobación humana

Una vez registrado el análisis, n8n consulta la tabla Configuración para obtener el correo del responsable.

El responsable recibe la propuesta generada por IA y puede aprobarla o rechazarla.

Según su decisión, el registro de Leads se actualiza:

- Aprobado: continúa el proceso hacia el envío.
- Rechazado: registra el rechazo y finaliza sin responder.
- Error técnico: registra el error y detiene la ruta normal.

Este mecanismo permite mantener la trazabilidad de cada consulta.

---

## 6. Mapeo de variables en n8n

Una vez obtenido el resultado del análisis, el workflow utiliza expresiones para extraer los valores del JSON y guardarlos en Airtable.

### Prioridad

`{{ $json.output.parseJson().prioridad }}`

### Resumen IA

`{{ $json.output.parseJson().resumen }}`

### Respuesta IA

`{{ $json.output.parseJson().respuesta }}`

### Presupuesto USD

`{{ $json.output.parseJson().presupuesto_usd ?? null }}`

Estas expresiones permiten completar los campos de Airtable con los resultados obtenidos del modelo de IA.

---

## 7. Relación entre los datos y la aprobación humana

Una vez registrado el análisis, n8n consulta la tabla Configuración para obtener el correo del responsable.

El responsable recibe la propuesta generada por IA y puede aprobarla o rechazarla.

Según su decisión, el registro de Leads se actualiza:

- Aprobado: continúa el proceso hacia el envío.
- Rechazado: registra el rechazo y finaliza sin responder.
- Error técnico: registra el error y detiene la ruta normal.

Este mecanismo permite mantener la trazabilidad de cada consulta.

---
