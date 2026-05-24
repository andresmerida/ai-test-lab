# Tabla de Contenido

- Examen Laboratorio Práctico Individual — Caso FTGO
- Anexo A — Brief del caso FTGO
- Anexo B — Prompts semilla
- Prompt PRD
- Prompt FSD
- Prompt ADR
- Prompt C4

# Examen Laboratorio Práctico Individual — Caso FTGO

> Documento convertido desde PDF a Markdown con formato mejorado, segmentación clara y tablas normalizadas.

Examen-Laboratorio Práctico Individual — Caso FTGO
| Campo | Valor |
|---|---|
Modalidad Individual — cada maestrante entrega su propio repositorio (público en GitHub, o privado
dando acceso al usuario eterceros)
Caso de estudio FTGO (Food To Go) del libro Microservices Patterns de Chris Richardson (Manning, 2019)
Liberación 19 de mayo de 2026
Deadline Viernes 22 de mayo de 2026, 18:59 hora local
Esfuerzo
estimado ~4 horas
Peso 10 % de la nota global
Objetivo
Demostrar individualmente la capacidad de:
1. Documentar la arquitectura de un sistema de ejemplo (FTGO) atravesando PRD → FSD → ADRs →
diagramas C4 con trazabilidad explícita.
2. Mejorar prompts existentes del módulo (no partir de cero): rellenar huecos TODO, agregar secciones
nuevas (anti-patterns / verification / examples), declarar métricas antes/después con evidencia.
Productos esperados
Cada maestrante entrega en un repositorio individual con branch release/exam-lab:
# Artefacto Ruta esperada Mínimo aceptable
### 1 PRD ligero de FTGO docs/PRD.md Secciones contexto, stakeholders,
capacidades, NFRs, alcance
### 2 FSD ligero docs/FSD.md ≥ 5 UCs con Given/When/Then explícito
### 3 ADR 1 (estilo arquitectónico) docs/adr/0001-*.md Opciones, decisión, consecuencias
positivas/negativas
### 4 ADR 2 (decisión de IPC, datos
o descomposición) `docs/adr/0002-*.md` Mismo formato que ADR 1
### 5 Diagrama C4 nivel 1 (Context) docs/diagrams/c4_context.mmd Mermaid C4Context válido
### 6 Diagrama C4 nivel 2
(Container) `docs/diagrams/c4_container.mmd` Mermaid C4Container válido
### 7 ≥ 2 prompts mejorados (de prompts_mejorados/<nombre>.md Ver sección "Requisitos para los prompts
los 4 semilla) mejorados"
### 8 README ejecutable README.md raíz Estructura del repo, comandos para
invocar prompts, métricas declaradas
Materiales provistos
Los materiales del examen están incluidos en los Anexos A y B de este documento:
-

## Anexo A — Brief del caso FTGO: contexto, stakeholders, capacidades, NFRs base, 3 user stories semilla.

-

## Anexo B — 4 prompts semilla (uno por cada artefacto: PRD, FSD, ADR, C4), cada uno con huecos TODO

que debes mejorar.
Recursos adicionales compartidos en el Classroom del módulo 4:
- PDF Microservices Patterns (Richardson, Manning 2019).
- Plantillas del módulo (ADR, PRD, FSD, PROMPT).
Requisitos para los prompts mejorados (D4)
Elige al menos 2 de los 4 prompts semilla del

## Anexo B para mejorar. Cada prompt mejorado se guarda en

