
# 5. Dashboard de Control y KPIs

## Descripción general

El proyecto utiliza Airtable como herramienta de seguimiento y control de la automatización de consultas inmobiliarias.

El dashboard permite visualizar los resultados del workflow de n8n mediante indicadores que se alimentan de los registros almacenados en la tabla Leads.

Estos indicadores permiten evaluar el volumen de consultas respondidas, las decisiones de aprobación humana, los errores técnicos y el ahorro operativo estimado.

---

## 1. Dashboard público de Airtable

Se creó una vista compartida llamada **Dashboard KPIs - Entrega Final**, que permite consultar los registros y los indicadores del sistema.

### Enlace de acceso

[Acceder al Dashboard KPIs de Airtable](https://airtable.com/appoLo3gLVfHx5iIF/shrj8zRblXtf9BYEq)

La vista contiene campos de fórmula y resúmenes que permiten calcular los indicadores directamente desde Airtable.

El acceso compartido es de solo lectura y no permite modificar los registros originales.

---

## 2. Indicadores principales

El dashboard presenta los tres indicadores obligatorios para la evaluación del proyecto.

| KPI | Resultado | Objetivo |
|---|---:|---|
| Tasa de aprobación | 83% | Medir el porcentaje de respuestas aprobadas por el responsable |
| Volumen de salida | 5 | Contabilizar las consultas respondidas al cliente |
| Tasa de error IA | 14% | Medir el porcentaje de consultas que finalizaron con un error técnico |

Los resultados corresponden a la muestra de siete consultas documentada en el dashboard.

---

## 3. Cálculo de los KPI

### KPI 1. Tasa de aprobación

Este indicador mide el porcentaje de respuestas aprobadas por el responsable humano.

Se utiliza el campo `Índice de aprobación` de la tabla Leads.

Los registros reciben los siguientes valores:

- **Aprobado / Enviado:** 100%.
- **Rechazado por Humano:** 0%.
- **Error sin decisión humana:** valor vacío.

Airtable calcula el promedio de los registros que llegaron a una decisión humana.

**Cálculo:**

5 respuestas aprobadas / 6 decisiones humanas × 100 = 83,33%.

**Resultado mostrado: 83%.**

Este indicador permite evaluar qué proporción de las respuestas generadas por IA obtiene la aprobación del responsable.

### KPI 2. Volumen de salida

Este indicador representa la cantidad de consultas que finalizaron con una respuesta enviada al cliente.

Se utiliza un campo de fórmula de Airtable llamado `Volumen de salida`.

**Fórmula utilizada:**

`IF({Estado} = "Enviado", 1, 0)`

La fórmula asigna:

- Valor 1 cuando el estado es Enviado.
- Valor 0 para los demás estados.

En la vista compartida se utiliza la función **Suma** para obtener el total de consultas enviadas.

**Resultado: 5 consultas enviadas.**

Este indicador permite medir el volumen de respuestas gestionadas mediante la automatización.

### KPI 3. Tasa de error IA

Este indicador permite identificar el porcentaje de consultas que finalizaron con un error técnico.

Se utiliza el campo `Indicador de error` de Airtable.

El campo identifica los registros cuyo estado es Error.

La tasa se calcula mediante el promedio de los valores del indicador.

**Cálculo:**

1 consulta con error / 7 consultas registradas × 100 = 14,29%.

**Resultado mostrado: 14%.**

Este indicador permite evaluar la frecuencia de errores registrados durante el procesamiento.

---

## 4. Indicadores complementarios

Además de los tres KPI principales, el dashboard incorpora indicadores de ahorro operativo.

| Indicador | Resultado |
|---|---:|
| Tiempo ahorrado estimado | 1:03:00 |
| Ahorro económico estimado | USD 8,40 |

### Tiempo ahorrado

Este indicador estima el tiempo de trabajo manual que se evita mediante la automatización del procesamiento de consultas inmobiliarias.

El sistema registra el tiempo ahorrado en Airtable y permite calcular su valor acumulado.

**Resultado: 1 hora y 3 minutos.**

### Ahorro económico estimado

Este indicador transforma el tiempo ahorrado en una estimación económica.

Para el cálculo se utiliza un costo operativo supuesto de **USD 8 por hora**.

**Cálculo:**

1 hora y 3 minutos = 1,05 horas.

1,05 horas × USD 8 = USD 8,40.

**Resultado: USD 8,40 de ahorro económico estimado.**

Este valor representa una estimación de ahorro operativo y no una ganancia económica efectivamente comprobada.

---

## 5. Actualización y funcionamiento del dashboard

Los indicadores se alimentan de la tabla Leads de Airtable, donde n8n registra y actualiza la información de las consultas.

El funcionamiento es el siguiente:

1. Gmail recibe una nueva consulta inmobiliaria.
2. n8n registra el lead en Airtable.
3. La IA analiza el mensaje y genera una respuesta propuesta.
4. Se solicita aprobación humana.
5. Según el resultado, Airtable actualiza el estado del registro.
6. Los campos de fórmula y los cálculos del dashboard reflejan la información registrada.

Los principales estados utilizados son:

- Enviado.
- Rechazado por Humano.
- Error.

De esta manera, el dashboard permite identificar los resultados del procesamiento y mantener un seguimiento del funcionamiento de la automatización.

Los indicadores se actualizan cuando se modifican los registros utilizados para sus cálculos.

---

## 6. Evidencia visual

La siguiente captura muestra el dashboard de control desarrollado en Airtable, donde se visualizan los cinco indicadores de rendimiento y la distribución de consultas por estado.

![Dashboard de control de Airtable](03_dashboard_kpis_final.png)
