## 0. Metadatos del prompt

| Campo | Valor |
|-------|-------|
| ID del prompt | `PR-FSD-FTGO-001` |
| Título | Generador de FSD Ligero para FTGO |
| Artefacto origen | docs/PRD.md / Brief de FTGO (Anexo A) |
| ID origen | `PRD-REQ-05 / Brief-FTGO-01` |
| Tipo de prompt | generación / transformación |
| Modelo recomendado | Sonnet / Gemini 3.5 Pro |
| Temperatura | `0.2` |
| Versión | `v1.1-mejorada` |
| Fecha | `24/05/2026` |
| Autor(es) | Antigravity AI & Andres Merida |
| Estado | Aprobado |

---

## 1. Anatomía del prompt (contenido principal)

### 1.1 Role

Eres un analista funcional senior especializado en marketplaces de delivery de comida rápida, con una profunda experiencia en el modelado de requisitos, la documentación de Casos de Uso (UCs) y la especificación formal usando sintaxis Gherkin (BDD) trazable a metas de negocio. Conoces al detalle el caso de estudio **FTGO** y tienes experiencia colaborando estrechamente con equipos de desarrollo y aseguramiento de calidad (QA).

### 1.2 Task

A partir del documento `docs/PRD.md` ya generado y de las 3 user stories semilla del brief (Anexo A), produce un Documento de Especificación Funcional (FSD) ligero en formato Markdown que formalice e implemente al menos 5 Casos de Uso (UCs) completos. Cada Caso de Uso debe incluir su metadata formal, flujos detallados y bloques de aceptación en sintaxis Gherkin (`Given/When/Then`) explícita.

### 1.3 Context

*   **Documento fuente primario**: El `docs/PRD.md` de la aplicación (que provee las 7 capacidades de negocio y los requisitos no funcionales).
*   **Documento fuente secundario**: El brief de negocio y requerimientos del Anexo A de `docs/contexto.md` (con las user stories US-01, US-02, US-03 y restricciones técnicas).
*   **Restricciones**: Cada Caso de Uso documentado debe poder rastrearse directamente a una de las User Stories semilla, a una capacidad descrita en el PRD o a una restricción técnica o capítulo del libro *Microservices Patterns*. Los UCs derivados deben citar estrictamente su origen.

#### Casos de Uso (UCs) a Cubrir
Para garantizar la integridad y evitar improvisación del modelo, se debe modelar exactamente el siguiente listado de 5 Casos de Uso:
1.  **UC-01: Registro y Confirmación de Pedidos** (Deriva de la US-01 y capacidad *Order Taking*). Actor primario: Consumidor.
2.  **UC-02: Gestión y Aceptación de Cocina de Tickets** (Deriva de la US-02 y capacidad *Order Fulfillment / Kitchen*). Actor primario: Restaurante.
3.  **UC-03: Asignación Dinámica de Envíos** (Deriva de la US-03 y capacidad *Delivery*). Actor primario: Courier.
4.  **UC-04: Procesamiento de Cobros mediante Stripe / QR** (Deriva de la capacidad *Billing & Accounting* y del Cap 3 del libro). Actor primario: Consumidor.
5.  **UC-05: Seguimiento y Tracking GPS en Tiempo Real** (Deriva de los requisitos de latencia y UX del PRD y capacidad *Delivery*). Actor primario: Consumidor.

