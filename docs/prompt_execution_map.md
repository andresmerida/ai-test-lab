# Mapa de Ejecución de Prompts y Registro de Artefactos

Este documento sirve como el registro de trazabilidad y mapeo oficial del laboratorio de desarrollo de **FTGO**. Relaciona cada plantilla de prompt ejecutable (`docs/prompts/`) con sus respectivas versiones de metadatos, los artefactos de diseño que han generado (`docs/`), y las variables de telemetría de ejecución (fecha, autor, modelo y temperatura).

---

## 🗺️ Mapa de Trazabilidad de Prompts

| ID de Prompt | Título del Prompt / Plantilla | Versión | Autor(es) | Modelo Utilizado | Temp | Documento(s) Generado(s) | Fecha de Ejecución |
|---|---|---|---|---|---|---|---|
| **`PR-PRD-FTGO-001`** | Generador de PRD Ligero para FTGO | `v1.1-mejorada` | Antigravity AI & Andres Merida | Gemini 3.5 Flash (Medium) | `0.2` | 📄 [PRD_v0.md](file:///Users/andresmerida/workspace/githup/ai-test-lab/docs/prd/PRD_v0.md)<br>📄 [PRD_v1.md](file:///Users/andresmerida/workspace/githup/ai-test-lab/docs/prd/PRD_v1.md) | 24/05/2026 |
| **`PR-ADR-FTGO-001`** | Generador de ADR para FTGO | `v1.1-mejorada` | Andres Merida & Antigravity AI | Gemini 3.5 Flash (Medium) | `0.3` | 📄 [0001-estrategia-descomposicion.md](file:///Users/andresmerida/workspace/githup/ai-test-lab/docs/adr/0001-estrategia-descomposicion.md)<br>📄 [0002-patron-saga.md](file:///Users/andresmerida/workspace/githup/ai-test-lab/docs/adr/0002-patron-saga.md)<br>📄 [0003-bbdd-por-servicio.md](file:///Users/andresmerida/workspace/githup/ai-test-lab/docs/adr/0003-bbdd-por-servicio.md)<br>📄 [0004-mecanismo-ipc.md](file:///Users/andresmerida/workspace/githup/ai-test-lab/docs/adr/0004-mecanismo-ipc.md)<br>📄 [0005-api-gateway.md](file:///Users/andresmerida/workspace/githup/ai-test-lab/docs/adr/0005-api-gateway.md) | 24/05/2026 |
| **`PR-C4-FTGO-001`** | Generador de Diagramas C4 para FTGO | `v1.2-mejorada` | Antigravity AI & Andres Merida | Gemini 3.5 Flash (Medium) | `0.2` | 📄 [C4_v0.md](file:///Users/andresmerida/workspace/githup/ai-test-lab/docs/c4/C4_v0.md) | 24/05/2026 |
| **`PR-FSD-FTGO-001`** | Generador de FSD Ligero para FTGO | `v1.1-mejorada` | Antigravity AI & Andres Merida | Gemini 3.5 Flash (Medium) | `0.2` | 📄 [FSD_v0.md](file:///Users/andresmerida/workspace/githup/ai-test-lab/docs/fsd/FSD_v0.md) | 24/05/2026 |

---

## 🔍 Detalles Específicos por Flujo de Ejecución

### 1. Requisitos de Producto (PRD)
* **Plantilla Origen:** `docs/prompts/PRD.md` (versión `v1.1-mejorada`, estado Aprobado)
* **Fórmula de Consolidación:** La versión `PRD_v1.md` consolida e integra de forma lineal las salidas lógicas e históricas de la Ejecución 0 y la Ejecución 1, duplicando el nivel de profundidad técnica en Bounded Contexts y Sagas, y manteniendo un historial unificado de métricas acumuladas.

### 2. Decisiones Arquitectónicas (ADRs)
* **Plantilla Origen:** `docs/prompts/ADR.md` (versión `v1.1-mejorada`, estado Aprobado)
* **Consecuencias e Invariantes:** Cada uno de los 5 ADRs evalúa de forma estricta 3 alternativas tecnológicas reales basándose en trade-offs, incluye un mínimo de 3 consecuencias positivas y 3 consecuencias negativas, y mapea el impacto directo sobre los NFRs y el patrón *Strangler Fig*.

### 3. Diagramación C4
* **Plantilla Origen:** `docs/prompts/c4.md` (versión `v1.2-mejorada`, estado Aprobado)
* **Rendimiento de Renderizado:** Genera código fuente Mermaid C4 100% libre de errores sintácticos. Modela de manera consistente la separación de niveles (FTGO como caja negra en el Nivel 1, y límites físicos, microservicios lógicos y colas Kafka con protocolos HTTP/2 y gRPC en el Nivel 2).

### 4. Especificación Funcional (FSD)
* **Plantilla Origen:** `docs/prompts/FSD.md` (versión `v1.1-mejorada`, estado Aprobado)
* **BDD y Fixtures de QA:** Implementa 5 Casos de Uso atómicos detallando precondiciones, flujos principal y alternativos, y scripts Gherkin válidos utilizando fixtures estructurados (materia, moneda boliviana `BOB`, y pizzería local) listos para la ingesta del motor de testing.
