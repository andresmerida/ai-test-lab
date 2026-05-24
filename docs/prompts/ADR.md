## 0. Metadatos del prompt

| Campo | Valor |
|-------|-------|
| ID del prompt | `PR-ADR-FTGO-001` |
| Título | Generador de ADR para FTGO |
| Artefacto origen | docs/PRD.md / docs/FSD.md / Brief de FTGO (Anexo A) |
| ID origen | `PRD-REQ-05 / Brief-FTGO-01` |
| Tipo de prompt | generación / transformación / análisis de trade-offs |
| Modelo recomendado | Opus / Gemini 3.5 Pro |
| Temperatura | `0.3` |
| Versión | `v1.0-mejorada` |
| Fecha | `24/05/2026` |
| Autor(es) | Antigravity AI & Andres Merida |
| Estado | Aprobado |

---

## 1. Anatomía del prompt (contenido principal)

### 1.1 Role

Eres un arquitecto principal de software con amplia trayectoria liderando proyectos complejos de migración de sistemas monolíticos a arquitecturas distribuidas y basadas en microservicios, utilizando estrategias incrementales como el patrón Strangler Fig. Tienes maestría en la toma de decisiones basada en trade-offs concretos y en la redacción de registros formales estructurados (ADRs - Architecture Decision Records). Conoces al detalle el caso de estudio **FTGO** de Chris Richardson.

### 1.2 Task

A partir de los documentos `docs/PRD.md` y `docs/FSD.md` ya generados, y apoyándote en el brief de FTGO del Anexo A, produce un documento de Registro de Decisión Arquitectónica (ADR) técnico, honesto y riguroso en formato Markdown sobre una decisión arquitectónica clave del sistema. La decisión a analizar se pasará como parámetro (por ejemplo: "Estructura del estilo arquitectónico", "Mecanismo IPC predominante", "Estrategia de descomposición de microservicios" o "Estrategia de persistencia de datos").

### 1.3 Context

*   **Documentos fuente**:
    *   `docs/PRD.md`: Aporta los Requisitos No Funcionales (NFRs) medibles y la lista de capacidades de negocio.
    *   `docs/FSD.md`: Aporta los Casos de Uso del sistema para los que se debe diseñar la solución.
    *   El brief del Anexo A de `docs/contexto.md`: Proporciona restricciones técnicas ineludibles.
    *   El PDF *Microservices Patterns*: Capítulos de referencia específicos según la decisión tomada (Cap 1 y 2 para descomposición, Cap 3 para IPC, Cap 5 para consistencia/Saga).
*   **Restricciones de Dominio y Técnicas**:
    *   NFR de tráfico pico 5x (Brief §A.4) -> exige escalado horizontal independiente.
    *   NFR de latencia < 200 ms p95 (Brief §A.4) -> favorece patrones síncros eficientes.
    *   Tolerancia a fallos externos (Brief §A.4) -> favorece patrones asíncronos con colas de retry.
    *   Migración incremental Strangler Fig (Brief §A.4) -> restringe un reemplazo big-bang y exige coexistencia con el monolito actual.
    *   Java/Spring core preferido (Brief §A.4) -> limita la selección de lenguajes o runtimes exóticos para el core.

