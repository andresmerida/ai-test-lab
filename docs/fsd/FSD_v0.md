# Functional Specification Document (FSD) - FTGO Platform

## 1. Introducción y Alcance
El presente **Documento de Especificación Funcional (FSD)** ligero tiene como propósito actuar como el puente conceptual y técnico definitivo entre los Requisitos de Producto (PRD) de la plataforma **FTGO (Food To Go)**, las decisiones arquitectónicas de microservicios (ADRs) y la implementación de código de backend y testing de calidad automatizado. 

Este FSD detalla los flujos transaccionales primarios y alternativos de la plataforma que posibilitan la interacción entre los distintos actores de la red (Consumidores, Restaurantes y Couriers) y los límites de los nuevos microservicios desacoplados. La característica fundamental de este documento es su estrecha integración con la Skill canónica de testing [SKILL.md](file:///Users/andresmerida/workspace/githup/ai-test-lab/docs/prompts/SKILL.md) (`fsd-gherkin-a-tests-aceptacion`). Cada Caso de Uso (UC) detallado aquí incorpora un bloque de aceptación formal escrito en sintaxis **Gherkin**. Estos escenarios de Given/When/Then, estructurados de forma tipada, libre de ambigüedades y con fixtures de datos realistas (moneda local `BOB`, platos reales y tiempos lógicos), son la entrada directa que la Skill de testing parsea para generar automáticamente suites de integración en JUnit y scripts de pruebas de carga en k6, garantizando la consistencia y trazabilidad de extremo a extremo del ciclo de desarrollo guiado por comportamiento (BDD).

---

## 2. Tabla Resumen de Casos de Uso

El FSD cubre exactamente el siguiente conjunto de 5 Casos de Uso atómicos y de alta prioridad de negocio, mapeados a las capacidades y aggregates de dominio descritos en el PRD:

| ID | Caso de Uso | Actor Primario | Capacidad PRD | Aggregate Raíz DDD | Origen (Brief/Libro) |
|---|---|---|---|---|---|
| **UC-01** | Registro y Confirmación de Pedidos | Consumidor | Order Taking | `Order` | US-01 (Brief) |
| **UC-02** | Gestión y Aceptación de Cocina de Tickets | Restaurante | Order Fulfillment | `Ticket` | US-02 (Brief) |
| **UC-03** | Asignación Dinámica de Envíos | Courier | Delivery | `DeliveryJob` | US-03 (Brief) |
| **UC-04** | Procesamiento de Cobros mediante Stripe / QR | Consumidor | Billing & Accounting | `Transaction` | Cap 3 / Billing (PRD) |
| **UC-05** | Seguimiento y Tracking GPS en Tiempo Real | Consumidor | Delivery | `Delivery` | UX Latencia (PRD) |

---

## 3. Detalle de Casos de Uso

### UC-01: Registro y Confirmación de Pedidos

| Campo | Valor |
|---|---|
| **ID** | UC-01 |
| **Actor Primario** | Consumidor |
| **Capacidad PRD** | Order Taking |
| **Aggregate Raíz** | `Order` |
| **Origen** | US-01 (Brief) |

**Precondiciones**:
* El Consumidor ha iniciado sesión de forma segura y tiene un JSON Web Token (JWT) activo en su dispositivo móvil.
* El Consumidor tiene seleccionada una dirección de entrega válida registrada dentro de su perfil.
* El Restaurante asociado se encuentra en estado abierto con horarios disponibles y tiene menús configurados y activos.

**Flujo Principal**:
1. El Consumidor explora el menú del restaurante asociado seleccionado desde la aplicación móvil.
2. El Consumidor selecciona los platos, cantidades específicas, y los añade a su carrito de compras digital.
3. El sistema valida de forma síncrona mediante gRPC la disponibilidad de stock de cada ítem en el inventario del restaurante.
4. El Consumidor confirma la orden seleccionando su dirección de entrega y el método de pago predeterminado.
5. El sistema calcula de manera precisa el subtotal de la orden, los costos de envío geográficos, los impuestos del laboratorio y genera el precio total de la orden.
6. El sistema crea el pedido en la base de datos de órdenes (`Order DB`) en estado `PENDING_PAYMENT` y devuelve un identificador de pedido único (ID de Pedido).

**Flujos Alternativos**:
* **3a. Ítem seleccionado sin stock disponible**:
  1. El sistema detecta en el servicio de Restaurant Management que la cantidad seleccionada excede el stock disponible.
  2. El sistema muestra una alerta en la aplicación móvil indicando que el plato "no cuenta con existencias suficientes".
  3. El sistema no permite avanzar hacia la pantalla de checkout y sugiere ajustar las cantidades.
* **4a. Dirección de entrega fuera del radio de cobertura**:
  1. El sistema valida la geolocalización de entrega del cliente contra el radio de despacho en kilómetros del restaurante seleccionado.
  2. Si la distancia supera el límite configurado (ej. > 8.0 km), el sistema muestra un mensaje de error "Dirección de entrega fuera de cobertura de este restaurante".
  3. Se aborta la transacción y se mantiene al consumidor en la edición del carrito.

**Postcondiciones**:
* Se registra el aggregate `Order` en la base de datos de órdenes con estado `PENDING_PAYMENT` y se encola la saga de Checkout transaccional.

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

---

### UC-02: Gestión y Aceptación de Cocina de Tickets

| Campo | Valor |
|---|---|
| **ID** | UC-02 |
| **Actor Primario** | Restaurante |
| **Capacidad PRD** | Order Fulfillment / Kitchen |
| **Aggregate Raíz** | `Ticket` |
| **Origen** | US-02 (Brief) |

**Precondiciones**:
* El pedido del Consumidor ha sido aprobado financieramente en el subsistema de Billing (`Order` en estado `PENDING_ACCEPTANCE`).
* El Restaurante asociado tiene el Dashboard Web activo en su terminal de cocina y se encuentra marcado como disponible.

**Flujo Principal**:
1. El Restaurante recibe una notificación auditiva y visual en tiempo real a través de WebSockets en su Dashboard de cocina con un nuevo ticket de preparación de pedido.
2. El personal del Restaurante revisa el listado detallado de ítems y las instrucciones especiales del pedido.
3. El Restaurante pulsa "Aceptar Ticket" ingresando el tiempo estimado de preparación estimado en minutos (ej. 35 minutos).
4. El sistema actualiza el estado del ticket en la base de datos de cocinas (`Kitchen DB`) a `ACCEPTED` y calcula la hora estimada de retiro (ETA).
5. El sistema publica asíncronamente el evento `TicketAcceptedEvent` a través del bus de mensajería Apache Kafka.
6. El microservicio de órdenes consume el evento de Kafka y actualiza el pedido a estado `PREPARING`, notificando automáticamente al Consumidor en su app móvil.

**Flujos Alternativos**:
* **3a. Rechazo manual del ticket por saturación de cocina**:
  1. El Restaurante pulsa "Rechazar Ticket" y selecciona el motivo de rechazo "Cocina saturada por alta demanda".
  2. El sistema actualiza el estado del ticket a `REJECTED` en `Kitchen DB` y publica `TicketRejectedEvent` en Kafka.
  3. La saga transaccional de órdenes consume el rechazo, actualiza el pedido a `CANCELLED`, gatilla una transacción compensatoria en Billing para reembolsar de forma automática los cargos y envía una alerta de disculpas al Consumidor.
* **3b. Cancelación automática por timeout de cocina**:
  1. El sistema inicia un temporizador de 5 minutos una vez que el ticket es encolado en el dashboard del Restaurante.
  2. Si el Restaurante no realiza ninguna acción antes de expirar el temporizador, el sistema marca el ticket como `TIMED_OUT`.
  3. El sistema cancela el pedido, libera el saldo en Billing y suspende temporalmente la recepción de pedidos automáticos del restaurante hasta que confirme disponibilidad.

**Postcondiciones**:
* El aggregate `Ticket` queda almacenado en `Kitchen DB` en estado `ACCEPTED` o `REJECTED`, y la Saga del pedido progresa al siguiente paso de logística o compensación.

**Escenario de Aceptación (Gherkin)**:
```gherkin
Feature: Gestion y aceptacion de tickets por el restaurante
  As a Restaurante
  I want to aceptar o rechazar los tickets de cocina
  So that pueda controlar la carga de preparacion de mi establecimiento

  Scenario: Aceptacion exitosa del ticket por la cocina
    Given un ticket con ID "TKT-9988" en estado "PENDING_ACCEPTANCE" en la cola de "Pizzería Don Vico"
    When el operador del Restaurante acepta el ticket ingresando un tiempo estimado de "35" minutos
    Then el sistema actualiza el ticket a estado "ACCEPTED" con hora estimada de retiro
    And publica el evento de aceptacion en el topico "kitchen-events" de Kafka
```

---

### UC-03: Asignación Dinámica de Envíos

| Campo | Valor |
|---|---|
| **ID** | UC-03 |
| **Actor Primario** | Courier |
| **Capacidad PRD** | Delivery |
| **Aggregate Raíz** | `DeliveryJob` |
| **Origen** | US-03 (Brief) |

**Precondiciones**:
* El pedido se encuentra en preparación activa en el Restaurante (`Ticket` en estado `ACCEPTED`).
* El Courier ha iniciado sesión en su app móvil, tiene activado el tracking GPS en segundo plano y se ha marcado como "Disponible".

**Flujo Principal**:
1. El Courier se marca como disponible en la aplicación móvil en un área geográfica determinada.
2. El sistema Delivery calcula mediante algoritmos geospaciales la distancia de los repartidores disponibles respecto a la ubicación del restaurante.
3. El sistema selecciona al Courier más cercano idóneo y le envía una oferta de envío de "15.00 BOB" en la app móvil.
4. El Courier pulsa "Aceptar Envío" dentro del tiempo límite de 30 segundos.
5. El sistema actualiza el trabajo de despacho en la base de datos de envíos (`Delivery DB`) a estado `ASSIGNED` y asigna el Courier a la orden.
6. El sistema calcula y muestra en la app del Courier la ruta GPS optimizada mediante la API de Google Maps desde su ubicación hasta el Restaurante y luego hacia el Consumidor.

**Flujos Alternativos**:
* **4a. Rechazo explícito de la oferta por el Courier**:
  1. El Courier pulsa "Rechazar Oferta" en su aplicación o la descarta de la pantalla.
  2. El sistema marca la oferta como rechazada y no penaliza la disponibilidad del repartidor.
  3. El sistema recalcula la disponibilidad geográfica y envía de inmediato la oferta al segundo Courier más cercano disponible.
* **4b. Timeout de asignación automática de Courier**:
  1. El sistema inicia una cuenta regresiva de 30 segundos al ofrecer el envío al Courier seleccionado.
  2. Si transcurren los 30 segundos sin respuesta, el sistema marca el intento como `TIMED_OUT`.
  3. El sistema retira la oferta de la pantalla del repartidor, lo cambia a estado "No Disponible temporalmente" para evitar asignaciones fantasmas, y reasigna el pedido a otro repartidor.

**Postcondiciones**:
* El aggregate `DeliveryJob` queda guardado en estado `ASSIGNED` en `Delivery DB`, vinculando de forma unívoca el ID del Courier con el ID del Pedido.

**Escenario de Aceptación (Gherkin)**:
```gherkin
Feature: Asignacion dinamica de envios al courier
  As a Courier
  I want to recibir ofertas de entrega cercanas y aceptarlas
  So that pueda optimizar mis rutas de traslado e ingresos

  Scenario: Asignacion y aceptacion exitosa de despacho
    Given el Courier "Juan Perez" se encuentra en estado "Disponible" en el radio de "1.5" km del restaurante "Pizzería Don Vico"
    When el sistema ofrece el despacho del pedido "ORD-5544" con tarifa de "15.00" BOB
    And el Courier acepta la oferta dentro del tiempo de "30" segundos
    Then el sistema genera un DeliveryJob en estado "ASSIGNED" vinculado al Courier "Juan Perez"
    And despliega la ruta de navegacion optimizada al restaurante en la app
```

---

### UC-04: Procesamiento de Cobros mediante Stripe / QR

| Campo | Valor |
|---|---|
| **ID** | UC-04 |
| **Actor Primario** | Consumidor |
| **Capacidad PRD** | Billing & Accounting |
| **Aggregate Raíz** | `Transaction` |
| **Origen** | Cap 3 / Billing (PRD) |

**Precondiciones**:
* El pedido se encuentra en estado `PENDING_PAYMENT` en el subsistema de órdenes.
* El Consumidor tiene una tarjeta de crédito/débito tokenizada asociada a su perfil, o solicita la generación de un código QR interoperable.

**Flujo Principal**:
1. El Consumidor aprueba el pago del total calculado de "135.60 BOB" en la pantalla de checkout de la aplicación.
2. El microservicio de Billing captura la solicitud y procesa la transacción transaccional enviando un payload seguro con el token de tarjeta a la API REST de la pasarela de pagos Stripe.
3. Stripe valida los datos y procesa de forma exitosa el cargo financiero retornando un ID de transacción único de pasarela.
4. El microservicio de Billing registra de forma atómica la transacción en estado `SUCCESSFUL` en `Billing DB`.
5. El sistema publica el evento `PaymentAuthorizedEvent` en el tópico de eventos de Kafka.
6. La saga de órdenes consume el evento de pago autorizado, actualiza el estado del pedido a `PAID` en `Order DB` y gatilla la cola de tickets de cocina en Kitchen.

**Flujos Alternativos**:
* **3a. Transacción rechazada por fondos insuficientes en Stripe**:
  1. La pasarela de pagos Stripe responde a la API de Billing con un código de error de procesamiento `card_declined` y detalle "Fondos insuficientes".
  2. El microservicio de Billing registra el intento de pago en la base de datos en estado `DECLINED`.
  3. El sistema publica `PaymentFailedEvent` en Kafka.
  4. La saga de órdenes actualiza la orden a `PAYMENT_FAILED` y la aplicación móvil solicita al Consumidor cambiar el método de pago para reintentar.
* **3b. Interrupción de red y caída temporal de Stripe (Resiliencia Asíncrona)**:
  1. El sistema intenta llamar a la API de Stripe pero experimenta un timeout de conexión debido a una caída de la red WAN externa.
  2. El microservicio de Billing activa el Circuit Breaker de seguridad y detecta la interrupción del proveedor externo (`NFR-04`).
  3. El sistema guarda la transacción en estado `QUEUE_RETRY`, aprueba provisionalmente la orden bajo consistencia eventual para no perder la venta de comida, y encola la petición en una cola asíncrona persistente para intentar procesar el cobro repetidamente en intervalos de 5 minutos durante 15 minutos en background.

**Postcondiciones**:
* La transacción queda persistida en `Billing DB` en estado `SUCCESSFUL` o encolada de forma resiliente en `QUEUE_RETRY` bajo consistencia eventual de la pasarela de pagos.

**Escenario de Aceptación (Gherkin)**:
```gherkin
Feature: Procesamiento de cobros de pedidos
  As a Consumidor
  I want to pagar mi pedido mediante tarjeta de credito de forma segura
  So that pueda completar mi compra exitosamente

  Scenario: Cargo exitoso a tarjeta de credito procesado en Stripe
    Given el pedido con ID "ORD-5544" requiere un cobro total de "135.60" BOB
    And el Consumidor tiene una tarjeta de credito Visa tokenizada "tok_12345" registrada
    When el sistema Billing procesa el cargo de "135.60" BOB contra la API de Stripe usando el token "tok_12345"
    Then Stripe aprueba la operacion y devuelve el identificador de transaccion "ch_998877"
    And el sistema registra la transaccion en estado "SUCCESSFUL" en la base de datos contable
```

---

### UC-05: Seguimiento y Tracking GPS en Tiempo Real

| Campo | Valor |
|---|---|
| **ID** | UC-05 |
| **Actor Primario** | Consumidor |
| **Capacidad PRD** | Delivery |
| **Aggregate Raíz** | `Delivery` |
| **Origen** | UX Latencia (PRD) |

**Precondiciones**:
* El pedido se encuentra en estado de entrega física activa (`DeliveryJob` en estado `PICKED_UP` o `DELIVERING`).
* El Courier asignado tiene encendido el GPS del dispositivo móvil con transmisión de red móvil activa.

**Flujo Principal**:
1. El Consumidor ingresa a la vista detallada de tracking del pedido en curso desde su aplicación móvil móvil.
2. La aplicación móvil del Courier envía de forma automatizada las coordenadas GPS del terminal (latitud y longitud) cada 5 segundos mediante canales de comunicación livianos y bidireccionales basados en WebSockets.
3. El microservicio de Delivery captura la trama GPS, actualiza la geolocalización en `Delivery DB` y publica las coordenadas en el bus Kafka.
4. El servidor de WebSockets de notificaciones consume el evento del bus Kafka y retransmite las coordenadas a la aplicación móvil del Consumidor en tiempo real con una latencia percibida < 200 ms (`NFR-01`).
5. La aplicación móvil del Consumidor renderiza interactivamente la ubicación exacta del Courier en el mapa interactivo.

**Flujos Alternativos**:
* **2a. Pérdida temporal de conexión móvil y GPS del Courier**:
  1. El sistema no recibe actualizaciones de telemetría del Courier durante un período mayor a 30 segundos.
  2. El sistema detecta la ausencia de señales GPS y marca la conexión como degradada temporalmente.
  3. La aplicación móvil del Consumidor muestra una notificación visual indicando "Courier sin señal móvil temporal" y mantiene el pin de ubicación en el último punto geográfico conocido, evitando saltos erráticos en la UI.
* **4a. Degradación del servicio de geocodificación de mapas externo**:
  1. El sistema experimenta fallos o límites superados al llamar a la API externa de Google Maps para calcular el tiempo estimado de entrega (ETA).
  2. El sistema activa la tolerancia a fallos locales, calcula un ETA heurístico aproximado basado en la última velocidad de traslado conocida del Courier y renderiza la ubicación sobre un mapa básico offline sin bloquear la pantalla de tracking.

**Postcondiciones**:
* Las coordenadas de ubicación del repartidor se transmiten continuamente, y las coordenadas se guardan como telemetría geoespacial en la base de datos de envíos.

**Escenario de Aceptación (Gherkin)**:
```gherkin
Feature: Seguimiento y tracking GPS en tiempo real
  As a Consumidor
  I want to visualizar la ubicacion exacta de mi courier en el mapa
  So that pueda conocer con precision el momento de llegada de mi pedido

  Scenario: Transmision fluida y exitosa de telemetria GPS
    Given el Courier "Juan Perez" ha retirado el pedido "ORD-5544" de "Pizzería Don Vico"
    And la app movil del Consumidor tiene la pantalla de tracking abierta
    When el dispositivo movil del Courier transmite la coordenada "lat: -17.3894, lon: -66.1568" via WebSockets al backend
    Then el sistema retransmite las coordenadas a la app del Consumidor en menos de "200" ms
    And el mapa movil del cliente actualiza la ubicacion del repartidor de forma fluida
```

---

## Métricas

> **Instrucción de llenado**: El modelo debe completar esta sección al final de cada ejecución del prompt, registrando el número de ejecución secuencial, el ID y versión del prompt, y los valores concretos de cada métrica con sus respectivos insights.

**#️⃣ Ejecución**: `0`
**🔖 Prompt**: `PR-FSD-FTGO-001` — versión `v1.1-mejorada`

| Nombre de la métrica | Valor | Insights |
|---|---|---|
| **Completitud de Casos de Uso** (`uc_coverage`) | `5/5 UCs` | Se han documentado en su totalidad los 5 Casos de Uso atómicos prioritarios e ineludibles definidos por el prompt. |
| **Tasa de bloques Gherkin válidos** (`gherkin_syntax_pass_rate`) | `100%` | El 100% de los UCs cuentan con bloques de sintaxis Gherkin (Feature/Scenario/Given/When/Then) parseables. |
| **Flujos alternativos por UC** (`alt_flows_rate`) | `5/5 UCs` | Todos los Casos de Uso (UC-01 al UC-05) especifican al menos 2 flujos alternativos o caminos de fallo de infraestructura. |
| **Trazabilidad UC→US/Capacidad** (`uc_traceability_rate`) | `100%` | El 100% de los Casos de Uso se traza de forma unívoca a una User Story semilla y a capacidades core y de infraestructura del PRD. |
| **Fixtures de datos realistas** (`realistic_fixtures`) | `Sí` | Se emplearon valores del dominio real (BOB como moneda, pizzería local y nombres de ítems) para alimentar los tests de BDD de SKILL.md. |
| **Ausencia de placeholders vacíos** (`placeholder_free`) | `Sí` | El documento funcional FSD consolidado se encuentra completamente libre de placeholders, TODOs o textos inconclusos. |
