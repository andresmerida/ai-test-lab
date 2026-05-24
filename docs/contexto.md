Campo Valor
Producto FTGO (Food To Go)
Industria Marketplace de delivery de comida
Fuente canónica Richardson, Microservices Patterns, Manning 2019
Repositorio oficial https://github.com/microservices-patterns/ftgo-application
A.1 Contexto de negocio
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
A.2 Stakeholders
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

A.3 Capacidades de negocio (Cap 2 del libro)
Las 7 capacidades de negocio estables identificadas por el libro como candidatos a microservicios.
# Capacidad Responsabilidad
1 Consumer Management Registro, perfiles, direcciones, preferencias de los consumidores
2 Restaurant Management Restaurantes registrados, menús, horarios, disponibilidad
3 Order Taking Toma de pedidos: validación, cálculo de total, confirmación
4 Order Fulfillment / Kitchen Tickets entregados al restaurante, estado de preparación
5 Delivery Asignación de couriers, rutas, tracking en tiempo real
6 Billing & Accounting Cobros, comisiones, payouts a restaurantes y couriers
7 Notifications Emails, SMS, push: confirmaciones, alertas, recibos
Estas capacidades NO son automáticamente microservicios uno-a-uno. Tu trabajo en los ADRs y diagramas C4
es decidir el grado de descomposición apropiado para FTGO según trade-offs concretos.

A.4 Restricciones técnicas y NFRs base
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

A.5 User stories semilla
Estas 3 stories son tu punto de partida obligatorio. En el FSD debes extenderlas a ≥ 5 UCs con bloques
Given/When/Then y derivar al menos 2 UCs adicionales justificables desde el brief o el libro (no inventados).
US-01: Toma de pedido por el consumidor
Como Consumidor, quiero realizar un pedido desde el menú de un restaurante seleccionado, para recibir mi
comida en casa de forma rápida y confiable.
Aceptación esperada en el FSD:
• El consumidor puede ver el menú del restaurante elegido.
• Puede agregar/quitar ítems a su carrito.
• Puede confirmar el pedido con dirección de entrega y método de pago.
• El sistema valida disponibilidad del restaurante y stock.
• El consumidor recibe confirmación con un número de pedido único.

US-02: Aceptación de tickets por el restaurante
Como Restaurante, quiero aceptar o rechazar los tickets de pedido entrantes, para gestionar la carga de mi
cocina sin saturarla.
Aceptación esperada en el FSD:
• El restaurante recibe notificación de nuevos tickets en su dashboard.
• Puede aceptar (con tiempo estimado de preparación) o rechazar con motivo.
• El consumidor recibe actualización del estado del pedido.
• Si rechaza, el pedido se cancela y se notifica al consumidor.
US-03: Asignación de entrega al courier
Como Courier, quiero recibir asignaciones de entrega cercanas y aceptarlas o rechazarlas, para optimizar mi
ruta y mis ingresos.
Aceptación esperada en el FSD:
• El courier marca disponibilidad en la app.
• El sistema le ofrece pedidos listos para retirar cerca de su ubicación.
• El courier acepta o rechaza dentro de un timeout (ej. 30 s).
• Al aceptar, el courier ve la ruta optimizada al restaurante y al consumidor.
UCs adicionales que puedes derivar (ejemplos válidos): pago en el momento del checkout, tracking en tiempo
real del consumidor, dashboard de reportes para el back office, reasignación automática si un courier rechaza,
manejo de cancelaciones por el consumidor, gestión de menús del restaurante.
A.6 Restricciones del laboratorio
• Fuente única del dominio: este brief + el PDF de Richardson. No inventar stakeholders, capacidades o
NFRs fuera de aquí.
• Trazabilidad obligatoria: cada decisión del PRD/FSD/ADR/C4 debe citar el origen (brief sección X, libro
cap Y, US-NN).
• Granularidad apropiada: este es un PRD/FSD ligero, no exhaustivo. Cubre lo esencial, no agotes.
• Migración incremental como contexto: tus ADRs deben considerar que el monolito sigue vivo y la
migración es gradual (Strangler Fig).
A.7 Enlaces de referencia
• Repositorio oficial FTGO: https://github.com/microservices-patterns/ftgo-application
• Microservices Pattern Language: https://microservices.io/
