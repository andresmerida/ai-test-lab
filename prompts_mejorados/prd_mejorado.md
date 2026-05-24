## 0. Metadatos del prompt

| Campo | Valor |
|-------|-------|
| ID del prompt | `PR-PRD-FTGO-001` |
| Título | Generador de PRD Ligero para FTGO |
| Artefacto origen | Brief de FTGO (Anexo A) / Microservices Patterns Cap 1-2 |
| ID origen | `Brief-FTGO-01` |
| Tipo de prompt | generación / estructuración |
| Modelo recomendado | Sonnet / Opus / Gemini 3.5 Pro |
| Temperatura | `0.2` |
| Versión | `v1.0-mejorada` |
| Fecha | `24/05/2026` |
| Autor(es) | Antigravity AI & Andres Merida |
| Estado | Aprobado |

---

## 1. Anatomía del prompt (contenido principal)

### 1.1 Role

Eres un arquitecto de software senior con más de 10 años de experiencia en el diseño y evolución de plataformas de marketplaces de delivery a gran escala. Conoces en profundidad el caso de estudio **FTGO (Food To Go)** desarrollado por Chris Richardson en su obra clásica *Microservices Patterns* (Manning, 2019), así como los patrones de diseño estratégico y táctico del Domain-Driven Design (DDD), incluyendo la delimitación de Bounded Contexts y la clasificación de Subdominios.

### 1.2 Task

Produce un documento de Requisitos de Producto (PRD) ligero, estructurado y de alta calidad para la plataforma FTGO en formato Markdown, a partir del brief de negocio y los requerimientos del Anexo A de `docs/contexto.md`. El PRD resultante debe tener una extensión equivalente a entre 2 y 4 páginas de contenido técnico preciso y servir como la especificación de negocio de entrada definitiva para derivar posteriormente el FSD (Functional Specification Document) y al menos 2 ADRs (Architecture Decision Records) de diseño de microservicios.

### 1.3 Context

* **Documento fuente principal**: El brief de negocio y restricciones técnicas descritos en `docs/contexto.md` (que cubre contexto de negocio, stakeholders, capacidades, NFRs base y user stories semilla).
* **Documento fuente secundario**: Los Capítulos 1 y 2 del libro *Microservices Patterns* de Chris Richardson.
* **Restricciones de dominio**: No debes inventar stakeholders, capacidades o requisitos no funcionales fuera de lo indicado en el brief. Cada NFR en el PRD final debe citar de forma explícita su origen exacto en el brief (por ejemplo: `[Brief §A.4 Latencia UX]`).
* **Restricciones técnicas**: La arquitectura objetivo debe ser compatible con una estrategia de migración incremental (*Strangler Fig Pattern*) desde el monolito Java actual en un plazo de 18-24 meses.

#### Stakeholders del Brief (Lista Compacta)
Para evitar que deduzcas o re-derives stakeholders, guíate únicamente por esta lista canónica:
1. **Consumidor** (Usuario final móvil/web): Desea realizar pedidos de comida a domicilio. Su interés principal es una UX extremadamente rápida (latencia < 200 ms), transparencia del estado de su pedido y tracking en tiempo real.
2. **Restaurante** (Negocio asociado): Desea procesar las órdenes entrantes. Su interés principal es la gestión eficiente de tickets de cocina, control de carga para evitar saturación y un dashboard operativo de pedidos.
3. **Courier** (Repartidor independiente): Desea entregar pedidos. Su interés principal es recibir asignaciones de entregas cercanas, rutas de entrega optimizadas y un sistema de pagos confiable.
4. **Empleado FTGO** (Personal de back office/soporte/finanzas): Requiere visibilidad completa, reportes y herramientas eficaces para resolver incidencias de clientes o disputas de pago.
5. **Equipo de Arquitectura** (Tú): Responsable del rediseño incremental de la aplicación monolítica Java (WAR) hacia microservicios. Su interés primario es garantizar la alta cohesión, el bajo acoplamiento, la escalabilidad horizontal y la trazabilidad de los NFRs.
6. **Sistemas Externos**: Pasarela de pagos (Stripe), Servicios de Mapas y Rutas (Google Maps API), Servicios de Notificaciones (SendGrid/Twilio). Requieren integraciones estables con SLAs y contratos de servicios predecibles.

