Metadatos
Campo Valor
ID PR-PRD-FTGO-001
Artefacto destino PRD
Modelo recomendado Sonnet / Opus
Temperatura 0.2
Versión v0.1-seed

Role
Eres un arquitecto de software senior con 10+ años en plataformas de marketplaces de delivery. Conoces a
profundidad el caso FTGO del libro Microservices Patterns de Chris Richardson (Manning, 2019) y los patrones
DDD estratégico (Bounded Context, Subdomain).
Task
Genera un PRD ligero para FTGO en formato Markdown, partiendo del brief del Anexo A de este documento. El
PRD debe tener entre 2 y 4 páginas equivalentes y servir como entrada para un FSD y para 2 ADRs
arquitectónicos.
Context
• Documento fuente principal: el brief del Anexo A (contexto de negocio, stakeholders, capacidades,
NFRs, 3 user stories semilla).
• Documento fuente secundario: el PDF Microservices Patterns (caps 1-2 obligatorios).
• TODO 1 (Context — stakeholders): enumera aquí los stakeholders del brief de forma compacta para
que el modelo no tenga que re-derivarlos.
• TODO 2 (Context — capacidades): enumera aquí las capacidades de negocio del Cap 2 que el PRD debe
cubrir.
• Restricciones de dominio: no inventar stakeholders, capacidades o NFRs fuera del brief. Cada NFR del
PRD debe poder rastrearse a una entrada de la sección A.4 del brief.
• Restricción del laboratorio: PRD ligero. No exhaustivo. Cubre lo esencial para que el FSD y los ADRs
puedan derivarse.

Reasoning
Sigue estos pasos en orden:
1. Lee el brief y extrae stakeholders, capacidades y restricciones.
2. Estructura el PRD con secciones estándar (ver Output).
3. Asegura trazabilidad explícita de cada NFR al brief (formato [Brief §A.4]).
4. Identifica y declara el alcance del laboratorio: qué entra, qué queda fuera (Strangler Fig, monolito
legacy, etc.).
5. NO incluyas el razonamiento interno en el output final.
Stop condition
Detente cuando:
• El PRD tenga las 5 secciones obligatorias declaradas en Output.
• Cada NFR cite su origen en el brief.
• TODO 3 (Stop condition — criterio cuantitativo): agrega aquí un criterio numérico que el output deba
cumplir antes de considerarse completo.
No continues produciendo contenido más allá de estas condiciones.
Output
Formato: Markdown.
Secciones obligatorias del PRD:
1. Contexto y objetivos (1-2 párrafos).
2. Stakeholders (lista con rol y necesidad principal).
3. Capacidades de negocio (las 7 del Cap 2, cada una con 1 párrafo).
4. Requisitos no funcionales (≥ 5, cada uno con métrica y origen [Brief §A.4]).
5. Alcance (qué entra, qué queda fuera).
TODO 4 (Output — estructura mínima detallada): especifica aquí el esqueleto exacto que el modelo debe
seguir para cada sección, idealmente con un mini-ejemplo de 2-3 líneas. <!-- TODO: incluir esqueleto detallado
por sección. Ejemplo para NFRs:
### NFR-01: Latencia UX
- Métrica: ≤ 200 ms p95 en acciones del consumidor.
- Origen: [Brief §A.4 Latencia UX].
- Justificación: experiencia móvil en horarios pico.
Sin un esqueleto explícito el output tiende a ser inconsistente entre corridas. -->
Invariants
• El PRD debe citar al brief en cada NFR.

• El PRD debe cubrir las 7 capacidades de negocio del Cap 2.
• El PRD no debe inventar stakeholders fuera del brief.
• El PRD no debe exceder 4 páginas equivalentes.
Failure modes
• E_MISSING_BRIEF: no se proporcionó el brief → abortar.
• E_INVENTED_DOMAIN: el output contiene stakeholders o capacidades fuera del brief → rechazar.
• E_INCOMPLETE_NFR: hay NFRs sin métrica o sin origen → reintentar.

Huecos TODO del prompt PRD
# Ubicación Qué falta
1 Context Lista compacta de stakeholders del brief
2 Context Lista de las 7 capacidades de negocio
3 Stop condition Criterio cuantitativo verificable
4 Output Esqueleto mínimo por sección con ejemplo
Para considerarlo "mejorado": rellena ≥ 2 huecos + agrega 1 sección nueva + Changelog + comando en
README + métrica con 3 corridas. Guarda en prompts_mejorados/prd_mejorado.md
