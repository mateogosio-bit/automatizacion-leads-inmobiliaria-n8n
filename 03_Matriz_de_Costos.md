
# 3. Optimización de costos y selección del modelo de IA

## Objetivo

El proyecto busca automatizar las consultas inmobiliarias manteniendo un costo operativo bajo.

Para ello, se compararon diferentes modelos de inteligencia artificial, considerando sus precios por token, la complejidad de las tareas y las posibilidades de procesamiento por lotes (Batch).

El workflow utiliza el modelo `openai/gpt-oss-safeguard-20b` mediante Groq.

Groq es el proveedor de inferencia. El prefijo `openai/` forma parte del identificador del modelo y no significa que se utilice directamente la API de OpenAI.

---

## 1. Matriz comparativa de modelos

La siguiente tabla reproduce los valores de referencia utilizados en la evaluación económica del proyecto.

| Modelo / modalidad | Entrada por 1M tokens | Salida por 1M tokens | Aplicación |
|---|---:|---:|---|
| GPT-OSS Safeguard 20B - Groq | USD 0,075 | USD 0,30 | Modelo utilizado en el workflow |
| GPT-5 Mini - OpenAI | USD 0,25 | USD 2,00 | Clasificación y respuestas estructuradas |
| Claude Sonnet 5 - Anthropic | USD 2,00 | USD 10,00 | Procesamiento de tareas más complejas |
| Batch API | Descuento estimado del 50% | Descuento estimado del 50% | Procesamiento asíncrono compatible |

Los valores corresponden a la matriz de referencia elaborada para la entrega del 2 de octubre de 2026. Deben verificarse contra las tarifas oficiales vigentes antes de utilizarlos como precios actuales.

El descuento Batch no es universal: depende del proveedor, modelo y modalidad de ejecución.

---

## 2. Supuestos del análisis económico

Para comparar los modelos se definió un escenario teórico de 1.000 consultas inmobiliarias.

Se utilizaron los siguientes supuestos:

| Variable | Valor |
|---|---:|
| Cantidad de consultas | 1.000 |
| Tokens de entrada por consulta | 800 |
| Tokens de salida por consulta | 250 |
| Total de tokens de entrada | 800.000 |
| Total de tokens de salida | 250.000 |

Estos consumos son estimaciones comparativas y no mediciones reales del workflow.

El costo de cada alternativa se calcula mediante:

**Costo total = (Tokens de entrada / 1.000.000 × tarifa de entrada) + (Tokens de salida / 1.000.000 × tarifa de salida)**

---

## 3. Estimación de costos para 1.000 consultas

| Modelo | Costo de entrada | Costo de salida | Costo total |
|---|---:|---:|---:|
| GPT-OSS Safeguard 20B - Groq | USD 0,060 | USD 0,075 | USD 0,135 |
| GPT-5 Mini - OpenAI | USD 0,200 | USD 0,500 | USD 0,700 |
| GPT-5 Mini - Batch | — | — | USD 0,350 |
| Claude Sonnet 5 - Anthropic | USD 1,600 | USD 2,500 | USD 4,100 |
| Claude Sonnet 5 - Batch | — | — | USD 2,050 |

Los resultados se calculan utilizando los precios y supuestos establecidos en la matriz anterior.

No incluyen posibles costos adicionales de infraestructura, suscripciones, almacenamiento o ejecución de n8n y Airtable.

---

## 4. Evaluación de Batch API

Batch permite procesar solicitudes de inteligencia artificial por lotes, generalmente de manera asíncrona.

En el análisis se consideró un descuento teórico del 50% para modalidades compatibles de OpenAI y Anthropic.

### Ventajas

- Reducción del costo por procesamiento.
- Posibilidad de ejecutar grandes volúmenes de solicitudes.
- Conveniente para tareas que no necesitan una respuesta inmediata.

### Limitaciones

- El procesamiento no es necesariamente inmediato.
- No resulta adecuado para todas las automatizaciones.
- La disponibilidad y el ahorro dependen del proveedor y del modelo.

### Decisión aplicada

**No se utiliza Batch en el workflow operativo.**

El sistema necesita generar una respuesta para continuar hacia la aprobación humana sin esperar un procesamiento diferido.

Por esta razón, se decidió utilizar una llamada directa al modelo mediante Groq.

---

## 5. Estrategias de optimización implementadas

### Una solicitud de IA por consulta

El diseño operativo contempla una llamada principal a IA por lead, evitando análisis innecesarios.

Si ocurre una falla, el nodo puede ejecutar los reintentos configurados.

### Salida JSON estructurada

El prompt solicita una respuesta estructurada que incluya prioridad, resumen, respuesta propuesta, código de propiedad y presupuesto.

Esto facilita el procesamiento automático de la información.

### Límite de salida

El modelo tiene configurado un límite máximo de 1.000 tokens de salida por ejecución.

Este límite busca controlar la extensión de las respuestas y reducir el consumo innecesario.

### Contexto desde Airtable

La información comercial de la propiedad se consulta en Airtable y se utiliza como contexto para el modelo.

De esta manera, la IA puede trabajar con datos previamente registrados en lugar de generar información comercial sin respaldo.

---

## 6. Ahorro operativo del proyecto

Además del costo de inferencia, el dashboard de Airtable registra indicadores de ahorro de tiempo y dinero.

Resultados documentados en la muestra final:

| Indicador | Resultado |
|---|---:|
| Volumen de salida | 5 |
| Tiempo ahorrado estimado | 1 hora y 3 minutos |
| Costo horario supuesto | USD 8 |
| Ahorro económico estimado | USD 8,40 |

### Cálculo

63 minutos equivalen a 1,05 horas.

**1,05 horas × USD 8 por hora = USD 8,40**

Este valor representa un ahorro operativo estimado en tiempo de trabajo, no una ganancia económica efectivamente comprobada.

---

## 7. Conclusión

Para este proyecto se seleccionó GPT-OSS Safeguard 20B mediante Groq, considerando el escenario de costos analizado y la necesidad de generar respuestas durante la ejecución del workflow.

Batch se descartó para el flujo principal porque el procesamiento necesita continuar hacia la aprobación humana.

La combinación de consultas a Airtable, respuestas JSON y control de tokens busca mantener una automatización eficiente y económicamente viable.