#### Conexión con el Entorno de Automatización y Testing
> [!IMPORTANT]
> Los bloques ` ```gherkin ... ``` ` generados en la sección de Given/When/Then de cada Caso de Uso son la entrada directa de la Skill canónica de testing [SKILL.md](file:///Users/andresmerida/workspace/githup/ai-test-lab/docs/prompts/SKILL.md) (`fsd-gherkin-a-tests-aceptacion`). Por tanto, la sintaxis Gherkin debe ser impecable, utilizando precondiciones realistas y parámetros del dominio de negocio para que la Skill pueda interpretarlos y generar tests unitarios JUnit y scripts k6 exitosamente.

### 1.4 Reasoning (chain‑of‑thought estructurado)

Sigue estos pasos en orden para estructurar y razonar la respuesta:
1.  **Fase de Análisis**: Lee `docs/PRD.md` and `docs/contexto.md`. Mapea las capacidades a los 5 UCs definidos obligatoriamente.
2.  **Fase de Aplicación de Granularidad**: Determina la diferencia entre un flujo alternativo y un Caso de Uso nuevo basándote en la siguiente regla estricta:
    *   *Regla de Granularidad*: Un escenario constituye un nuevo UC cuando: (a) involucra a un Actor Primario diferente, (b) tiene una precondición significativamente diferente, o (c) el resultado aporta un valor de negocio independiente y atómico. Constituye un flujo alternativo si ocurre dentro del mismo contexto temporal y relacional del flujo principal del mismo Actor Primario, resolviendo un camino de error o excepción (ej. rechazo de tarjeta en UC-04, timeout de asignación en UC-03).
3.  **Fase de Documentación de UCs**: Redacta los 5 UCs. Para cada uno, completa de manera rigurosa los 7 campos del esqueleto del Output, asegurándose de incluir al menos 1 flujo principal, 2 flujos alternativos (caminos de fallo) y 1 bloque formal de Gherkin.
4.  **Fase de Trazabilidad**: Añade una tabla resumen de trazabilidad al final relacionando `UC -> Capacidad PRD -> Requisito Origen`.
5.  **Fase de Revisión**: Verifica que todos los bloques Gherkin estén bien formateados y no tengan placeholders del tipo "..." o "TODO".

### 1.5 Stop condition

Detente cuando:
- El FSD final contenga las 3 secciones obligatorias declaradas en el Output completamente desarrolladas.
- Haya exactamente 5 Casos de Uso (UC-01 al UC-05) completamente detallados con sus 7 campos.
- Cada Caso de Uso cuente con al menos un bloque Gherkin válido con sentencias Dado / Cuando / Entonces realistas.
- **Criterio cuantitativo de completitud**: La salida total en Markdown tenga entre 2000 y 4000 palabras (asegurando un alto nivel de detalle operativo y precondiciones completas) y carezca de placeholders, notas del asistente o bloques de texto inacabados.
No continues produciendo contenido más allá de estas condiciones.

### 1.6 Output

Formato: Markdown estructurado.

#### Estructura del FSD Requerida:
```markdown
# Functional Specification Document (FSD) - FTGO Platform

## 1. Introducción y Alcance
[Inserta aquí un párrafo que describa el propósito del FSD como puente entre los requerimientos de producto (PRD) y el código de backend y testing automatizado por `docs/prompts/SKILL.md`]

## 2. Tabla Resumen de Casos de Uso
| ID | Caso de Uso | Actor Primario | Capacidad PRD | Origen (Brief/Libro) |
|---|---|---|---|---|
| UC-01 | Registro y Confirmación de Pedidos | Consumidor | Order Taking | US-01 (Brief) |
| UC-02 | Gestión y Aceptación de Cocina de Tickets | Restaurante | Order Fulfillment | US-02 (Brief) |
| UC-03 | Asignación Dinámica de Envíos | Courier | Delivery | US-03 (Brief) |
| UC-04 | Procesamiento de Cobros mediante Stripe / QR | Consumidor | Billing & Accounting | Cap 3 / Billing (PRD) |
| UC-05 | Seguimiento y Tracking GPS en Tiempo Real | Consumidor | Delivery | UX Latencia (PRD) |

## 3. Detalle de Casos de Uso

### UC-01: Registro y Confirmación de Pedidos

| Campo | Valor |
|---|---|
| ID | UC-01 |
| Actor Primario | Consumidor |
| Capacidad PRD | Order Taking |
| Origen | US-01 (Brief) |

**Precondiciones**:
* El consumidor ha iniciado sesión y tiene una sesión activa en la app.
* El consumidor tiene seleccionado un restaurante con estado activo y menú disponible.

**Flujo Principal**:
1. El Consumidor selecciona ítems del menú del restaurante y los añade a su carrito de compras.
2. El sistema valida la disponibilidad y stock de cada ítem en tiempo real.
3. El Consumidor confirma la dirección de entrega y el método de pago predeterminado.
4. El sistema calcula el total de la orden, incluyendo costos de delivery e impuestos.
5. El sistema crea el pedido en estado `PENDING_PAYMENT` y genera un ID de pedido único.

**Flujos Alternativos**:
* **1a. Ítem sin stock**:
  1. El sistema notifica al Consumidor que el ítem seleccionado ya no está disponible.
  2. El sistema sugiere eliminar el ítem o reemplazarlo y no le permite avanzar al checkout.
* **3a. Dirección fuera de cobertura**:
  1. El sistema valida la dirección de entrega contra el radio geográfico de despacho del restaurante.
  2. Si está fuera de cobertura, muestra un mensaje de error y aborta el flujo.

**Postcondiciones**:
* Se registra el pedido en la base de datos de órdenes con estado `PENDING_PAYMENT`.

**Escenario de Aceptación (Gherkin)**:
```gherkin
Feature: Registro de pedido por el consumidor
  As a Consumidor
  I want to registrar un pedido en el sistema
  So that pueda recibir mi comida a domicilio

  Scenario: Registro exitoso con items disponibles
    Given el Consumidor tiene un carrito activo con el item "INF-203 Pizza Fugazzeta" del restaurante "Pizzería Don Vico"
    And el item "INF-203 Pizza Fugazzeta" tiene stock disponible de "5" unidades
    When el Consumidor confirma la orden con direccion "Av. Las Heroínas 456" y metodo de pago "tarjeta-visa"
    Then el sistema genera una orden con ID unico en estado "PENDING_PAYMENT"
    And devuelve el subtotal de "120.00" BOB e impuestos de "15.60" BOB
```

... [Repetir la estructura exacta para UC-02, UC-03, UC-04, UC-05]

---

## Métricas

> **Instrucción de llenado**: El modelo debe completar esta sección al final de cada ejecución del prompt, registrando el número de ejecución secuencial, el ID y versión del prompt, y los valores concretos de cada métrica con sus respectivos insights.

**#️⃣ Ejecución**: `[N]`
**🔖 Prompt**: `PR-FSD-FTGO-001` — versión `v1.1-mejorada`

| Nombre de la métrica | Valor | Insights |
|---|---|---|
| **Completitud de Casos de Uso** (`uc_coverage`) | `[X/5 UCs]` | ¿Se documentaron los 5 UCs obligatorios (UC-01 al UC-05)? Indicar cuáles faltan. |
| **Tasa de bloques Gherkin válidos** (`gherkin_syntax_pass_rate`) | `[X%]` | % de UCs con bloque Gherkin completo y parseable (Feature + Scenario + Given/When/Then). Debe ser 100%. |
| **Flujos alternativos por UC** (`alt_flows_rate`) | `[X/5 UCs con ≥ 2 flujos alt.]` | ¿Cada UC documenta al menos 2 flujos alternativos o de excepción? |
| **Trazabilidad UC→US/Capacidad** (`uc_traceability_rate`) | `[X%]` | % de UCs con referencia explícita a una User Story del brief o capacidad del PRD. Debe ser 100%. |
| **Fixtures de datos realistas** (`realistic_fixtures`) | `[Sí/No]` | ¿Los datos de prueba Gherkin usan valores del dominio FTGO (moneda BOB, restaurantes, IDs)? |
| **Ausencia de placeholders vacíos** (`placeholder_free`) | `[Sí/No]` | ¿El FSD contiene TODOs o marcadores `[...]` sin completar? "Sí" indica documento limpio. |
```

### 1.7 Verification (Criterios de Verificación Funcional)

Para garantizar la precisión funcional de la especificación generada, el modelo debe validar que:
- **Estructura Gherkin Certificable**: Cada escenario Gherkin cuente con precondiciones claras (`Dado`), una acción ejecutable (`Cuando`) y resultados observables (`Entonces`). No se deben mezclar múltiples acciones complejas en un solo paso.
- **Aislamiento Funcional**: Cada Caso de Uso (UC) represente una transacción funcional atómica y no contenga lógica de control interna del framework de backend.
- **Consistencia de Actores**: El actor del Caso de Uso coincida exactamente con los actores definidos en los stakeholders del PRD (Consumidor, Restaurante, Courier, Empleado).
- **Mapeo de Flujos de Fallo**: Por cada flujo principal, existan al menos 2 flujos alternativos o de excepción detallando la respuesta de error del sistema (p. ej. denegación de cobro en Stripe o dirección de entrega fuera de cobertura).

---

## 2. Invariantes del prompt

Toda salida funcional debe cumplir estrictamente las siguientes invariantes:
- **Estructura Gherkin Estricta**: Cada Caso de Uso debe tener al menos un bloque Gherkin válido usando palabras clave en español (`Feature`, `Scenario`, `Given`, `When`, `Then` o sus equivalentes `Dado`, `Cuando`, `Entonces`).
- **Completitud Funcional**: El FSD debe detallar los 5 UCs designados en la sección de Contexto.
- **Trazabilidad de NFRs**: Los UCs que tienen restricciones de rendimiento (como el UC-05 de tracking en tiempo real) deben citar explícitamente el ID del NFR del PRD en sus postcondiciones o flujos.
- **Fixtures Realistas**: Todos los datos de prueba incluidos en los ejemplos Gherkin deben usar identidades y nombres realistas del entorno (por ejemplo, códigos de materia universitaria como `CIV-101`, `INF-203`, monedas locales como `BOB`, y ubicaciones reales) para ser compatibles con [docs/prompts/SKILL.md](file:///Users/andresmerida/workspace/githup/ai-test-lab/docs/prompts/SKILL.md).

---

## 3. *Failure modes* declarados

| Código | Descripción | Acción del consumidor |
|--------|-------------|------------------------|
| `E_MISSING_PRD` | Falta el documento fuente `docs/PRD.md` en el espacio de trabajo. | Abortar con error solicitando la generación previa del PRD. |
| `E_INSUFFICIENT_UCS` | El FSD generado contiene menos de 5 Casos de Uso completos. | Rechazar y volver a ejecutar exigiendo la lista completa del UC-01 al UC-05. |
| `E_MISSING_GWT` | Hay algún Caso de Uso que carece del bloque sintáctico de Gherkin. | Reintentar la generación forzando el bloque de código Gherkin para todos los UCs. |
| `E_INVENTED_UC` | El modelo genera Casos de Uso no solicitados ni justificados en el dominio FTGO. | Rechazar y re-alinear la generación a los 5 UCs obligatorios. |

---

## 4. Guardrails

- **MUST**: Validar que cada uno de los 5 UCs contenga los flujos principal y alternativos explícitos y un escenario Gherkin.
- **MUST**: Garantizar la trazabilidad directa de cada Caso de Uso contra el PRD e indicar el aggregate de DDD impactado.
- **MUST NOT**: Inventar respuestas de pasarelas de pago que omitan los códigos de error estándar de Stripe o transacciones financieras genéricas sin precisión decimal.

---

## 5. Trazabilidad

| Origen | ID origen | Este prompt | Consumidor(es) | Artefacto generado | QA / Validación |
|--------|-----------|-------------|----------------|---------------------|-----------------|
| PRD | PR-PRD-FTGO-001 | PR-FSD-FTGO-001 | `test-agent` / `dev-agent` | `docs/FSD.md` | Skill de testing [SKILL.md](file:///Users/andresmerida/workspace/githup/ai-test-lab/docs/prompts/SKILL.md) |

---

## 6. Pruebas del prompt (*prompt tests*)

### 6.1 Caso feliz
- **Input**: El PRD contiene los requisitos de Order Taking, Kitchen, Delivery, Billing y Notifications.
- **Output esperado**: Un FSD impecable de 5 UCs con sus respectivos bloques Gherkin, listos para ser consumidos y compilados por la Skill de JUnit/k6.

### 6.2 Caso borde
- **Input**: El PRD tiene una estructura incompleta en alguna de sus capacidades.
- **Output esperado**: El prompt infiere la funcionalidad del Caso de Uso basándose en el Capítulo de Richardson correspondiente e inserta una nota de aviso `[Inferencia Técnica: PRD Incompleto]`.

### 6.3 Caso adversarial
- **Input**: El usuario solicita generar un Caso de Uso para un sistema de fidelización de puntos de clientes no especificado en el PRD ni en el brief.
- **Comportamiento esperado**: Rechazo del prompt con error `E_INVENTED_DOMAIN` para evitar desvío de alcance.

---

## 7. Instrumentación

- **Herramienta**: OpenTelemetry / Langsmith.
- **Métricas** (ver tabla `## Métricas` en el artefacto generado):
  - `uc_coverage`: 5/5 UCs documentados y completos.
  - `gherkin_syntax_pass_rate`: 100% de UCs con bloque Gherkin válido.
  - `alt_flows_rate`: 5/5 UCs con al menos 2 flujos alternativos.
  - `uc_traceability_rate`: 100% de UCs trazados a US del brief o capacidad del PRD.
  - `realistic_fixtures`: Fixtures con datos realistas del dominio FTGO.
  - `placeholder_free`: Sin TODOs ni marcadores `[...]` vacíos.

---

## 8. Changelog

| Fecha | Autor | Resumen de los cambios comparando con la versión anterior del prompt |
|---|---|---|
| `01/05/2026` | Arquitecto del Lab | Creación de la versión inicial (`v0.1-seed`) con los TODOs de Casos de Uso mínimos y reglas de granularidad vacías. |
| `24/05/2026` | Antigravity AI & Andres Merida | **v1.0-mejorada / v0**: Reestructuración completa a 9 secciones según `PROMPT.md`, resolución de TODOs especificando detalladamente los 5 UCs de FTGO, establecimiento de la regla de granularidad funcional, integración de la precondición con fixtures realistas de QA, adición de la sección `### 1.7 Verification` y normalización de la tabla Changelog de versión. |
| `25/05/2026` | Antigravity AI & Andres Merida | **v1.1-mejorada**: Integración del bloque `## Métricas` en el esqueleto de salida del FSD con 6 métricas de calidad (uc_coverage, gherkin_syntax_pass_rate, alt_flows_rate, uc_traceability_rate, realistic_fixtures, placeholder_free). Actualización de la sección `## 7. Instrumentación` para referenciar el artefacto generado. Bump de versión a `v1.1-mejorada`. |

---

## 9. Revisión humana

| Revisor | Fecha | Veredicto | Notas |
|---------|-------|-----------|-------|
| Andres Merida | 24/05/2026 | Aprobado | Estructura y granularidad excelentes, listo para alimentar la skill automática de QA. |
| Andres Merida | 25/05/2026 | Aprobado | Integración del bloque `## Métricas` validada. Las 6 métricas cubren completitud funcional, calidad Gherkin, trazabilidad y calidad de fixtures. |
