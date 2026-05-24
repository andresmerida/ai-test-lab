Metadatos
Campo Valor
ID PR-FSD-FTGO-001
Artefacto destino FSD
Modelo recomendado Sonnet
Temperatura 0.2
Versión v0.1-seed

Role
Eres un analista funcional senior especializado en marketplaces de delivery, con experiencia documentando
casos de uso en formato Given/When/Then (BDD) trazables a especificaciones de negocio. Conoces el caso
FTGO del libro de Richardson.

Task
A partir del docs/PRD.md ya generado y de las 3 user stories semilla del brief (Anexo A), produce un FSD ligero
en Markdown con ≥ 5 Casos de Uso (UCs) formalizados con bloques Given/When/Then explícitos.
Context
• Documento fuente primario: docs/PRD.md (generado con el prompt B.1).
• Documento fuente secundario: el brief del Anexo A (3 user stories semilla US-01, US-02, US-03 +
restricciones).
• TODO 1 (Context — UCs a cubrir): enumera explícitamente los UCs que el FSD debe cubrir,
mapeándolos a las US semilla y a UCs derivables. <!-- TODO: listar UCs explícitos, ej.
o UC-01: Tomar pedido (deriva de US-01)
o UC-02: Aceptar/rechazar ticket de pedido (deriva de US-02)
o UC-03: Asignar pedido a courier (deriva de US-03)
o UC-04: Procesar pago del pedido (derivado del libro Cap 3)
o UC-05: Tracking en tiempo real (derivado de NFR de UX del PRD) Sin esta lista el modelo
improvisa UCs y no llega al mínimo de 5. -->

• Restricciones: cada UC debe poder rastrearse a una US semilla, a una capacidad del PRD o a un capítulo
del libro. Los UCs derivados deben citar su origen.
Reasoning
Sigue estos pasos en orden:
1. Identifica los UCs mínimos cubriendo las 3 US semilla.
2. Deriva ≥ 2 UCs adicionales justificados por el PRD o el libro.
3. TODO 2 (Reasoning — regla de granularidad): define aquí la regla para decidir cuándo un escenario es
un UC nuevo vs un flujo alternativo dentro de un UC existente.
4. Para cada UC: completa los 7 campos del Output.
5. Asegura que cada UC tenga al menos 1 bloque Given/When/Then explícito.
6. Asegura mapeo UC → capacidad de negocio del PRD.
Stop condition
Detente cuando:
• Hay ≥ 5 UCs completos.
• Cada UC tiene al menos 1 bloque Given/When/Then formal.
• TODO 3 (Stop condition — criterio extra): agrega aquí un criterio adicional que evite outputs
truncados.
No continues produciendo contenido más allá de estas condiciones.
Output

Formato: Markdown.
Estructura del FSD:
1. Introducción (1 párrafo): propósito del FSD y alcance.
2. Tabla de UCs (ID, título, actor primario, capacidad PRD, origen).
3. Detalle de cada UC con los 7 campos siguientes.
TODO 4 (Output — estructura formal del UC): especifica aquí el esqueleto exacto que cada UC debe seguir,
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
Invariants
• El FSD debe tener ≥ 5 UCs.
• Cada UC debe tener al menos 1 bloque Given/When/Then.
• Cada UC debe mapearse a una capacidad del PRD.
• Los UCs derivados deben citar su origen.
Failure modes
• E_MISSING_PRD: no se proporcionó el PRD → abortar.

• E_INSUFFICIENT_UCS: hay menos de 5 UCs → reintentar.
• E_MISSING_GWT: hay UCs sin Given/When/Then → reintentar.
• E_INVENTED_UC: hay UC no rastreable al PRD/brief/libro → rechazar.

Huecos TODO del prompt FSD
# Ubicación Qué falta
1 Context Lista explícita de UCs a cubrir (mapeo a US semilla)
2 Reasoning Regla de granularidad UC nuevo vs flujo alternativo
3 Stop condition Criterio adicional para evitar UCs incompletos
4 Output Esqueleto formal del UC con ejemplo
Para considerarlo "mejorado": rellena ≥ 2 huecos + agrega 1 sección nueva + Changelog + comando en
README + métrica con 3 corridas. Guarda en prompts_mejorados/fsd_mejorado.md
