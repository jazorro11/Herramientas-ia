# Technical Brief

---

## 1. Título de la tarea

Nombre claro y específico de la funcionalidad o servicio a implementar.

> 💡 **Pregunta guía:** ¿Puedes describir la tarea en una sola oración que incluya qué se hace, sobre qué entidad y con qué propósito? Si no puedes, probablemente la tarea es demasiado amplia y conviene dividirla.

Ejemplo:
> Servicio Desacoplado de Cálculo de Impuestos

---

## 2. Contexto

Describe el problema actual y por qué se necesita esta solución.

Incluye:
- Cómo funciona el sistema actualmente
- Qué problema existe (acoplamiento, deuda técnica, escalabilidad, etc.)
- Qué se busca lograr con esta implementación

> 💡 **Preguntas guía:**
> - ¿Qué pasa hoy si este componente falla o cambia? ¿A cuántos otros módulos afecta?
> - ¿Existe documentación del comportamiento actual o hay que inferirlo del código?
> - ¿Cuál es el origen de esta tarea: bug, requerimiento de negocio, deuda técnica, performance?
> - ¿Hay decisiones de diseño pasadas que esta tarea deba respetar o que explícitamente busque reemplazar?

Ejemplo de estructura:

El sistema actual **___**.
Esto genera problemas como **___**.
El objetivo de esta tarea es **___** para mejorar **___**.

---

## 3. Requerimientos técnicos

### Lenguaje / Stack

> 💡 **Preguntas guía:**
> - ¿Existe una versión mínima impuesta por el entorno de producción o por otras dependencias del proyecto?
> - ¿Se está introduciendo un framework nuevo o se continúa con el stack existente?
> - ¿Hay restricciones de licencia que afecten la elección de librerías?

- Lenguaje:
- Versión mínima:
- Framework (si aplica):

Ejemplo:
- Python 3.9+
- FastAPI
- PostgreSQL

---

### Arquitectura

Describe patrones o principios a usar.

> 💡 **Preguntas guía:**
> - ¿El resto del sistema ya usa algún patrón arquitectónico que este componente deba respetar para mantener consistencia?
> - ¿Se necesita que el servicio sea stateless para escalar horizontalmente?
> - ¿Hay algún patrón que se haya descartado explícitamente y por qué?
> - ¿Cómo se gestionan las dependencias (inyección, contenedor IoC, instanciación directa)?

Ejemplo:
- Clean Architecture
- Strategy Pattern
- Dependency Injection
- Stateless service

---

### Input esperado

Define los datos de entrada.

> 💡 **Preguntas guía:**
> - ¿De dónde provienen estos datos: otro servicio, base de datos, llamada HTTP, evento?
> - ¿Algún campo puede ser nulo u opcional? ¿Cuál es el comportamiento esperado en ese caso?
> - ¿Existen validaciones de formato, rango o negocio que deban aplicarse antes de procesar?

Ejemplo:
```python
InputObject
- field_1: type        # descripción breve y restricciones (ej: no nulo, > 0)
- field_2: type        # descripción breve (ej: código ISO 4217 de moneda)
```

---

### Output esperado

Define los datos de salida.

> 💡 **Preguntas guía:**
> - ¿Quién consume este output: otro servicio, una API, una capa de presentación?
> - ¿Qué debe devolver el servicio en caso de error o cuando no hay resultados?
> - ¿El output debe cumplir algún contrato o schema ya definido (OpenAPI, Protobuf, etc.)?

Ejemplo:
```python
OutputObject
- result_field_1: type   # descripción breve (ej: monto calculado, siempre >= 0)
- result_field_2: type   # descripción breve (ej: desglose por tipo de impuesto)
```

---

## 4. Constraints (Restricciones)

> 💡 **Preguntas guía:**
> - ¿Hay librerías que el equipo ya aprobó y deben preferirse sobre alternativas?
> - ¿Existe alguna restricción de performance (tiempo máximo de respuesta, uso de memoria)?
> - ¿Hay consideraciones de seguridad específicas (sanitización de inputs, manejo de datos sensibles)?
> - ¿Se deben respetar convenciones de nombres o estructura de carpetas ya establecidas en el proyecto?

- No usar librerías externas salvo las aprobadas por el equipo.
- Implementar type hints en todo el código.
- Seguir principios SOLID y buenas prácticas del lenguaje.
- Separar claramente el dominio, estrategias y servicios principales.
- _(agrega aquí restricciones específicas de este proyecto o tarea)_

---

## 5. Definition of Done (DoD)

El trabajo se considera terminado cuando:

> 💡 **Preguntas guía:**
> - ¿Quién debe revisar y aprobar el código antes de mergear (par, tech lead, arquitecto)?
> - ¿Hay criterios de aceptación funcionales definidos por producto o negocio que deban verificarse?
> - ¿Se requiere actualizar documentación técnica, diagramas o el README del módulo?
> - ¿El cambio requiere un plan de rollback o feature flag para desplegarse de forma segura?

- [ ] El código pasa linters y convenciones de estilo adoptadas por el equipo (ej: `flake8`, `black`).
- [ ] La cobertura de tests unitarios es al menos 90%.
- [ ] Los tests de integración relevantes pasan sin errores.
- [ ] El código fue revisado y aprobado por al menos un par del equipo.
- [ ] La documentación (docstrings, README del módulo) está actualizada si hubo cambios en la interfaz pública.
- [ ] No se introducen regresiones en funcionalidades existentes (suite de regresión en verde).
- [ ] _(agrega criterios adicionales específicos de este proyecto o tarea)_
