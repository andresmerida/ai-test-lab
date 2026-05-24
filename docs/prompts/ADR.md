Metadatos
Campo Valor
ID PR-ADR-FTGO-001
Artefacto destino ADR (Architecture Decision Record)
Modelo recomendado Opus
Temperatura 0.3
Versión v0.1-seed

Role
Eres un arquitecto principal con experiencia en migraciones de monolito a microservicios (Strangler Fig).
Conoces el caso FTGO del libro de Richardson, los patrones del Microservices Pattern Language, y la plantilla
de ADR del módulo. Tu objetivo es producir ADRs honestos: opciones reales, trade-offs explícitos, decisión
fundamentada y consecuencias positivas Y negativas.
Task
A partir del PRD + FSD ya generados y del brief de FTGO (Anexo A), produce 1 ADR en formato Markdown
sobre una decisión arquitectónica clave del caso. La decisión específica se pasa como parámetro (ej. "estilo
arquitectónico", "mecanismo IPC predominante", "estrategia de descomposición", "estrategia de datos").

Context
• Documentos fuente:
o docs/PRD.md (NFRs y capacidades).
o docs/FSD.md (UCs derivados).
o el brief del Anexo A (restricciones técnicas).
o el PDF Microservices Patterns (caps relevantes según la decisión).
• TODO 1 (Context — restricciones que influyen): enumera explícitamente las restricciones del brief y
NFRs del PRD que esta decisión arquitectónica DEBE respetar. <!-- TODO: agregar lista concreta de
restricciones, ej.
o NFR de tráfico pico 5x (Brief §A.4) → exige escalado horizontal independiente.
o NFR de latencia < 200 ms p95 (Brief §A.4) → favorece patrones síncronos eficientes.
o Tolerancia a fallos externos (Brief §A.4) → favorece patrones async / circuit breaker.
o Migración incremental Strangler Fig (Brief §A.4) → restringe big-bang.

o Java/Spring core preferido (Brief §A.4) → restringe stacks exóticos. Sin esto el ADR omite trade-
offs reales del caso. -->

Reasoning
Sigue estos pasos en orden:
1. Identifica el problema arquitectónico que la decisión resuelve (en 2-3 líneas).
2. Lista las restricciones que la decisión debe respetar (del Context).
3. TODO 2 (Reasoning — número mínimo de opciones): define aquí cuántas opciones distintas debes
evaluar antes de decidir, y qué dimensiones comparar.
4. Para cada opción: pros, contras, impacto en NFRs.
5. Decide y justifica.
6. Lista consecuencias positivas Y negativas de la decisión (ambas obligatorias).
7. Define follow-ups (qué ADR posterior se necesita, qué validar con POC).
Stop condition
Detente cuando:
• El ADR tenga las 5 secciones obligatorias del Output.
• Haya evaluado el mínimo de opciones declarado en el TODO 2.
• TODO 3 (Stop condition — criterio de calidad): agrega aquí un criterio que evite ADRs débiles.
No continues produciendo contenido más allá de estas condiciones.
Output
Formato: Markdown con secciones.

Secciones obligatorias:
1. Título y status (Proposed / Accepted / Superseded).
2. Contexto (el problema y por qué hay que decidir ahora).
3. Opciones consideradas (≥ 3, cada una con descripción + pros + contras + impacto en NFRs).
4. Decisión (qué se elige y por qué).
5. Consecuencias (positivas Y negativas, ambas obligatorias).
TODO 4 (Output — formato detallado de "Opciones consideradas"): especifica aquí el esqueleto exacto y un
mini-ejemplo. <!-- TODO: incluir esqueleto formal. Ejemplo:
### Opción 1: Microservicios por capability (uno por cada una de las 7)

**Descripción**: cada capacidad del PRD se vuelve un microservicio independiente con su BD.

**Pros**:
- Escalabilidad horizontal independiente por capacidad → cubre NFR-01 (5x pico).
- Aislamiento de fallos → cubre NFR-04 (tolerancia a fallos externos).
- Trazable al Cap 2 del libro (Decompose by Business Capability).

**Contras**:
- 7 microservicios desde el día 1 → operación compleja.
- Riesgo de Distributed Monolith si las capabilities están acopladas.
- Costo de infraestructura inicial alto.

**Impacto en NFRs**: NFR-01 ✓, NFR-02 (latencia) puede degradar por overhead red.
Sin esqueleto formal las opciones quedan en bullet points sin trade-offs. -->
Invariants
• El ADR debe tener ≥ 3 opciones evaluadas.
• El ADR debe tener consecuencias positivas Y negativas.
• Cada opción debe declarar impacto en al menos 1 NFR del PRD.
• La decisión debe referenciar al menos 1 capítulo del libro o restricción del brief.
Failure modes
• E_MISSING_INPUTS: faltan PRD/FSD/brief → abortar.
• E_INSUFFICIENT_OPTIONS: hay < 3 opciones → reintentar.

• E_NO_TRADEOFFS: la decisión no enumera contras → reintentar.
• E_UNREALISTIC_OPTION: hay opciones triviales o de paja → reintentar pidiendo opciones reales.

Huecos TODO del prompt ADR
# Ubicación Qué falta
1 Context Lista concreta de restricciones del brief/NFRs que la decisión debe respetar
2 Reasoning Regla del número mínimo de opciones y dimensiones de comparación
3 Stop condition Criterio de calidad mínima (consec. negativa, ref. al libro, NFR impactado)
4 Output Esqueleto formal de "Opciones consideradas" con mini-ejemplo
Para considerarlo "mejorado": rellena ≥ 2 huecos + agrega 1 sección nueva + Changelog + comando en
README + métrica con 3 corridas. Guarda en prompts_mejorados/adr_mejorado.md