#### Capacidades de Negocio del Libro (Cap 2)
El PRD debe cubrir exactamente las siguientes 7 capacidades identificadas como candidatas estables a subdominios:
1. **Consumer Management**: Registro de usuarios, perfiles de consumidores, gestión de direcciones de entrega y preferencias alimenticias.
2. **Restaurant Management**: Registro de restaurantes asociados, gestión de menús dinámicos, horarios de atención y disponibilidad de cocinas.
3. **Order Taking**: Proceso de toma y validación de pedidos, cálculo preciso de precios, impuestos, tarifas de envío y confirmación inicial del pedido.
4. **Order Fulfillment / Kitchen**: Gestión del flujo de preparación de comidas en el restaurante, creación de tickets de cocina y transiciones de estados de preparación.
5. **Delivery**: Planificación y asignación de couriers a pedidos listos, cálculo de rutas de entrega óptimas y seguimiento (tracking) GPS en tiempo real de couriers.
6. **Billing & Accounting**: Procesamiento de cobros a consumidores mediante QR/tarjeta, deducción de comisiones operativas de FTGO y liquidación de pagos (payouts) a restaurantes y couriers.
7. **Notifications**: Orquestación y envío automatizado de correos electrónicos, mensajes SMS y alertas push móviles para mantener informados a todos los stakeholders clave sobre el estado del pedido.

