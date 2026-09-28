> Que puedes decirme de "Integración síncrona y asíncrona"

Claro. Esta distinción es fundamental en arquitectura de microservicios, y además conecta directamente con lo que acabamos de hablar de Service Bus, Event Hubs y Kafka.

La idea básica es:

Síncrona: "Te llamo y espero tu respuesta."
Asíncrona: "Te dejo un mensaje/evento y continúo; no necesito esperarte."

1. Integración síncrona

Imagina este flujo:

```text
Cliente
   │
   │ HTTP POST /orders
   ▼
Order Service
   │
   │ HTTP POST /payments
   ▼
Payment Service
   │
   │ respuesta
   ▼
Order Service
   │
   │ respuesta
   ▼
Cliente
```

El Order Service necesita esperar al Payment Service.

Por ejemplo:

```text
POST /payments

{
  "orderId": "ORD-1001",
  "amount": 250
}
```

Y espera:

```text
{
  "paymentId": "PAY-9001",
  "status": "APPROVED"
}
```

Durante ese proceso existe una dependencia temporal:

```text
Order Service ─────── espera ───────► Payment Service
                    ◄───────────────
                       respuesta
```

Ejemplos típicos
REST / HTTP
gRPC
SOAP
llamadas directas entre microservicios 2. Integración asíncrona

Ahora cambia el escenario.

```text
Cliente
   │
   ▼
Order Service
   │
   │ "OrderCreated"
   ▼
Message Broker
   │
   ├──────────────► Payment Service
   │
   ├──────────────► Inventory Service
   │
   └──────────────► Notification Service
```

El Order Service no necesita esperar a que Payment, Inventory o Notification terminen.

Publica:

```text
{
  "eventType": "OrderCreated",
  "orderId": "ORD-1001",
  "amount": 250
}
```

Y puede responder inmediatamente al cliente:

```text
201 Created
```

Mientras tanto:

```text
                  Message Broker
                       │
          ┌────────────┼────────────┐
          ▼            ▼            ▼
       Payment      Inventory   Notification
        Service       Service       Service
```

Esto es integración asíncrona.

3. Una analogía sencilla
   Síncrono = llamada telefónica ☎️

Tú llamas:

> "¿Puedes hacer esto?"

La otra persona responde:

> "Sí."

Tú esperas.

Si no contesta:

> "No puedo continuar."

Asíncrono = correo electrónico 📧

Tú envías:

> "Por favor procesa esta orden."

Y continúas con tu trabajo.

La otra persona lo leerá y procesará cuando pueda.

4. ¿Dónde entran Service Bus, Event Hubs y Kafka?

Aquí se conecta con nuestra conversación anterior.

Síncrono

Normalmente:

```text
Microservice A
      │
      │ HTTP/gRPC
      ▼
Microservice B
      │
      │ response
      ▼
Microservice A
```

No necesitas Kafka ni Service Bus.

Asíncrono con Service Bus

```text
Order Service
      │
      │ Command
      ▼
Service Bus Queue
      │
      ▼
Payment Service
```

Por ejemplo:

```text
ProcessPayment
```

Esto es un comando:

> "Payment Service, haz esto."

Asíncrono con Kafka/Event Hubs

```text
Order Service
      │
      │ Event
      ▼
Kafka / Event Hubs
      │
      ├──► Payment
      ├──► Analytics
      ├──► Data Lake
      └──► Notifications
```

Por ejemplo:

```text
OrderCreated
```

Esto es un evento:

> "La orden fue creada."

No necesariamente estás diciendo quién debe hacer algo.

5. Una diferencia conceptual muy importante

Mira estas dos frases:

**Comando**

> ProcessPayment

Significa:

> **"Haz algo."**

Normalmente asociado a:

```text
Service Bus
```

Evento

> PaymentProcessed

Significa:

**"Algo ya ocurrió."**

Normalmente asociado a:

```text
Kafka / Event Hubs
```

Esta distinción no es absoluta, pero es una regla arquitectónica muy útil.

6. ¿Por qué usar integración asíncrona?

Porque elimina parte del acoplamiento entre servicios.

