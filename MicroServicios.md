# MicroServicios

**1. Antecedentes**
Durante décadas, el estándar fue la **Arquitectura Monolítica**, donde toda la aplicación (UI, lógica de negocio y acceso a datos) reside en una sola unidad de despliegue. A medida que las aplicaciones crecen, los monolitos se convierten en "Big Balls of Mud" (grandes bolas de lodo): el código se vuelve interdependiente, el tiempo de compilación aumenta y un error en un módulo pequeño puede tirar abajo todo el sistema. Además, obligan a escalar toda la aplicación aunque solo una función (ej. el carrito de compras) esté saturada.

**2. Respuesta técnica**

**Definición:**
La **Arquitectura de Microservicios** es un enfoque de diseño de software donde una aplicación se divide en un conjunto de servicios pequeños, autónomos y débilmente acoplados. Cada servicio se encarga de una **capacidad de negocio específica**, posee su propia base de datos (Database per Service) y se comunica con los demás a través de protocolos ligeros (HTTP/REST, gRPC o Message Brokers).

**Implementación (Hoja de Ruta Técnica):**

Para implementar microservicios en el stack de .NET, seguiría estos pilares:

**A. Definición de Límites (Bounded Contexts)**
No se trata de dividir por "capas", sino por "dominios". Usaría **Domain Driven Design (DDD)** para identificar los límites.
*   *Ejemplo:* En un E-commerce, separaría el servicio de `Catálogo`, el de `Pedidos` y el de `Pagos`. Cada uno es un proyecto independiente en la solución.

**B. Comunicación entre Servicios**
Implementaría dos tipos de comunicación según la necesidad:
*   **Sincrónica (HTTP/gRPC):** Para peticiones inmediatas. Usaría **gRPC** para comunicación interna entre servicios por su alta eficiencia (binario) y **REST/WebAPI** para la comunicación externa.
*   **Asincrónica (Event-Driven):** Para evitar el acoplamiento fuerte. Usaría un Message Broker como **RabbitMQ** o **Azure Service Bus**. Cuando un pedido se crea, el servicio de `Pedidos` publica un evento `OrderCreated`, y el servicio de `Pagos` lo consume.

**C. Gestión de Tráfico y Seguridad (API Gateway)**
Para evitar que el cliente tenga que conocer 20 URLs diferentes, implementaría un **API Gateway** (usando **Ocelot** o **Azure API Management**). El Gateway actúa como punto de entrada único que redirige las peticiones al microservicio correspondiente y maneja la autenticación centralizada (**JWT/OAuth2**).

**D. Persistencia de Datos**
Aplicaría el patrón **Database per Service**. El servicio de `Catálogo` podría usar MongoDB (NoSQL) por la flexibilidad de los productos, mientras que el de `Pagos` usaría SQL Server por la necesidad de transacciones ACID.


**E. Observabilidad y Resiliencia**
Dado que el sistema ahora está distribuido, implementaría:
*   **Resiliencia:** Usaría la librería **Polly** en .NET para implementar patrones de *Retry* (reintentos) y *Circuit Breaker* (cortocircuito) para evitar que un servicio caído provoque un efecto dominó.
*   **Trazabilidad:** Implementaría **OpenTelemetry** o **Seq** para tener un `CorrelationId` que permita rastrear una petición a través de todos los microservicios.

**F. Despliegue y Orquestación**
Finalmente, empaquetaría cada microservicio en un **Contenedor Docker** y utilizaría **Kubernetes (K8s)** o **Azure Kubernetes Service (AKS)** para gestionar el escalado automático, el balanceo de carga y la salud de los pods.


</br>

**Transacciones ACID**
Las Transacciones ACID son un conjunto de cuatro propiedades que garantizan que las operaciones de la base de datos sean confiables, consistentes y seguras, incluso si hay error en la ejecución.

**ACID Significa.**
<div style="background-color: #8db9d6; padding: 12px; border-left: 4px solid; border-radius: 4px;">
    <strong>Atomicidad (Atomicity)</strong></br>
    Una transacción se ejecuta completamente o no se ejecuta nada. Si falla una operación, se revierten todos los cambios.
</div>
</br>
<div style="background-color: #aeda9b; padding: 12px; border-left: 4px solid; border-radius: 4px;">
    <strong>Consistencia (Consistensity)</strong></br>
    La Transacción lleva a la base de datos de un estado válido a otro, respetando las reglas y restricciones definidas.
</div>
</br>
<div style="background-color: #e8e4bf; padding: 12px; border-left: 4px solid; border-radius: 4px;">
    <strong>Aislamiento (Isolation)</strong></br>
    Las Transacciones que se ejecutan simultáneamente no deben intervenir entre sí de manera que se produzcan resultados incorrectos.
