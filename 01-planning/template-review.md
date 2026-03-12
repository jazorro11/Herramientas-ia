# Checklist de Revisión

---

## 1. Alucinaciones de librerías

### Pregunta clave

¿Ese import realmente existe?

La IA puede inventar:
- librerías
- funciones
- APIs
- métodos

> 💡 **Preguntas guía:**
> - ¿Ejecutaste el código localmente al menos una vez antes de hacer el review? Un import falso falla en el primer `import`.
> - ¿La función que usa la IA aparece en la documentación oficial de esa versión específica, o solo en versiones más nuevas/antiguas?
> - ¿El nombre del paquete es el mismo que el del módulo que se importa? (ej: se instala `Pillow` pero se importa `PIL` — confusiones así son comunes).

**Cómo verificar rápidamente:**

```bash
# Python — ¿el paquete existe y está instalado?
pip show nombre-paquete

# ¿Existe en PyPI?
pip index versions nombre-paquete

# Node.js — ¿el paquete existe en npm?
npm info nombre-paquete
```

**Ejemplo de alucinación real:**
La IA genera `from langchain.retrievers import SelfQueryRetriever` pero en la versión instalada (`langchain==0.0.150`) ese módulo todavía no existía. Se lanza `ImportError` en runtime.

**Verificar:**
- documentación oficial
- existencia real del paquete
- compatibilidad con la versión usada

### Checklist

- [ ] Los imports existen
- [ ] Las funciones usadas son reales
- [ ] La API corresponde a la versión actual

---

## 2. Lógica de negocio sutil

### Pregunta clave

¿La lógica es realmente correcta?

**Errores comunes generados por IA:**
- cálculos incorrectos
- redondeos incorrectos
- manejo incorrecto de fechas
- uso de float para dinero

> 💡 **Preguntas guía:**
> - ¿Hay algún experto de dominio (del negocio, no técnico) que pueda validar los cálculos con un ejemplo concreto?
> - ¿Se probaron los valores límite? Por ejemplo: cantidad = 0, fecha de inicio = fecha de fin, lista vacía.
> - ¿Los redondeos se aplican al final del cálculo o en pasos intermedios? (Los redondeos intermedios acumulan error.)
> - ¿El código funciona igual en zonas horarias distintas a UTC? ¿Se almacenan fechas con o sin zona horaria?

**Casos concretos a verificar manualmente:**

| Escenario | Qué revisar |
|---|---|
| Dinero / precios | ¿Se usa `Decimal` en lugar de `float`? `0.1 + 0.2 != 0.3` en float. |
| Fechas | ¿`datetime.now()` vs `datetime.utcnow()` vs `datetime.now(tz=utc)`? |
| Rangos | ¿El rango es `[inicio, fin)` o `[inicio, fin]`? ¿Coincide con el brief? |
| Porcentajes | ¿El cálculo es `valor * porcentaje / 100` o `valor * (porcentaje / 100)`? ¿Importa? |
| División entera | ¿`//` cuando se necesita `/` o viceversa? |

```python
# MAL — float para dinero
precio = 19.99
descuento = 0.10
total = precio * (1 - descuento)  # puede dar 17.991000000000003

# BIEN — Decimal para dinero
from decimal import Decimal
precio = Decimal("19.99")
descuento = Decimal("0.10")
total = precio * (1 - descuento)  # da 17.991 exacto
```

### Checklist

- [ ] Los cálculos son correctos
- [ ] El manejo de fechas es consistente
- [ ] No se usa float para dinero
- [ ] Edge cases están contemplados

---

## 3. Seguridad

### Pregunta clave

¿El código introduce riesgos de seguridad?

La IA puede generar código funcional pero inseguro.

**Revisar:**
- validación de inputs
- inyección (SQL, comandos, etc.)
- exposición de secretos
- manejo de datos sensibles

> 💡 **Preguntas guía:**
> - ¿Algún valor que ingresa el usuario se concatena directamente a una query, comando de shell o path de archivo?
> - ¿Los mensajes de error o logs incluyen tokens, contraseñas, o datos personales que no deberían salir del sistema?
> - ¿Las credenciales (API keys, DB passwords) vienen de variables de entorno o están hardcodeadas/en comentarios?
> - ¿Se usa `eval()`, `exec()`, `subprocess` con inputs del usuario sin sanitizar?

**Ejemplo de inyección SQL — MAL vs BIEN:**