Supongamos:

```text
Order
  │
  ├──► Payment
  ├──► Inventory
  ├──► Notification
  └──► Analytics
```

En síncrono:

```text
Order
 │
 ├──► Payment ─────► espera
 │
 ├──► Inventory ───► espera
 │
 ├──► Notification ► espera
 │
 └──► Analytics ───► espera
```

Ahora tienes varios puntos de fallo.

Si `Notification Service` está caído:

```text
Order Service
      │
      ▼
Notification ❌
      │
      ▼
Order ❌
```

Dependiendo del diseño, la orden podría incluso fallar.

Con asincronía:

```text
Order
  │
  ▼
Service Bus
  │
  ├──► Payment
  ├──► Inventory
  ├──► Notification ❌
  └──► Analytics
```

Notification puede estar temporalmente caído.

El mensaje puede permanecer en la infraestructura de mensajería y procesarse después.

Eso nos lleva directamente a:

- Retry
- Dead Letter
- Backpressure
- Durabilidad
- Idempotencia

7. Pero asincrónico no significa "mejor"

Esto es muy importante.

Hay operaciones donde **quieres una respuesta inmediata**.

Por ejemplo:

```text
GET /customers/123
```

Probablemente quieres:

```text
Cliente
   │
   ▼
Customer API
   │
   ▼
Customer DB
   │
   ▼
Respuesta
```

No tendría mucho sentido:

```text
GET customer
   │
   ▼
Message Queue
   │
   ▼
...
```

porque el usuario está esperando los datos.

8. Entonces, ¿cuándo síncrono y cuándo asíncrono?

Una regla práctica:

| Situación                            | Preferencia                          |
| ------------------------------------ | ------------------------------------ |
| Necesito respuesta inmediatamente    | Síncrono                             |
| Consultar datos                      | Síncrono                             |
| Validación inmediata                 | Síncrono                             |
| Crear recurso y devolver resultado   | Normalmente síncrono                 |
| Trabajo pesado                       | Asíncrono                            |
| Procesamiento que puede tardar       | Asíncrono                            |
| Alta cantidad de eventos             | Asíncrono                            |
| Integración entre muchos servicios   | Asíncrono                            |
| Necesito desacoplar servicios        | Asíncrono                            |
| Analytics / streaming                | Asíncrono                            |
| Notificaciones                       | Asíncrono                            |
| Jobs/background processing Asíncrono | Jobs/background processing Asíncrono |

9. Y aquí aparece una arquitectura híbrida

En sistemas reales, lo normal es utilizar ambos.

Por ejemplo:

```text
                 ┌───────────────┐
Cliente ──HTTP──►│  Order API    │
                 └───────┬───────┘
                         │
                      síncrono
                         │
                         ▼
                   Order Database
                         │
                         │ evento
                         ▼
                   Event Hubs
                         │
            ┌────────────┼────────────┐
            ▼            ▼            ▼
         Payment      Analytics    Notification
```

La entrada es síncrona:

```text
Cliente → Order API
```

pero las consecuencias de la operación son asíncronas:

```text
OrderCreated → Event Hubs → múltiples consumidores
```

Esto es muy común en arquitecturas modernas de microservicios.

10. La pregunta arquitectónica que debes hacerte

Cuando diseñes una integración entre dos servicios, no empieces preguntando:

> "¿Uso Kafka o Service Bus?"

Primero pregunta:

**"¿Necesito esperar la respuesta?"**

Si la respuesta es sí:

```text
Síncrono
   ↓
HTTP / REST / gRPC
```

Si la respuesta es no:

```text
Asíncrono
   ↓
Queue / Event Bus / Streaming
```

Y después:

```text
¿Es un comando?
       │
       ▼
 Service Bus

¿Es un evento/stream?
       │
       ▼
 Event Hubs / Kafka
```

Ese árbol mental te va a ayudar muchísimo a diseñar microservicios.

Y hay un concepto que conviene aprender inmediatamente después: acoplamiento temporal y espacial. Es precisamente lo que explica por qué la comunicación asíncrona hace que los microservicios sean más resilientes y escalables.