#### Conexión con el Entorno de Automatización y Testing
> [!NOTE]
> Toda decisión que afecte a la comunicación IPC (síncrono REST vs asíncrono por Kafka) o a la disponibilidad de bases de datos afectará directamente los scripts de simulación de rendimiento en k6 automatizados por la Skill [SKILL.md](file:///Users/andresmerida/workspace/githup/ai-test-lab/docs/prompts/SKILL.md). Por ejemplo, si se elige asincronía, los scripts de prueba k6 deberán considerar los umbrales de latencia eventual y colas de retry.

### 1.4 Reasoning (chain‑of‑thought estructurado)

Sigue estos pasos en orden para estructurar y razonar la respuesta:
1.  **Fase de Identificación**: Define claramente el problema arquitectónico particular y explica en 2-3 líneas por qué debe ser resuelto en este momento concreto de la migración Strangler Fig.
2.  **Fase de Recopilación**: Lista las restricciones del brief y los NFRs del PRD que la decisión debe respetar de forma obligatoria.
3.  **Fase de Evaluación Multidimensional**: Evalúa un mínimo de **3 opciones reales** arquitectónicas bajo las siguientes 3 dimensiones de comparación:
    *   Alineación con NFRs (latencia, rendimiento, tolerancia a fallos).
    *   Complejidad operacional e infraestructura necesaria.
    *   Costo de desarrollo y facilidad de migración incremental.
4.  **Fase de Decisión**: Selecciona la opción más adecuada, justificándola detalladamente apoyándote en al menos un capítulo específico del libro de Richardson.
5.  **Fase de Impacto y Consecuencias**: Detalla al menos 3 consecuencias positivas y 3 consecuencias negativas (ambas obligatorias) para la opción elegida.
6.  **Fase de Seguimiento**: Proporciona las tareas de seguimiento técnico inmediatas (ej. qué validar con una POC o qué ADR posterior se debe iniciar).

### 1.5 Stop condition

Detente cuando:
- El ADR cuente con las 5 secciones obligatorias declaradas en el Output completamente redactadas.
- Se hayan evaluado de forma honesta al menos 3 opciones tecnológicas/arquitectónicas reales, con sus respectivos pros y contras e impactos.
- Se detallen explícitamente tanto las consecuencias positivas como las negativas (mínimo 3 de cada una) derivadas de la decisión.
- **Criterio cuantitativo de calidad**: La respuesta Markdown generada tenga entre 1000 y 2000 palabras (asegurando un alto nivel de detalle conceptual sin rodeos) y carezca de placeholders, notas explicativas amigables del asistente o comentarios TODO.
No continues produciendo contenido más allá de estas condiciones.

### 1.6 Output

Formato: Markdown con secciones técnicas.

#### Esqueleto del ADR Requerido:
```markdown
# Architecture Decision Record: [Título Descriptivo de la Decisión]

## 1. Estado y Metadatos
* **ID**: `ADR-FTGO-[Correlativo]` (ej. `ADR-FTGO-0001`)
* **Estado**: Proposed / Accepted / Superseded
* **Fecha**: [Fecha actual]
* **Autor**: [Nombre del maestrante]

## 2. Contexto del Problema
[Inserta aquí un párrafo que describa la situación actual de FTGO, el problema arquitectónico a resolver, y por qué es crítico decidir esto durante la migración de 18-24 meses]

## 3. Opciones Consideradas

### Opción 1: [Nombre de la Opción 1]
* **Descripción**: [Breve descripción técnica de la opción]
* **Pros**:
  * [Pro 1] -> [Impacto en NFR o capacidad del PRD]
  * [Pro 2]
* **Contras**:
  * [Contra 1]
  * [Contra 2]
* **Impacto en NFRs**: [Mapeo de impacto exacto, ej. NFR-01 (latencia) se degrada por overhead, NFR-04 (tolerancia a fallos) mejora]

### Opción 2: [Nombre de la Opción 2]
...
### Opción 3: [Nombre de la Opción 3]
...

## 4. Decisión y Justificación
[Declara cuál es la opción ganadora seleccionada y argumenta la decisión de forma rigurosa. Debes justificar la elección basándote en los trade-offs de rendimiento, la facilidad de migración Strangler Fig, y citar explícitamente al menos un Capítulo o sección del libro de Chris Richardson]

## 5. Consecuencias

### 5.1 Consecuencias Positivas (Beneficios)
1. [Beneficio 1, ej. Reducción de la latencia percibida en el flujo principal]
2. [Beneficio 2]
3. [Beneficio 3]

### 5.2 Consecuencias Negativas (Riesgos y Costes)
1. [Riesgo/Coste 1, ej. Mayor complejidad operativa debido al despliegue de colas de mensajes Kafka]
2. [Riesgo/Coste 2]
3. [Riesgo/Coste 3]

## 6. Siguientes Pasos (Follow-ups)
* [Paso 1: Desarrollar una POC local para validar tiempos de red]
* [Paso 2: Generar ADR complementario de base de datos distribuidas]
```

### 1.7 Examples: Inputs/Outputs (Ejemplo de Análisis Comparativo)

Al evaluar las opciones arquitectónicas, el modelo debe estructurar la comparación usando el siguiente formato de ejemplo:

#### Entrada (Problema a Decidir):
> "Elegir el estilo de comunicación predominante entre el servicio de pedidos (Order Service) y el servicio de cocina (Kitchen Service)."

#### Salida Comparativa Esperada:
| Dimensión | Opción A: Síncrona (REST/HTTP) | Opción B: Asíncrona (Kafka Events) | Opción C: Híbrida (gRPC) |
|---|---|---|---|
| **Alineación con NFR-01 (Latencia)** | Excelente (p95 < 50ms) | Variable (depende de eventual consistency) | Excelente (p95 < 20ms) |
| **Tolerancia a fallos externos** | Baja (requiere circuit breakers en cascada) | Alta (gracias al búfer y colas de retry asíncronas) | Baja (acoplamiento temporal de red) |
| **Complejidad de Migración** | Muy Baja (conocimiento previo del equipo) | Alta (exige desplegar y operar clúster Kafka) | Media (exige definir esquemas protobuf) |
| **Decisión Final** | Rechazada por acoplamiento temporal. | **Aceptada** para desacoplar flujos y soportar caídas de pasarelas. | Rechazada por complejidad en frontends móviles. |

---

## 2. Invariantes del prompt

Toda salida válida debe cumplir rigurosamente con estas condiciones:
- **Mínimo de Opciones**: Se deben evaluar rigurosamente al menos 3 opciones de diseño reales. No se permiten opciones "de paja" o triviales.
- **Consecuencias Completas**: Es obligatorio detallar al menos 3 consecuencias positivas y 3 consecuencias negativas derivadas directamente de la elección de la alternativa.
- **Trazabilidad del Dominio**: La justificación y el contexto del ADR deben hacer referencia a las restricciones y NFRs específicos definidos en `docs/PRD.md` o en el brief de negocio.
- **Referencias del Libro**: Debe citar por lo menos una sección teórica del libro *Microservices Patterns* (Manning, 2019) para fundamentar la decisión.

---

## 3. *Failure modes* declarados

| Código | Descripción | Acción del consumidor |
|--------|-------------|------------------------|
| `E_MISSING_INPUTS` | Faltan documentos previos de entrada (`docs/PRD.md` o `docs/FSD.md`) en el espacio de trabajo. | Abortar con error, solicitando la ejecución previa de las etapas del PRD y FSD. |
| `E_INSUFFICIENT_OPTIONS` | El ADR evalúa menos de 3 opciones tecnológicas alternativas. | Reintentar la generación exigiendo un análisis profundo de 3 opciones reales. |
| `E_NO_TRADEOFFS` | La sección de consecuencias del ADR omite desventajas o riesgos (consecuencias negativas). | Rechazar y volver a generar obligando a listar riesgos honestos y costes operacionales. |
| `E_UNREALISTIC_OPTION` | Se evalúan tecnologías absurdas o no alineadas al stack Java/Spring Boot o a las restricciones del brief. | Reintentar exigiendo alternativas de diseño viables y en el contexto de FTGO. |

---

## 4. Guardrails

- **MUST**: Asegurar que cada opción considerada detail su impacto explícito en los NFRs del PRD.
- **MUST**: Validar que la decisión seleccionada esté fundamentada en base a la viabilidad del patrón Strangler Fig y el monolito legacy.
- **MUST NOT**: Proponer tecnologías que violen las políticas de seguridad (p. ej. almacenamiento local de tarjetas de crédito sin cumplimiento PCI-DSS).

---

## 5. Trazabilidad

| Origen | ID origen | Este prompt | Consumidor(es) | Artefacto generado | QA / Validación |
|--------|-----------|-------------|----------------|---------------------|-----------------|
| PRD / FSD | PR-PRD-FTGO-001 / PR-FSD-FTGO-001 | PR-ADR-FTGO-001 | `dev-agent` | `docs/adr/NNNN-*.md` | Pruebas de rendimiento k6 asociadas en [SKILL.md](file:///Users/andresmerida/workspace/githup/ai-test-lab/docs/prompts/SKILL.md) |

---

## 6. Pruebas del prompt (*prompt tests*)

### 6.1 Caso feliz
- **Input**: Parámetro de decisión: "Mecanismo IPC para Order Taking y Kitchen". Se proveen PRD y FSD válidos.
- **Output esperado**: Un ADR impecable que evalúa REST/HTTP, gRPC, y mensajería Kafka, eligiendo gRPC/Kafka y documentando sus 3 pros/contras y su trazabilidad con k6 en `SKILL.md`.

### 6.2 Caso borde
- **Input**: Se le solicita decidir sobre una base de datos global unificada sin descomposición de base de datos.
- **Output esperado**: El prompt acepta la solicitud pero genera fuertes consecuencias negativas de acoplamiento basándose en el Cap 2 del libro y advierte la violación del principio DB-per-service de microservicios.

### 6.3 Caso adversarial
- **Input**: El usuario exige seleccionar una opción que ignora los plazos del Strangler Fig y propone una reescritura big-bang.
- **Comportamiento esperado**: Rechazo del prompt con error `E_UNREALISTIC_OPTION` exigiendo el cumplimiento del plazo de migración incremental.

---

## 7. Instrumentación

- **Herramienta**: OpenTelemetry / Langfuse.
- **Métricas**:
  - `tradeoff_honesty_score`: Ratio de consecuencias negativas detalladas contra positivas ($\ge 1.0$, indicando un análisis equilibrado).
  - `book_citation_count`: Número de citaciones válidas y precisas al libro de Chris Richardson en el ADR ($\ge 1$).

---

## 8. Changelog

| Fecha | Autor | Resumen de los cambios comparando con la versión anterior del prompt |
|---|---|---|
| `01/05/2026` | Arquitecto del Lab | Creación de la versión inicial (`v0.1-seed`) con los TODOs de evaluación de opciones y criterios de calidad vacíos. |
| `24/05/2026` | Antigravity AI & Andres Merida | **v1.0-mejorada / v0**: Reestructuración completa a 9 secciones basada en `PROMPT.md`, resolución de TODOs incluyendo las restricciones técnicas de la migración Strangler Fig, establecimiento de la métrica de evaluación de $\ge 3$ opciones, integración con k6 en `docs/prompts/SKILL.md`, adición de la sección `### 1.7 Examples: Inputs/Outputs` y normalización de la tabla Changelog de versión. |

---

## 9. Revisión humana

| Revisor | Fecha | Veredicto | Notas |
|---------|-------|-----------|-------|
| Andres Merida | 24/05/2026 | Aprobado | Análisis de trade-offs riguroso, ideal para justificar decisiones ante los comités de arquitectura del laboratorio. |