prompts_mejorados/<nombre>.md y debe cumplir los siguientes 5 requisitos:
1. Al menos 2 huecos TODO críticos rellenados con valor real (los semilla traen 4 huecos cada uno).
2. 1 sección nueva agregada entre: Anti-patterns, Verification, Examples (input/output).
3. Changelog explicando el qué y el por qué de cada cambio (sección ## Changelog).
4. Comando invocable documentado en `README.md` (ej. @prompts_mejorados/prd_mejorado.md
genera PRD para FTGO).
5. Métrica de calidad antes/después con evidencia de 3 corridas (sección ## Métrica que reporte cómo
cambia un indicador concreto: completitud del output, % de secciones cubiertas, reducción de
iteraciones para llegar al resultado, etc.).
Rúbrica (sobre 100 pts, equivale al 10 % de la nota global)
Criterio Peso Excelente Aceptable Insuficiente
Coherencia brief → PRD →
FSD → ADR → C4 25 % trazabilidad explícita en cada doc parcial rota
Calidad del PRD + FSD 20 % ≥ 5 UCs con Given/When/Then
completos + NFRs trazables parcial < 5 UCs o sin GWT
Calidad de los 2 ADRs 20 % opciones + trade-offs + consecuencias
positivas/negativas
sin
trade-offs sin opciones
Calidad de los diagramas C4 10 % nivel 1 + 2 con tecnología/protocolo en
cada relación parcial solo 1 nivel o sin
protocolos
Mejora de los 2 prompts 15 % 5 requisitos D4 cumplidos en ambos parcial mejoras cosméticas
README y self-check 10 % comandos reproducibles + métricas
reportadas parcial ausente
Distribución de puntos:
- Verificación estructural de artefactos (estructura del repo, archivos presentes, secciones mínimas): 60
pts.
- Evaluación cualitativa (trazabilidad, coherencia con FTGO, calidad de prompts, razonamiento de ADRs,
README): 40 pts.
Reglas de entrega y antiplagio
- Entrega: branch release/exam-lab en el repositorio individual del maestrante.
- Tardío no se acepta: la entrega es el branch en la fecha de corte (viernes 22 de mayo, 18:59). Commits
posteriores no se evalúan.
- Individual estricto: cualquier indicio de copia entre maestrantes o reutilización literal del producto
grupal anula el 10 %. Los prompts mejorados deben ser propios; está permitido inspirarse en los del
producto grupal pero el changelog debe documentar las diferencias.
- Trazabilidad obligatoria: cada decisión arquitectónica debe poder rastrearse a un capítulo del libro
Richardson, una restricción del brief o una user story semilla. Inventar dominio fuera de FTGO penaliza.

## Anexo A — Brief del caso FTGO

Toma este brief como única fuente oficial del dominio: cualquier decisión arquitectónica que tomes debe
poder rastrearse a este brief, a un capítulo del libro Richardson o a una de las user stories semilla. Inventar
dominio fuera de aquí penaliza.
| Campo | Valor |
|---|---|
Producto FTGO (Food To Go)
Industria Marketplace de delivery de comida
Fuente canónica Richardson, Microservices Patterns, Manning 2019
Repositorio oficial https://github.com/microservices-patterns/ftgo-application

## A.1 Contexto de negocio

FTGO es una plataforma de delivery que conecta consumidores con restaurantes ofreciendo entrega de
comida a domicilio. El negocio ya está operando desde hace varios años como una aplicación monolítica Java
empaquetada como WAR. El monolito ha acumulado los síntomas clásicos del infierno monolítico (Cap 1 del
libro): builds lentos, escalado conflictivo entre módulos, falta de aislamiento de fallos, lock-in tecnológico y un
equipo cada vez más bloqueado por el tamaño del código.
La dirección de FTGO decidió migrar a microservicios para sostener el crecimiento. El equipo de arquitectura
debe documentar la nueva arquitectura objetivo con suficiente claridad para que los equipos de desarrollo
puedan comenzar la migración incremental (Strangler Fig).
Tu rol como maestrante: actuar como el arquitecto que produce los documentos iniciales de la nueva
arquitectura (PRD + FSD + ADRs + diagramas C4) usando el caso FTGO como base. No estás construyendo el
sistema desde cero, estás documentando la arquitectura objetivo para migrar.

## A.2 Stakeholders

Rol Descripción Interés primario
Consumidor Usuario final (móvil/web) que ordena comida UX rápida, transparencia del estado del
pedido, tracking en tiempo real
Restaurante Negocio asociado que prepara la comida Gestión de tickets, control de carga de
cocina, dashboard de pedidos
Courier Repartidor independiente que entrega los
pedidos
Asignaciones cercanas, rutas
optimizadas, pago confiable
Empleado FTGO
(back office)
Personal interno: customer support, finanzas,
operaciones
Visibilidad, reportes, resolución de
incidentes
Equipo de
arquitectura (tú) Responsable del rediseño hacia microservicios Calidad arquitectónica, trazabilidad,
mantenibilidad
Sistemas externos Pasarela de pago (Stripe), servicio de mapas
(Google Maps), notificaciones (SendGrid, Twilio) Integración estable, SLAs predecibles

## A.3 Capacidades de negocio (Cap 2 del libro)

Las 7 capacidades de negocio estables identificadas por el libro como candidatos a microservicios.
# Capacidad Responsabilidad
### 1 Consumer Management Registro, perfiles, direcciones, preferencias de los consumidores
### 2 Restaurant Management Restaurantes registrados, menús, horarios, disponibilidad
### 3 Order Taking Toma de pedidos: validación, cálculo de total, confirmación
### 4 Order Fulfillment / Kitchen Tickets entregados al restaurante, estado de preparación
### 5 Delivery Asignación de couriers, rutas, tracking en tiempo real
### 6 Billing & Accounting Cobros, comisiones, payouts a restaurantes y couriers
### 7 Notifications Emails, SMS, push: confirmaciones, alertas, recibos
Estas capacidades NO son automáticamente microservicios uno-a-uno. Tu trabajo en los ADRs y diagramas C4
es decidir el grado de descomposición apropiado para FTGO según trade-offs concretos.

## A.4 Restricciones técnicas y NFRs base

Cada NFR de tu PRD debe poder rastrearse a una de estas restricciones (o a una nueva inferida y justificada).
Categoría Restricción / NFR
Carga Tráfico pico de 5x durante horarios de almuerzo y cena (12:00-14:00, 19:00-22:00 hora
local)
Latencia UX Tiempo de respuesta percibido < 200 ms p95 para acciones del consumidor en la app
Disponibilidad 99.9 % mensual mínimo del flujo de toma de pedidos; tracking en tiempo real puede
degradar a 99.5 %
Tolerancia a fallos
externos
El sistema debe poder tomar pedidos aunque la pasarela de pago esté caída (cola de
retry); puede aceptar degradación temporal de mapas
Escalabilidad
horizontal
Cada componente debe poder escalarse independientemente (X-axis y Y-axis del Scale
Cube)
Consistencia de datos Eventual consistency entre servicios aceptada para reporting; fuerte consistencia
requerida dentro del aggregate de un pedido
Trazabilidad Cada acción del consumidor debe rastrearse end-to-end (correlation ID, distributed
tracing)
Migración
incremental El monolito no se reemplaza de golpe; se aplica Strangler Fig durante 18-24 meses
Tecnología Java/Spring Boot preferido en el core para reutilizar conocimiento del equipo; libertad
tecnológica en servicios satélite
Cumplimiento PCI-DSS para datos de pago (delegado a Stripe), GDPR/locales para datos de
consumidores

## A.5 User stories semilla

Estas 3 stories son tu punto de partida obligatorio. En el FSD debes extenderlas a ≥ 5 UCs con bloques
Given/When/Then y derivar al menos 2 UCs adicionales justificables desde el brief o el libro (no inventados).
US-01: Toma de pedido por el consumidor
Como Consumidor, quiero realizar un pedido desde el menú de un restaurante seleccionado, para recibir mi
comida en casa de forma rápida y confiable.
Aceptación esperada en el FSD:
- El consumidor puede ver el menú del restaurante elegido.
- Puede agregar/quitar ítems a su carrito.
- Puede confirmar el pedido con dirección de entrega y método de pago.
- El sistema valida disponibilidad del restaurante y stock.
- El consumidor recibe confirmación con un número de pedido único.
US-02: Aceptación de tickets por el restaurante
Como Restaurante, quiero aceptar o rechazar los tickets de pedido entrantes, para gestionar la carga de mi
cocina sin saturarla.
Aceptación esperada en el FSD:
- El restaurante recibe notificación de nuevos tickets en su dashboard.
- Puede aceptar (con tiempo estimado de preparación) o rechazar con motivo.
- El consumidor recibe actualización del estado del pedido.
- Si rechaza, el pedido se cancela y se notifica al consumidor.
US-03: Asignación de entrega al courier
Como Courier, quiero recibir asignaciones de entrega cercanas y aceptarlas o rechazarlas, para optimizar mi
ruta y mis ingresos.
Aceptación esperada en el FSD:
- El courier marca disponibilidad en la app.
- El sistema le ofrece pedidos listos para retirar cerca de su ubicación.
- El courier acepta o rechaza dentro de un timeout (ej. 30 s).
- Al aceptar, el courier ve la ruta optimizada al restaurante y al consumidor.
UCs adicionales que puedes derivar (ejemplos válidos): pago en el momento del checkout, tracking en tiempo
real del consumidor, dashboard de reportes para el back office, reasignación automática si un courier rechaza,
manejo de cancelaciones por el consumidor, gestión de menús del restaurante.

## A.6 Restricciones del laboratorio

- Fuente única del dominio: este brief + el PDF de Richardson. No inventar stakeholders, capacidades o
NFRs fuera de aquí.
- Trazabilidad obligatoria: cada decisión del PRD/FSD/ADR/C4 debe citar el origen (brief sección X, libro
cap Y , US-NN).
- Granularidad apropiada: este es un PRD/FSD ligero, no exhaustivo. Cubre lo esencial, no agotes.
- Migración incremental como contexto: tus ADRs deben considerar que el monolito sigue vivo y la
migración es gradual (Strangler Fig).

## A.7 Enlaces de referencia

- Repositorio oficial FTGO: https://github.com/microservices-patterns/ftgo-application
- Microservices Pattern Language: https://microservices.io/

## Anexo B — Prompts semilla

Elige al menos 2 de los 4 prompts para mejorar (sección "Requisitos D4" de la consigna). Los 4 están
disponibles para que uses todos si lo deseas. Guarda cada prompt mejorado en
prompts_mejorados/<nombre>.md de tu repo individual.

## B.1 Prompt semilla — PRD ligero de FTGO

Tiene 4 huecos TODO marcados. Para mejorarlo aplica los 5 requisitos D4 de la sección "Requisitos para los
prompts mejorados".
### Metadatos
| Campo | Valor |
|---|---|
ID PR-PRD-FTGO-001
Artefacto destino PRD
Modelo recomendado Sonnet / Opus
Temperatura 0.2
Versión v0.1-seed
### Role
Eres un arquitecto de software senior con 10+ años en plataformas de marketplaces de delivery. Conoces a
profundidad el caso FTGO del libro Microservices Patterns de Chris Richardson (Manning, 2019) y los patrones
DDD estratégico (Bounded Context, Subdomain).
### Task
Genera un PRD ligero para FTGO en formato Markdown, partiendo del brief del

## Anexo A de este documento. El

PRD debe tener entre 2 y 4 páginas equivalentes y servir como entrada para un FSD y para 2 ADRs
arquitectónicos.
### Context
- Documento fuente principal: el brief del

## Anexo A (contexto de negocio, stakeholders, capacidades,

NFRs, 3 user stories semilla).
- Documento fuente secundario: el PDF Microservices Patterns (caps 1-2 obligatorios).
- TODO 1 (Context — stakeholders): enumera aquí los stakeholders del brief de forma compacta para
que el modelo no tenga que re-derivarlos.
- TODO 2 (Context — capacidades): enumera aquí las capacidades de negocio del Cap 2 que el PRD debe
cubrir.
- Restricciones de dominio: no inventar stakeholders, capacidades o NFRs fuera del brief. Cada NFR del
PRD debe poder rastrearse a una entrada de la sección