</div>
</br>
<div style="background-color: #c1add8; padding: 12px; border-left: 4px solid; border-radius: 4px;">
    <strong>Durabilidad (Durability)</strong></br>
    Una vez confirmada las transacción, los cambios deben persistir, incluso si hay una caída del sistema.
</div>

---
---
---
>Podrias mejorar la respuesta? 

Para mejorar la respuesta y elevarla a un nivel de **Líder Técnico**, debemos pasar de una descripción "manual" a una visión "estratégica". Un líder no solo dice *qué* herramientas usar, sino que justifica las **decisiones arquitectónicas**, analiza los **trade-offs** (ventajas vs. desventajas) y propone un modelo de **gobernanza**.

Aquí tienes la versión optimizada:

---

**1. Antecedentes**
La transición hacia los microservicios no es una meta técnica, sino una respuesta a la necesidad de **escalabilidad organizacional y técnica**. Mientras que el monolito optimiza la simplicidad inicial, los microservicios optimizan la velocidad de despliegue y la autonomía de los equipos. El desafío principal no es la tecnología, sino la gestión de la **complejidad distribuida**: pasar de llamadas a funciones en memoria a llamadas de red, que son inherentemente lentas y fallibles.

**2. Respuesta técnica (Visión de Liderazgo)**

Defino la arquitectura de microservicios como un estilo arquitectónico que descompone la aplicación en servicios alineados con capacidades de negocio, desplegables independientemente y comunicados mediante interfaces bien definidas.

Para implementarlo con éxito, basaría la estrategia en estos cinco ejes:

**A. Descomposición Estratégica (DDD)**
No dividiría el sistema por "entidades", sino por **Bounded Contexts** usando Domain Driven Design. Esto evita el "Micro-monolito" (servicios demasiado pequeños que requieren llamadas constantes entre sí). 
*   **Decisión:** Definiría un modelo de datos segregado donde cada servicio es dueño absoluto de su esquema, eliminando las dependencias a nivel de base de datos.

**B. Estrategia de Comunicación y Consistencia**
Implementaría un modelo híbrido para balancear latencia y disponibilidad:
*   **Sincrónica (Orquestación):** Usaría **gRPC** para comunicación interna por su bajo overhead y tipado fuerte con Protocol Buffers, dejando **REST** solo para el consumo externo.
*   **Asincrónica (Coreografía):** Para procesos de negocio extensos, implementaría el **Patrón Saga** (Saga Pattern) mediante un bus de eventos (Azure Service Bus/Kafka). Esto garantiza la **Consistencia Eventual** sin bloquear los servicios.

**C. Capa de Abstracción y Seguridad (Edge Layer)**
Implementaría un **API Gateway** como punto de entrada único.
*   **Responsabilidades:** Cross-cutting concerns como el *Rate Limiting*, la terminación de SSL y la autenticación mediante **IdentityServer4** o **Azure AD usando tokens JWT**. Esto libera a los microservicios de gestionar la seguridad repetitivamente.

**D. Diseño para el Fallo (Resiliencia)**
En un sistema distribuido, el fallo es inevitable. Implementaría una estrategia de "Defensa en Profundidad":
*   **Circuit Breaker & Bulkheads:** Mediante la librería **Polly**, evitaría que la saturación de un servicio degrade el rendimiento de todo el ecosistema.
*   **Health Checks:** Implementaría endpoints de `/health` en cada servicio para que el orquestador (Kubernetes) pueda reiniciar instancias no saludables automáticamente.

**E. Operatividad y Observabilidad (The Golden Signals)**
Un sistema distribuido es "ciego" sin observabilidad. Establecería un stack de telemetría basado en:
*   **Distributed Tracing:** Uso de **OpenTelemetry** para seguir el flujo de una petición desde el Gateway hasta el último servicio.
*   **Centralized Logging:** Implementación de **ELK Stack** (Elasticsearch, Logstash, Kibana) o **Azure Monitor**.
*   **Métricas:** Dashboards en **Grafana** para monitorear la tasa de errores, latencia y saturación.

**Análisis de Trade-offs (El toque final del líder):**
Finalmente, advertiría que los microservicios introducen una **"Tasa de Impuesto de Complejidad"**. Solo los recomendaría si el equipo tiene la madurez en DevOps necesaria y si la escala del negocio justifica la complejidad de gestionar la red, la consistencia eventual y el despliegue distribuido. Si la aplicación es pequeña, un **Monolito Modular** sería la decisión técnica más responsable.