
# 4. Seguridad y resiliencia del sistema

## Descripción general

El workflow incorpora diferentes mecanismos para prevenir errores, proteger la información y evitar que una respuesta generada por inteligencia artificial sea enviada al cliente sin supervisión.

Estos controles permiten mantener la trazabilidad de las consultas y registrar los incidentes técnicos durante la ejecución.

---

## 1. Prevención de bucles

El Gmail Trigger utiliza el siguiente filtro:

`in:inbox -from:me`

Este filtro permite procesar mensajes recibidos en la bandeja de entrada y excluye los correos enviados desde la propia cuenta.

De esta manera, se busca evitar que una respuesta enviada por la automatización vuelva a activar el workflow.

El proceso también contempla el tratamiento de correos no leídos para reducir el reprocesamiento de consultas.

---

## 2. Aprobación humana obligatoria (HITL)

Antes de responder al cliente, el sistema incorpora una instancia de validación humana.

El procedimiento es el siguiente:

1. La IA analiza la consulta y propone una respuesta.
2. El análisis se registra en Airtable.
3. n8n consulta la tabla Configuración para obtener el correo del aprobador.
4. El nodo Solicitar Aprobación Humana envía la propuesta mediante Gmail.
5. El workflow espera la decisión del responsable.
6. El nodo ¿Aprobada? evalúa el resultado.

### Si la respuesta es aprobada

- Se actualiza el estado del lead a Aprobado por Humano.
- Se envía la respuesta al cliente.
- Se actualiza el estado a Enviado.

### Si la respuesta es rechazada

- Se actualiza el estado a Rechazado por Humano.
- El workflow finaliza sin enviar la respuesta al cliente.

**Regla de seguridad:** la IA no puede enviar directamente una respuesta al cliente sin aprobación humana.

### Evidencia del rechazo

La siguiente ejecución de n8n muestra que el nodo ¿Aprobada? toma la salida False y continúa hacia Marcar rechazado.

Los nodos encargados de responder al cliente no se ejecutan.

![Ejecución con rechazo humano](04_workflow_rechazado.png)

---

## 3. Manejo de errores técnicos

El nodo Analizar consulta con IA incorpora reintentos automáticos y una salida alternativa de error.

Si el análisis falla y no se recupera mediante los reintentos configurados, el proceso sigue una ruta diferente de la ejecución normal.

### Procedimiento ante un error

1. El nodo de IA detecta una falla de ejecución.
2. Se utiliza la salida de error.
3. El workflow continúa hacia el nodo Marcar error.
4. Airtable actualiza el registro correspondiente.
5. El campo Estado pasa a Error.
6. El campo Detalle Error almacena información técnica del incidente.

El flujo no continúa hacia la aprobación humana ni hacia el envío del correo al cliente.

Este mecanismo permite identificar las consultas que necesitan una revisión posterior.

### Evidencia de la ruta de error

La siguiente captura corresponde a una prueba controlada en la que se activa la ruta de error del análisis mediante IA.

![Ruta de error en n8n](02_ruta_error.png)

---

## 4. Variables dinámicas

El workflow utiliza información obtenida durante la ejecución, evitando depender de valores fijos para procesar las consultas.

| Variable | Origen | Utilización |
|---|---|---|
| Nombre | Gmail | Identificar al cliente |
| Email | Gmail | Registrar el lead y responder |
| Asunto | Gmail | Identificar el motivo de la consulta |
| Mensaje | Gmail | Analizar el contenido |
| Thread ID | Gmail | Conservar el hilo original |
| Código de propiedad | Airtable - Propiedades | Identificar la propiedad |
| Precio | Airtable - Propiedades | Proporcionar contexto comercial |
| Disponibilidad | Airtable - Propiedades | Informar el estado de la propiedad |
| Prioridad | IA mediante Groq | Clasificar la consulta |
| Resumen IA | IA mediante Groq | Sintetizar el mensaje |
| Respuesta IA | IA mediante Groq | Preparar la respuesta |
| Email del aprobador | Airtable - Configuración | Solicitar validación humana |

El correo del aprobador se obtiene mediante el nodo Buscar email de aprobación, por lo que no queda establecido como una dirección fija dentro del flujo.

---

## 5. Protección de credenciales

El sistema utiliza el gestor de credenciales de n8n para establecer las conexiones con Gmail, Airtable y Groq.

Las contraseñas y claves de acceso no se escriben directamente dentro de los prompts ni se incluyen como secretos visibles en la documentación del proyecto.

Este mecanismo permite separar las credenciales de la lógica del workflow.

---

## 6. Minimización de datos

El sistema registra la información necesaria para gestionar y dar seguimiento a las consultas inmobiliarias.

Entre los datos almacenados se encuentran:

- Nombre y correo electrónico del remitente.
- Asunto y contenido del mensaje.
- Propiedad relacionada.
- Presupuesto, cuando el cliente lo declara.
- Prioridad, resumen y respuesta propuesta por la IA.
- Estado del procesamiento.
- Thread ID de Gmail.
- Fecha de envío.
- Detalle técnico de error, cuando corresponde.

La información comercial de las propiedades se mantiene en Airtable y se utiliza como contexto durante el análisis.

---

## 7. Trazabilidad del proceso

La tabla Leads permite identificar el estado de cada consulta y conocer el resultado del procesamiento.

Los estados principales contemplados son:

- Pendiente.
- Procesado por IA.
- Pendiente de aprobación.
- Aprobado por Humano.
- Enviado.
- Rechazado por Humano.
- Error.

Los estados finales permiten diferenciar las consultas respondidas, las rechazadas por el responsable y las que presentaron errores técnicos.

Esto también permite alimentar los indicadores del dashboard de Airtable.

---

## 8. Resumen de controles implementados

| Control | Implementación | Objetivo |
|---|---|---|
| Anti-loop | Filtro Gmail `in:inbox -from:me` | Evitar procesar correos propios |
| HITL | Aprobación humana mediante Gmail | Evitar envíos sin autorización |
| Reintentos de IA | Configuración del nodo de análisis | Intentar recuperar fallas transitorias |
| Ruta de error | Salida alternativa hacia Airtable | Registrar incidentes técnicos |
| Variables dinámicas | Datos de Gmail, Airtable e IA | Evitar valores fijos innecesarios |
| Thread ID | Identificador de Gmail | Mantener la referencia al hilo |
| Credenciales de n8n | Gestor de credenciales | Evitar incluir secretos en el workflow |
| Estados de Airtable | Actualización de Leads | Mantener trazabilidad |

## 9. Alcance de la resiliencia

La ruta de error documentada corresponde al análisis mediante IA.

Las capturas presentadas demuestran el funcionamiento del rechazo humano y de la ruta de error del modelo.