## A.4 del brief.

- Restricción del laboratorio: PRD ligero. No exhaustivo. Cubre lo esencial para que el FSD y los ADRs
puedan derivarse.
### Reasoning
Sigue estos pasos en orden:
1. Lee el brief y extrae stakeholders, capacidades y restricciones.
2. Estructura el PRD con secciones estándar (ver Output).
3. Asegura trazabilidad explícita de cada NFR al brief (formato [Brief §A.4]).
4. Identifica y declara el alcance del laboratorio: qué entra, qué queda fuera (Strangler Fig, monolito
legacy, etc.).
5. NO incluyas el razonamiento interno en el output final.
### Stop Condition
Detente cuando:
- El PRD tenga las 5 secciones obligatorias declaradas en Output.
- Cada NFR cite su origen en el brief.
- TODO 3 (Stop condition — criterio cuantitativo): agrega aquí un criterio numérico que el output deba
cumplir antes de considerarse completo.
No continues produciendo contenido más allá de estas condiciones.
### Output
Formato: Markdown.
Secciones obligatorias del PRD:
1. Contexto y objetivos (1-2 párrafos).
2. Stakeholders (lista con rol y necesidad principal).
3. Capacidades de negocio (las 7 del Cap 2, cada una con 1 párrafo).
4. Requisitos no funcionales (≥ 5, cada uno con métrica y origen [Brief §A.4]).
5. Alcance (qué entra, qué queda fuera).
> **TODO 4 (Output — estructura mínima detallada): especifica aquí el esqueleto exacto que el modelo debe**
seguir para cada sección, idealmente con un mini-ejemplo de 2-3 líneas. <!-- TODO: incluir esqueleto detallado
por sección. Ejemplo para NFRs:
### NFR-01: Latencia UX
- Métrica: ≤ 200 ms p95 en acciones del consumidor.
- Origen: [Brief §

## A.4 Latencia UX].

- Justificación: experiencia móvil en horarios pico.
Sin un esqueleto explícito el output tiende a ser inconsistente entre corridas. -->
### Invariants
- El PRD debe citar al brief en cada NFR.
- El PRD debe cubrir las 7 capacidades de negocio del Cap 2.
- El PRD no debe inventar stakeholders fuera del brief.
- El PRD no debe exceder 4 páginas equivalentes.
### Failure Modes
- E_MISSING_BRIEF: no se proporcionó el brief → abortar.
- E_INVENTED_DOMAIN: el output contiene stakeholders o capacidades fuera del brief → rechazar.
- E_INCOMPLETE_NFR: hay NFRs sin métrica o sin origen → reintentar.
Huecos TODO del prompt PRD
# Ubicación Qué falta
### 1 Context Lista compacta de stakeholders del brief
### 2 Context Lista de las 7 capacidades de negocio
### 3 Stop condition Criterio cuantitativo verificable
### 4 Output Esqueleto mínimo por sección con ejemplo
Para considerarlo "mejorado": rellena ≥ 2 huecos + agrega 1 sección nueva + Changelog + comando en
README + métrica con 3 corridas. Guarda en prompts_mejorados/prd_mejorado.md.

## B.2 Prompt semilla — FSD ligero de FTGO

Tiene 4 huecos TODO marcados. Para mejorarlo aplica los 5 requisitos D4.
### Metadatos
| Campo | Valor |
|---|---|
ID PR-FSD-FTGO-001
Artefacto destino FSD
Modelo recomendado Sonnet
Temperatura 0.2
Versión v0.1-seed
### Role
Eres un analista funcional senior especializado en marketplaces de delivery, con experiencia documentando
casos de uso en formato Given/When/Then (BDD) trazables a especificaciones de negocio. Conoces el caso
FTGO del libro de Richardson.
### Task
A partir del `docs/PRD.md` ya generado y de las 3 user stories semilla del brief (Anexo A), produce un FSD ligero
en Markdown con ≥ 5 Casos de Uso (UCs) formalizados con bloques Given/When/Then explícitos.
### Context
- Documento fuente primario: docs/PRD.md (generado con el prompt B.1).
- Documento fuente secundario: el brief del

## Anexo A (3 user stories semilla US-01, US-02, US-03 +

restricciones).
- TODO 1 (Context — UCs a cubrir): enumera explícitamente los UCs que el FSD debe cubrir,
mapeándolos a las US semilla y a UCs derivables. <!-- TODO: listar UCs explícitos, ej.
o UC-01: Tomar pedido (deriva de US-01)
o UC-02: Aceptar/rechazar ticket de pedido (deriva de US-02)
o UC-03: Asignar pedido a courier (deriva de US-03)
o UC-04: Procesar pago del pedido (derivado del libro Cap 3)
o UC-05: Tracking en tiempo real (derivado de NFR de UX del PRD) Sin esta lista el modelo
improvisa UCs y no llega al mínimo de 5. -->
- Restricciones: cada UC debe poder rastrearse a una US semilla, a una capacidad del PRD o a un capítulo
del libro. Los UCs derivados deben citar su origen.
### Reasoning
Sigue estos pasos en orden:
1. Identifica los UCs mínimos cubriendo las 3 US semilla.
2. Deriva ≥ 2 UCs adicionales justificados por el PRD o el libro.
3. TODO 2 (Reasoning — regla de granularidad): define aquí la regla para decidir cuándo un escenario es
un UC nuevo vs un flujo alternativo dentro de un UC existente.
4. Para cada UC: completa los 7 campos del Output.
5. Asegura que cada UC tenga al menos 1 bloque Given/When/Then explícito.
6. Asegura mapeo UC → capacidad de negocio del PRD.
### Stop Condition
Detente cuando:
- Hay ≥ 5 UCs completos.
- Cada UC tiene al menos 1 bloque Given/When/Then formal.
- TODO 3 (Stop condition — criterio extra): agrega aquí un criterio adicional que evite outputs
truncados.
No continues produciendo contenido más allá de estas condiciones.
### Output
Formato: Markdown.
Estructura del FSD:
1. Introducción (1 párrafo): propósito del FSD y alcance.
2. Tabla de UCs (ID, título, actor primario, capacidad PRD, origen).
3. Detalle de cada UC con los 7 campos siguientes.
> **TODO 4 (Output — estructura formal del UC): especifica aquí el esqueleto exacto que cada UC debe seguir,**
idealmente con un mini-ejemplo. <!-- TODO: agregar esqueleto formal por UC. Ejemplo:
### UC-01: Tomar pedido
| Campo | Valor |
|---|---|
| Actor primario | Consumidor |
| Capacidad PRD | Order Taking |
| Origen | US-01 (brief) |
**Precondiciones**: ...
**Flujo principal**: ...
**Flujos alternativos**: ...
**Postcondiciones**: ...
**Given/When/Then**:
- Given: el Consumidor tiene un carrito con al menos 1 ítem y método de pago válido.
- When: el Consumidor confirma el pedido.
- Then: el sistema crea el pedido en estado PENDING_PAYMENT y devuelve número único.
Sin esqueleto formal el output varía mucho entre corridas. -->
### Invariants
- El FSD debe tener ≥ 5 UCs.
- Cada UC debe tener al menos 1 bloque Given/When/Then.
- Cada UC debe mapearse a una capacidad del PRD.
- Los UCs derivados deben citar su origen.
### Failure Modes
- E_MISSING_PRD: no se proporcionó el PRD → abortar.
- E_INSUFFICIENT_UCS: hay menos de 5 UCs → reintentar.
- E_MISSING_GWT: hay UCs sin Given/When/Then → reintentar.
- E_INVENTED_UC: hay UC no rastreable al PRD/brief/libro → rechazar.
Huecos TODO del prompt FSD
# Ubicación Qué falta
### 1 Context Lista explícita de UCs a cubrir (mapeo a US semilla)
### 2 Reasoning Regla de granularidad UC nuevo vs flujo alternativo
### 3 Stop condition Criterio adicional para evitar UCs incompletos
### 4 Output Esqueleto formal del UC con ejemplo
Para considerarlo "mejorado": rellena ≥ 2 huecos + agrega 1 sección nueva + Changelog + comando en
README + métrica con 3 corridas. Guarda en prompts_mejorados/fsd_mejorado.md.