#### Conexión con el Entorno de Automatización y Testing
> [!NOTE]
> El PRD generado por este prompt representa el nivel más alto del ciclo de desarrollo de software guiado por IA en el laboratorio. Las definiciones de las User Stories y Requisitos No Funcionales del PRD serán la entrada directa del FSD, cuyos criterios de aceptación Gherkin (`Given/When/Then`) serán procesados automáticamente por la Skill de desarrollo ágil [SKILL.md](file:///Users/andresmerida/workspace/githup/ai-test-lab/docs/prompts/SKILL.md) (`fsd-gherkin-a-tests-aceptacion`) para generar tests ejecutables en JUnit y simulaciones de rendimiento en k6. Por tanto, cada capacidad y requisito definido aquí debe ser atómico y medible para no romper la cadena de automatización.

### 1.4 Reasoning (chain‑of‑thought estructurado)

Sigue estrictamente estos pasos en orden para estructurar y razonar la respuesta:
1. **Fase de Análisis**: Lee detalladamente el archivo de contexto `docs/contexto.md`. Extrae de forma explícita y almacena en el contexto de la conversación las restricciones de latencia, tráfico en horas pico, y disponibilidad de toma de pedidos.
2. **Fase de Organización**: Estructura el PRD en Markdown respetando las 5 secciones obligatorias listadas en la sección 1.6 (Output).
3. **Fase de Redacción del Contexto**: Define el contexto de negocio de FTGO, justificando la migración del monolito al patrón Strangler Fig y vinculándolo con el plazo de 18-24 meses.
4. **Fase de Redacción de Stakeholders y Capacidades**: Rellena las secciones correspondientes utilizando únicamente la lista compacta y las 7 capacidades descritas en la sección de Contexto de este prompt. Asegúrate de dedicar exactamente 1 párrafo a explicar cómo cada capacidad de negocio apoya la visión de microservicios.
5. **Fase de Trazabilidad de NFRs**: Redacta al menos 5 Requisitos No Funcionales (NFRs). Cada NFR debe incluir una métrica exacta de aceptación y la etiqueta de origen formateada como `[Brief §A.4 NombreCategoria]`. Justifica su impacto en la experiencia de usuario o la operación.
6. **Fase de Definición de Alcance**: Declara explícitamente el alcance de esta fase de diseño del laboratorio (qué procesos entran y qué queda delegado al monolito legacy).
7. **Fase de Revisión Interna de Invariantes**: Verifica que no hayas incluido razonamiento interno en la salida final y que todas las invariantes se cumplan rigurosamente.

### 1.5 Stop condition

Detente cuando:
- El PRD cuente con las 5 secciones obligatorias indicadas en el Output completamente estructuradas.
- Hayas incluido y detallado exactamente las 7 capacidades de negocio de FTGO sin omitir ninguna.
- Se hayan especificado un mínimo de 5 Requisitos No Funcionales (NFRs) válidos, cada uno con una métrica de rendimiento explícita y su respectiva citación al origen en el brief.
- **Criterio cuantitativo de completitud**: La salida total en Markdown contenga entre 1500 y 3000 palabras (asegurando un nivel de detalle técnico profundo pero conciso) y carezca de placeholders vacíos, notas explicativas personales del asistente o comentarios del tipo "TODO".
No continues produciendo contenido más allá de estas condiciones.

### 1.6 Output

Formato: Markdown estructurado e impecable.

#### Esqueleto de PRD Requerido:
```markdown
# Product Requirement Document (PRD) - FTGO Platform Migration

## 1. Contexto y Objetivos
[Inserta aquí un párrafo describiendo la situación del monolito actual WAR, la urgencia de migrar debido al crecimiento, y el objetivo de la migración utilizando el patrón Strangler Fig durante 18-24 meses]

[Inserta un segundo párrafo detallando el propósito del presente PRD ligero como base para el FSD y los ADRs posteriores, vinculándolo con la automatización del ciclo mediante la especificación de tests en la skill de testing `docs/prompts/SKILL.md`]

## 2. Stakeholders
[Para cada uno de los 6 stakeholders listados en el Contexto del prompt, genera un elemento en la siguiente lista:]
* **[Nombre del Stakeholder]** ([Rol]):
  * *Necesidad Principal*: [Breve descripción de su necesidad de negocio]
  * *Interés en el Sistema*: [Qué espera de la plataforma tecnológica y su migración]

## 3. Capacidades de Negocio
[Presenta las 7 capacidades de negocio identificadas del Capítulo 2 en Microservices Patterns. Cada capacidad debe tener su propia subcabecera e incluir exactamente un párrafo de 4-6 líneas describiendo su responsabilidad, su aggregate principal de DDD y cómo interactúa conceptualmente en la arquitectura desacoplada]

### 3.1 Consumer Management
...
### 3.2 Restaurant Management
...
### 3.3 Order Taking
...
### 3.4 Order Fulfillment / Kitchen
...
### 3.5 Delivery
...
### 3.6 Billing & Accounting
...
### 3.7 Notifications
...

## 4. Requisitos No Funcionales (NFRs)
[Genera una lista detallada con al menos 5 NFRs utilizando el siguiente formato de ejemplo:]

### NFR-01: Latencia UX
* **Métrica**: Tiempo de respuesta percibido < 200 ms p95 para acciones críticas del consumidor en la app móvil.
* **Origen**: [Brief §A.4 Latencia UX]
* **Justificación e Impacto**: Vital para retener la conversión de compra de los consumidores móviles en entornos urbanos competitivos. Afecta directamente los tests automatizados y simulaciones de carga k6 descritos en `docs/prompts/SKILL.md`.

### NFR-02: Escalabilidad en Tráfico Pico
* **Métrica**: Soportar un tráfico pico de hasta 5 veces el volumen promedio en ventanas críticas de almuerzo (12:00-14:00) y cena (19:00-22:00) sin degradación de servicio.
* **Origen**: [Brief §A.4 Carga]
* **Justificación e Impacto**: Requiere escalabilidad horizontal independiente (Ejes X/Y del Scale Cube) en servicios de Order Taking y Kitchen.

... [Añade NFR-03, NFR-04, NFR-05]

## 5. Alcance de la Migración
### 5.1 En Alcance (In Scope)
* [Elemento 1: p. ej. Modelado estratégico y táctico del aggregate del pedido en Order Taking]
* [Elemento 2: p. ej. Transición de estados de tickets en Order Fulfillment]
* [Elemento 3: p. ej. Procesamiento asíncrono de cobros e integración con pasarela Stripe]

### 5.2 Fuera de Alcance (Out of Scope)
* [Elemento 1: p. ej. Reescritura completa del módulo legacy de contabilidad interna (permanece en monolito)]
* [Elemento 2: p. ej. Sistema autónomo de despacho por drones o vehículos auto-conducidos]
```

---

## 2. Invariantes del prompt

Toda salida producida por este prompt debe cumplir rigurosamente las siguientes invariantes verificables:
- **Trazabilidad Absoluta**: Cada uno de los Requisitos No Funcionales (NFRs) declarados debe citar textualmente su origen en el brief con el formato `[Brief §A.4 Categoria]`.
- **Cobertura de Capacidades**: El PRD debe detallar de forma independiente cada una de las 7 capacidades de negocio estables del Capítulo 2 de *Microservices Patterns*.
- **No Invención de Datos**: No se deben inventar stakeholders ni requisitos tecnológicos adicionales a los documentados en `docs/contexto.md` (no incluir Blockchain, IA conversacional, etc.).
- **Cumplimiento de Extensión**: La salida total no debe exceder las 3000 palabras ni quedar a medias o truncada en sus secciones finales.
- **Enlace a la Skill de Calidad**: El documento generado debe contener al menos una referencia explícita a la existencia y propósito de la Skill canónica de pruebas [SKILL.md](file:///Users/andresmerida/workspace/githup/ai-test-lab/docs/prompts/SKILL.md) en su sección de NFRs o de objetivos, cerrando el bucle de trazabilidad de ingeniería de software.

---

## 3. *Failure modes* declarados

Si ocurre alguna de las siguientes anomalías durante el procesamiento del prompt, el motor del agente debe reaccionar según lo indicado:

| Código de Error | Descripción del Error | Acción Requerida / Resolución |
|-----------------|-----------------------|--------------------------------|
| `E_MISSING_CONTEXT` | No se encuentra el archivo de contexto `docs/contexto.md` en el espacio de trabajo. | Abortar la ejecución inmediatamente e informar al usuario de la ausencia del brief de FTGO. |
| `E_INVENTED_DOMAIN` | El PRD generado incluye tecnologías no solicitadas (ej. Web3) o stakeholders ajenos al dominio de FTGO. | Rechazar el output, limpiar contexto y re-ejecutar el prompt limitándose estrictamente al brief. |
| `E_INCOMPLETE_NFR` | Se listan NFRs sin su correspondiente origen del brief, sin justificación de impacto, o sin métricas numéricas verificables. | Reintentar la generación de la sección de Requisitos No Funcionales asegurando la estructura de tres campos por NFR. |
| `E_MISSING_CAPABILITIES` | El PRD final omite una o más de las 7 capacidades de negocio enumeradas. | Abortar e iniciar de nuevo el listado, asegurando la inclusión exacta de las 7 subcabeceras. |

---

## 4. Guardrails

- **MUST**: Validar que la salida final contenga exactamente los 5 encabezados principales de nivel 2 (`## 1. Contexto y Objetivos`, `## 2. Stakeholders`, etc.).
- **MUST**: Registrar en los metadatos de telemetría de tu ejecución los parámetros críticos del prompt (`promptId: PR-PRD-FTGO-001`, `modelo`, `versión: v1.0-mejorada`, `tokens_consumidos`).
- **MUST NOT**: Bajo ninguna circunstancia incluyas API keys ficticias de Stripe, SendGrid o Google Maps, ni credenciales reales de bases de datos en los ejemplos o datos de prueba.

---

## 5. Trazabilidad

| Origen (Entrada) | ID Origen | Este Prompt (Procesador) | Consumidor Siguiente | Artefacto Generado | Validación Posterior |
|------------------|-----------|-------------------------|----------------------|-------------------|----------------------|
| Brief de Negocio FTGO | `Brief-FTGO-01` | `PR-PRD-FTGO-001` | Diseñador de FSD (`PR-FSD-FTGO-001`) | `docs/PRD.md` | Skill canónica de pruebas [SKILL.md](file:///Users/andresmerida/workspace/githup/ai-test-lab/docs/prompts/SKILL.md) |

---

## 6. Pruebas del prompt (*prompt tests*)

### 6.1 Caso feliz
- **Input**: El archivo `docs/contexto.md` contiene el brief estándar completo y las 3 US semilla.
- **Output esperado**: Un PRD en Markdown limpio, con las 7 capacidades mapeadas a subdominios, 5 NFRs detallados con métricas exactas citando `[Brief §A.4 ...]` y referencias de acoplamiento a `docs/prompts/SKILL.md`.

### 6.2 Caso borde
- **Input**: El brief en `docs/contexto.md` tiene una sección de NFRs vacía o con datos numéricos alterados.
- **Output esperado**: El prompt debe generar los NFRs esenciales infiriendo métricas realistas y lógicas del dominio de delivery e indicando en el PRD la anotación `[Inferencia de Arquitectura: Contexto Incompleto]`, levantando una advertencia sin abortar el proceso completo.

### 6.3 Caso adversarial
- **Input**: Un usuario instruye al prompt que omita los microservicios y recomiende construir un monolito moderno basado en PHP o Ruby on Rails.
- **Comportamiento esperado**: Rechazo del prompt levantando el error `E_INVENTED_DOMAIN`. El prompt debe forzar al modelo a alinearse al caso FTGO (migración incremental a microservicios sobre Java/Spring Boot) conforme al Cap 1 y 2 del libro de Richardson.

---

## 7. Instrumentación

- **Herramienta de Observabilidad**: OpenTelemetry / Langfuse.
- **Métricas Clave de Calidad del Prompt**:
  - `trazabilidad_nfr_rate`: Porcentaje de NFRs que contienen citación de origen válida en el brief ($\ge 100\%$).
  - `capabilities_coverage`: Número de capacidades cubiertas sobre el total estipulado (7/7 obligatorias).
  - `completeness_score`: Evaluación automática de presencia de las 5 secciones requeridas sin placeholders.

---

## 8. Versionado

| Versión | Fecha | Autor | Cambio | Modelo Validado |
|---------|-------|-------|--------|-----------------|
| `v0.1-seed` | 01/05/2026 | Arquitecto del Lab | Estructura básica del seed. | Claude Sonnet |
| `v1.0-mejorada` | 24/05/2026 | Antigravity AI / Andres Merida | **Mejora Completa**: Reestructuración total a 9 secciones según `PROMPT.md`, resolución de los 4 huecos TODO (stakeholders e intereses, las 7 capacidades, parada cuantitativa profunda y esqueleto detallado), integración de trazabilidad de pruebas hacia `docs/prompts/SKILL.md` e instrumentación de calidad. | Gemini 1.5/3.5 Pro & Flash |

---

## 9. Revisión humana

| Revisor | Fecha | Veredicto | Notas |
|---------|-------|-----------|-------|
| Andres Merida | 24/05/2026 | Aprobado | El prompt cumple plenamente con los objetivos de normalización, detalle de dominio FTGO y trazabilidad extrema hacia el ciclo de automatización de QA. |