```python
# MAL — vulnerable a SQL injection
user_input = request.args.get("name")
query = f"SELECT * FROM users WHERE name = '{user_input}'"
cursor.execute(query)

# BIEN — query parametrizada
user_input = request.args.get("name")
cursor.execute("SELECT * FROM users WHERE name = %s", (user_input,))
```

**Ejemplo de secreto en log — MAL vs BIEN:**

```python
# MAL — expone el token en logs
logger.info(f"Llamando API con token={api_token}")

# BIEN — nunca loggear el valor de credenciales
logger.info("Llamando API con token configurado")
```

### Checklist

- [ ] Inputs están validados
- [ ] No hay riesgo de inyección
- [ ] No se exponen credenciales
- [ ] No se filtran datos sensibles

---

## 4. Context window

### Pregunta clave

¿La IA olvidó algo del brief?

Cuando el contexto es largo la IA puede ignorar:
- constraints
- decisiones arquitectónicas
- requisitos técnicos
- Definition of Done

> 💡 **Preguntas guía:**
> - ¿Leíste el `template-brief` original antes de empezar el review? Tenerlo a la vista evita comparar de memoria.
> - ¿El código generado agrega dependencias que el brief explícitamente prohibía?
> - ¿La arquitectura implementada coincide con los patrones definidos en la sección de Arquitectura del brief, o la IA eligió uno diferente sin justificación?
> - ¿Todos los ítems de la Definition of Done tienen evidencia de cumplimiento (tests, linter output, etc.)?

**Método de verificación sugerido:**

1. Abre el brief en paralelo al código.
2. Recorre sección por sección: Stack → Arquitectura → Input/Output → Constraints → DoD.
3. Para cada constraint, busca en el código la evidencia de que se respeta.

```bash
# Ejemplo: verificar que no se usó una librería prohibida (ej: requests en un proyecto que usa httpx)
grep -r "import requests" src/
```

### Checklist

- [ ] El código respeta el brief original
- [ ] Se cumplen los constraints definidos
- [ ] No se agregaron dependencias innecesarias
- [ ] Se cumple la Definition of Done

---

## 5. Punto personalizado del proyecto

Este punto depende de tu stack o arquitectura.

> 💡 **Preguntas guía para definir este punto:**
> - ¿Qué tipo de bug o problema se repite con más frecuencia en este proyecto? Ese es un buen candidato para este punto.
> - ¿Hay un área (tests, observabilidad, performance) que el equipo haya identificado como crítica y que la IA tiende a ignorar?
> - ¿Existe algún contrato externo (SLA, schema de API, formato de eventos) que siempre deba verificarse?

**Ejemplos comunes por stack:**

| Stack / contexto | Qué verificar aquí |
|---|---|
| APIs REST | ¿Los status codes HTTP son los correctos? (`404` vs `400` vs `422`) |
| Eventos / mensajería | ¿El schema del evento es compatible con los consumidores existentes? |
| Microservicios | ¿Se actualiza el contrato de la API (OpenAPI/Protobuf) si cambió la interfaz? |
| Data pipelines | ¿Se manejan correctamente filas nulas, duplicados o formatos inesperados? |
| Frontend | ¿Se manejan los estados de carga, error y vacío en la UI? |

**Ejemplos comunes:**
- cobertura de tests
- métricas y observabilidad
- logging
- performance
- compatibilidad con arquitectura

### Checklist

- [ ] Tests cubren el código nuevo
- [ ] Logs no exponen datos sensibles
- [ ] Performance es aceptable
- [ ] Métricas funcionan correctamente
- [ ] _(agrega aquí el criterio específico de tu proyecto)_

---

## Resultado del Review

> 💡 **Antes de marcar como aprobado:**
> - ¿Se documentaron todos los hallazgos encontrados antes de solicitar correcciones a la IA? Tener un registro evita que se pierdan en la próxima iteración.
> - Si se encontraron problemas, ¿se re-ejecutó el checklist completo después de las correcciones, no solo el punto afectado?

- [ ] El código pasa los 5 puntos del protocolo
- [ ] Se hicieron correcciones necesarias
- [ ] El código está listo para commit

**Hallazgos encontrados:** _(documenta aquí cualquier problema detectado y cómo se resolvió)_

---

**Reviewer:** ____________________
**Fecha:** ____________________

---

## Uso dentro del repositorio

**Estructura recomendada:**

```
herramientas-ia/
│
├─ 01-planning/
│   ├─ template-brief.md
│   └─ template-review.md
│
└─ reflexion-semana-1.md
```