## B.3 Prompt semilla — ADR de FTGO

Tiene 4 huecos TODO marcados. Para mejorarlo aplica los 5 requisitos D4.
### Metadatos
| Campo | Valor |
|---|---|
ID PR-ADR-FTGO-001
Artefacto destino ADR (Architecture Decision Record)
Modelo recomendado Opus
Temperatura 0.3
Versión v0.1-seed
### Role
Eres un arquitecto principal con experiencia en migraciones de monolito a microservicios (Strangler Fig).
Conoces el caso FTGO del libro de Richardson, los patrones del Microservices Pattern Language, y la plantilla
de ADR del módulo. Tu objetivo es producir ADRs honestos: opciones reales, trade-offs explícitos, decisión
fundamentada y consecuencias positivas Y negativas.
### Task
A partir del PRD + FSD ya generados y del brief de FTGO (Anexo A), produce 1 ADR en formato Markdown
sobre una decisión arquitectónica clave del caso. La decisión específica se pasa como parámetro (ej. "estilo
arquitectónico", "mecanismo IPC predominante", "estrategia de descomposición", "estrategia de datos").
### Context
- Documentos fuente:
o `docs/PRD.md` (NFRs y capacidades).
o `docs/FSD.md` (UCs derivados).
o el brief del

## Anexo A (restricciones técnicas).

o el PDF Microservices Patterns (caps relevantes según la decisión).
- TODO 1 (Context — restricciones que influyen): enumera explícitamente las restricciones del brief y
NFRs del PRD que esta decisión arquitectónica DEBE respetar. <!-- TODO: agregar lista concreta de
restricciones, ej.
o NFR de tráfico pico 5x (Brief §A.4) → exige escalado horizontal independiente.
o NFR de latencia < 200 ms p95 (Brief §A.4) → favorece patrones síncronos eficientes.
o Tolerancia a fallos externos (Brief §A.4) → favorece patrones async / circuit breaker.
o Migración incremental Strangler Fig (Brief §A.4) → restringe big-bang.
o Java/Spring core preferido (Brief §A.4) → restringe stacks exóticos. Sin esto el ADR omite trade-
offs reales del caso. -->
### Reasoning
Sigue estos pasos en orden:
1. Identifica el problema arquitectónico que la decisión resuelve (en 2-3 líneas).
2. Lista las restricciones que la decisión debe respetar (del Context).
3. TODO 2 (Reasoning — número mínimo de opciones): define aquí cuántas opciones distintas debes
evaluar antes de decidir, y qué dimensiones comparar.
4. Para cada opción: pros, contras, impacto en NFRs.
5. Decide y justifica.
6. Lista consecuencias positivas Y negativas de la decisión (ambas obligatorias).
7. Define follow-ups (qué ADR posterior se necesita, qué validar con POC).
### Stop Condition
Detente cuando:
- El ADR tenga las 5 secciones obligatorias del Output.
- Haya evaluado el mínimo de opciones declarado en el TODO 2.
- TODO 3 (Stop condition — criterio de calidad): agrega aquí un criterio que evite ADRs débiles.
No continues produciendo contenido más allá de estas condiciones.
### Output
Formato: Markdown con secciones.
Secciones obligatorias:
1. Título y status (Proposed / Accepted / Superseded).
2. Contexto (el problema y por qué hay que decidir ahora).
3. Opciones consideradas (≥ 3, cada una con descripción + pros + contras + impacto en NFRs).
4. Decisión (qué se elige y por qué).
5. Consecuencias (positivas Y negativas, ambas obligatorias).
> **TODO 4 (Output — formato detallado de "Opciones consideradas"): especifica aquí el esqueleto exacto y un**
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
### Invariants
- El ADR debe tener ≥ 3 opciones evaluadas.
- El ADR debe tener consecuencias positivas Y negativas.
- Cada opción debe declarar impacto en al menos 1 NFR del PRD.
- La decisión debe referenciar al menos 1 capítulo del libro o restricción del brief.
### Failure Modes
- E_MISSING_INPUTS: faltan PRD/FSD/brief → abortar.
- E_INSUFFICIENT_OPTIONS: hay < 3 opciones → reintentar.
- E_NO_TRADEOFFS: la decisión no enumera contras → reintentar.
- E_UNREALISTIC_OPTION: hay opciones triviales o de paja → reintentar pidiendo opciones reales.
Huecos TODO del prompt ADR
# Ubicación Qué falta
### 1 Context Lista concreta de restricciones del brief/NFRs que la decisión debe respetar
### 2 Reasoning Regla del número mínimo de opciones y dimensiones de comparación
### 3 Stop condition Criterio de calidad mínima (consec. negativa, ref. al libro, NFR impactado)
### 4 Output Esqueleto formal de "Opciones consideradas" con mini-ejemplo
Para considerarlo "mejorado": rellena ≥ 2 huecos + agrega 1 sección nueva + Changelog + comando en
README + métrica con 3 corridas. Guarda en prompts_mejorados/adr_mejorado.md.

## B.4 Prompt semilla — Diagramas C4 (nivel 1 y 2) de FTGO

Tiene 4 huecos TODO marcados. Para mejorarlo aplica los 5 requisitos D4.
### Metadatos
| Campo | Valor |
|---|---|
ID PR-C4-FTGO-001
Artefacto destino 2 archivos .mmd (C4 nivel 1 y nivel 2)
Modelo recomendado Sonnet / Opus
Temperatura 0.2
Versión v0.1-seed
### Role
Eres un arquitecto de software experto en el modelo C4 de Simon Brown y en la sintaxis Mermaid para
C4Context y C4Container. Conoces el caso FTGO del libro de Richardson y has documentado al menos 10
sistemas usando C4.
### Task
Produce 2 diagramas Mermaid del caso FTGO:
1. c4_context.mmd — diagrama de contexto (nivel 1): FTGO como un solo sistema + personas + sistemas
externos.
2. c4_container.mmd — diagrama de contenedores (nivel 2): los principales contenedores
(microservicios, BDs, broker) de FTGO con sus tecnologías y protocolos.
### Context
- Documentos fuente:
o `docs/PRD.md` (stakeholders + capacidades + NFRs).
o `docs/adr/0001-*.md` y `docs/adr/0002-*.md` (decisiones arquitectónicas que condicionan los
containers).
o el brief del

## Anexo A (sistemas externos, stakeholders).

- TODO 1 (Context — sistema y stakeholders del brief): enumera explícitamente el sistema principal y
todas las personas/sistemas externos que deben aparecer en el diagrama de contexto. <!-- TODO:
completar la lista, ej.
o Person: Consumidor, Restaurante, Courier, Empleado FTGO (back office).
o System: FTGO Platform (el sistema bajo diseño).
o System_Ext: Stripe (pasarela de pago), Google Maps (geocoding + rutas), SendGrid/Twilio
(notificaciones email/SMS), Sistema Legacy Monolito (durante la migración Strangler Fig). Sin
esta lista el diagrama omite externos clave del brief. -->
### Reasoning
Sigue estos pasos en orden:
1. Nivel 1 (Context): dibuja FTGO como un único System rodeado de Person y System_Ext. Cada relación
con un protocolo y un propósito (ej. "usa la app móvil para ordenar comida").
2. Nivel 2 (Container): dentro del System_Boundary de FTGO, dibuja los contenedores que se derivan del
PRD y los ADRs (ej. Consumer Service, Order Service, Kitchen Service, Delivery Service, Billing Service,
Notification Service, Mobile App, Web Admin, Order DB, Kafka Broker, etc.).
3. TODO 2 (Reasoning — criterio nivel 1 vs nivel 2): define aquí la regla para decidir qué pertenece al
nivel 1 (Context) y qué al nivel 2 (Container).
4. En el Nivel 2, cada relación entre contenedores debe declarar tecnología + protocolo (ej.
<<JSON/HTTPS>> o <<async Kafka>>).
5. Verifica que el Nivel 2 sea coherente con los ADRs (si elegiste async, el broker aparece; si elegiste DB-
per-service, hay N BDs separadas).
### Stop Condition
Detente cuando:
- Existen los 2 archivos Mermaid completos.
- Nivel 1: ≥ 1 Person, ≥ 2 System_Ext, 1 System (FTGO).
- Nivel 2: ≥ 5 contenedores, todas las relaciones con tecnología/protocolo.
- TODO 3 (Stop condition — sintaxis válida): agrega aquí un criterio para garantizar sintaxis Mermaid
válida.
No continues produciendo contenido más allá de estas condiciones.
### Output
Formato: dos bloques de código Mermaid (uno por diagrama), cada uno destinado a un archivo .mmd
separado.
C4Context
title FTGO – Diagrama de contexto (nivel 1)
Person(consumer, "Consumidor", "Ordena comida")
%% ... resto de actores y sistema ...
C4Container
title FTGO – Diagrama de contenedores (nivel 2)
System_Boundary(ftgo, "FTGO Platform") {
Container(order_service, "Order Service", "Java/Spring Boot", "Order Taking + Order Fulfillment")
ContainerDb(order_db, "Order DB", "PostgreSQL", "Datos de pedidos")
%% ... resto de contenedores ...
}
%% ... relaciones con tecnología y protocolo ...
> **TODO 4 (Output — sintaxis Mermaid C4 detallada): especifica aquí los elementos sintácticos exactos de**
Mermaid C4 que se deben usar y un fragmento de referencia más extenso. <!-- TODO: agregar referencia
sintáctica. Ejemplo:
C4Container
title FTGO – Container Diagram
Person(consumer, "Consumidor", "Usuario móvil")
Person(restaurant, "Restaurante", "Operador de cocina")
System_Ext(stripe, "Stripe", "Pasarela de pago")
System_Boundary(ftgo, "FTGO Platform") {
Container(mobile_app, "Mobile App", "React Native", "App del consumidor")
Container(order_svc, "Order Service", "Java 17/Spring Boot", "Toma de pedidos")
ContainerDb(order_db, "Order DB", "PostgreSQL 15", "Pedidos y items")
ContainerQueue(kafka, "Event Broker", "Apache Kafka", "Bus de eventos")
}
Rel(consumer, mobile_app, "Usa", "iOS/Android")
Rel(mobile_app, order_svc, "Crea pedidos", "JSON/HTTPS, REST")
Rel(order_svc, order_db, "Lee/Escribe", "JDBC")
Rel(order_svc, kafka, "Publica OrderCreated", "Kafka protocol")
Rel(order_svc, stripe, "Cobra", "JSON/HTTPS")
Sin un fragmento de referencia los maestrantes producen sintaxis inválida que no renderiza. -->
### Invariants
- Ambos archivos deben ser sintaxis Mermaid C4 válida (C4Context, C4Container).
- Nivel 1 debe tener ≥ 1 Person y ≥ 2 System_Ext.
- Nivel 2 debe tener ≥ 5 contenedores.
- Cada relación de Nivel 2 debe declarar tecnología y protocolo.
### Failure Modes
- E_MISSING_INPUTS: faltan PRD/ADRs → abortar.
- E_INVALID_MERMAID: la sintaxis no renderiza → reintentar.
- E_LEVEL_MIXED: el Nivel 1 contiene detalles internos de FTGO → reintentar.
- E_NO_TECH_PROTOCOL: hay relaciones sin tecnología/protocolo → reintentar.
Huecos TODO del prompt C4
# Ubicación Qué falta
### 1 Context Lista concreta de personas y sistemas externos del brief
### 2 Reasoning Regla para decidir Nivel 1 vs Nivel 2
### 3 Stop condition Criterio de sintaxis Mermaid válida
### 4 Output Fragmento sintáctico de referencia más completo
Para considerarlo "mejorado": rellena ≥ 2 huecos + agrega 1 sección nueva + Changelog + comando en
README + métrica con 3 corridas. Guarda en prompts_mejorados/c4_mejorado.md.
