# Describir Soluciones de Integracion

“Describir soluciones de integración consiste en explicar cómo diferentes sistemas, aplicaciones, servicios o plataformas se comunican entre sí para intercambiar información y ejecutar procesos de negocio de forma coordinada.

Como Líder Técnico trabajando con .NET Core, mi responsabilidad sería definir cómo se integran los sistemas, qué tecnologías utilizar, cómo se manejan los errores y cómo garantizamos la seguridad, la consistencia de los datos y la disponibilidad de la solución.”

## 1. ¿Qué tipos de integración existen?

Principalmente, considero los siguientes enfoques:

### A. Integración síncrona mediante APIs REST

Un sistema realiza una petición HTTP y espera una respuesta del otro sistema.

- Tecnología: ASP.NET Core Web API.
- Formato habitual: JSON.
- Ejemplo: una aplicación consulta el servicio de clientes para obtener sus datos antes de procesar un pedido.

La utilizaría cuando necesitamos una respuesta inmediata. Como desventaja, existe una dependencia de disponibilidad y tiempo de respuesta entre los servicios.

### B. Integración asíncrona mediante mensajería

Un sistema publica un mensaje o evento y otro lo procesa posteriormente, sin necesidad de que ambos estén disponibles al mismo tiempo.

- Tecnologías: Azure Service Bus, RabbitMQ o Apache Kafka, según el caso.
- Ejemplo: cuando se confirma un pedido, se publica un evento que otros componentes utilizan para actualizar inventario, generar una factura o enviar una notificación.

Este enfoque reduce el acoplamiento temporal y facilita absorber picos de carga, aunque requiere gestionar duplicados, reintentos y consistencia eventual.

### C. Integración mediante procesos batch

Consiste en intercambiar o procesar información por lotes, mediante archivos o tareas programadas.

- Tecnologías: procesos .NET, servicios en segundo plano, Azure Data Factory o mecanismos de transferencia de archivos.
- Ejemplo: importar diariamente miles de registros desde un sistema ERP.

Lo utilizaría cuando el negocio no requiere procesamiento en tiempo real.

## 2. ¿Cómo diseñaría una solución de integración con .NET Core?

Supongamos que desarrollamos un sistema de pedidos que necesita comunicarse con un ERP externo.

Sistema de pedidos

ASP.NET Core Web API

Capa de integración

Adaptadores, contratos, autenticación y transformación de datos

ERP externo

Sistema empresarial de terceros

Mi propuesta sería:

1. Definir el contrato de comunicación: endpoints, estructura de datos, códigos de respuesta y reglas de validación.
2. Implementar un cliente HTTP mediante `IHttpClientFactory`, evitando administrar manualmente las conexiones.
3. Configurar autenticación segura, por ejemplo, OAuth 2.0 cuando el proveedor lo soporte.
4. Establecer tiempos de espera, reintentos limitados y circuit breaker cuando corresponda.
5. Registrar logs estructurados y métricas para identificar fallos y medir tiempos de respuesta.
6. Implementar pruebas unitarias, de integración y de contrato.
7. Definir cómo se recupera el proceso cuando el ERP está temporalmente fuera de servicio.

Si la integración no necesita una respuesta inmediata, evaluaría mensajería asíncrona para evitar que la disponibilidad del ERP afecte directamente al sistema de pedidos.

## 3. ¿Qué patrones de integración conozco?

Como líder técnico, es importante saber cuándo aplicar cada patrón:

- API Gateway: centraliza la entrada a múltiples APIs y puede aplicar políticas de seguridad, enrutamiento y limitación de solicitudes.
- Message Broker: desacopla productores y consumidores mediante colas o temas.
- Publish/Subscribe: permite que varios consumidores reaccionen a un mismo evento.
- Adapter: encapsula las particularidades de un sistema externo para evitar que contaminen el dominio de negocio.
- Anti-Corruption Layer: traduce modelos externos al modelo interno del negocio, protegiendo los límites del dominio.
- Transactional Outbox: permite coordinar de forma confiable la persistencia de un cambio de negocio y la publicación de su evento.

No aplicaría todos estos patrones por defecto; seleccionaría los que resuelvan problemas concretos del proyecto.

## 4. ¿Cómo manejaría errores y consistencia de datos?

Esta parte es especialmente importante en una entrevista técnica.

- Definiría timeouts para evitar solicitudes bloqueadas indefinidamente.
- Implementaría reintentos con backoff exponencial y variación aleatoria cuando el error sea transitorio.
- Evitaría reintentar indiscriminadamente operaciones que puedan duplicar transacciones.
- Utilizaría claves de idempotencia cuando sea necesario garantizar que una misma operación no se ejecute dos veces.
- En mensajería, contemplaría colas de mensajes fallidos o dead-letter queues.
- Para procesos distribuidos, evaluaría consistencia eventual y patrones de compensación, como Saga, cuando existan transacciones de negocio que abarquen varios servicios.

Por ejemplo, si se registra un pedido y el ERP no está disponible, no necesariamente cancelaría todo el proceso. Dependiendo de las reglas de negocio, podría guardar el pedido, registrar la integración pendiente y reintentar el envío de forma controlada.

## 5. ¿Qué aspectos de seguridad consideraría?

- Comunicación mediante HTTPS y validación de certificados.
- Autenticación y autorización entre sistemas.
- Gestión de secretos fuera del código fuente.
- Validación de los datos recibidos y enviados.
- Protección de información sensible en logs.
- Control de acceso y auditoría de operaciones.
- Límites de consumo y protección frente a solicitudes abusivas.

## 6. ¿Cómo respondería en una entrevista como Líder Técnico?

“Cuando diseño una solución de integración, primero identifico los sistemas involucrados, los requisitos de negocio, el volumen de información, la latencia esperada y las necesidades de disponibilidad y consistencia.

A partir de eso, decido si conviene una integración síncrona mediante APIs REST, una comunicación asíncrona mediante mensajería o un proceso batch. En .NET Core suelo utilizar `IHttpClientFactory` para consumir APIs y, cuando el escenario lo requiere, tecnologías como Azure Service Bus o RabbitMQ para desacoplar procesos.

También defino los contratos de integración, la seguridad, las políticas de reintentos, la idempotencia, la observabilidad y las estrategias de recuperación ante fallos. Finalmente, documento las decisiones, coordino las pruebas con los equipos involucrados y verifico que la solución sea mantenible y cumpla los requisitos del negocio.

Mi objetivo es lograr que los sistemas intercambien información de manera segura y confiable, minimizando el acoplamiento y evitando que una falla externa comprometa innecesariamente toda la operación.”

Consejo para la entrevista: no te limites a mencionar tecnologías. Para demostrar nivel de Líder Técnico, explica por qué eliges una integración síncrona o asíncrona, qué riesgos implica y cómo garantizas que el sistema siga funcionando ante fallos.