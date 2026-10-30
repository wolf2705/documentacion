# Designing enterprise application architecture


“Designing Enterprise Application Architecture, o diseño de arquitectura de aplicaciones empresariales, es el proceso de definir la estructura de una solución de software, sus componentes, las responsabilidades de cada uno, cómo se comunican y qué principios técnicos se utilizarán para cumplir los requisitos funcionales y no funcionales del negocio.

Como Líder Técnico trabajando con .NET Core, mi responsabilidad no sería solamente definir cómo desarrollar una funcionalidad, sino diseñar una arquitectura escalable, segura, mantenible y alineada con los objetivos del negocio.

## 1. ¿Qué aspectos considero al diseñar una arquitectura empresarial?

Yo comenzaría por entender los requisitos del negocio y después definiría los siguientes aspectos:

- Requisitos funcionales: qué debe hacer el sistema, por ejemplo, registrar clientes, procesar pagos o administrar pedidos.
- Requisitos no funcionales: rendimiento, disponibilidad, escalabilidad, seguridad, mantenibilidad y tolerancia a fallos.
- Separación de responsabilidades: definiría qué corresponde a la presentación, la lógica de negocio, el acceso a datos y los sistemas externos.
- Integración: establecería cómo se comunican los componentes mediante APIs REST, mensajería o eventos.
- Persistencia: elegiría la tecnología de almacenamiento según las necesidades del dominio, las transacciones y el volumen de información.
- Operación: consideraría monitoreo, logging, trazabilidad, despliegues automatizados y recuperación ante fallos.

## 2. ¿Qué arquitectura utilizaría en un proyecto con .NET Core?

Dependería de la complejidad del sistema, el tamaño del equipo y las necesidades de escalabilidad. Para una aplicación empresarial de complejidad media o alta, consideraría Clean Architecture, aplicando sus principios de separación de responsabilidades e inversión de dependencias.

Por ejemplo, una estructura de solución podría ser:

Presentation / API

ASP.NET Core Web API, controllers, endpoints y validación HTTP

Application

Casos de uso, servicios de aplicación, DTOs y orquestación

Domain

Entidades, reglas de negocio, objetos de valor e invariantes

Infrastructure

Entity Framework Core, SQL Server, servicios externos y mensajería

Las dependencias del código apuntan hacia el dominio y las abstracciones internas; Infrastructure implementa las interfaces que necesita Application o Domain.

La ventaja de este enfoque es que la lógica de negocio no queda acoplada directamente a Entity Framework Core, a la base de datos o al framework web. Esto facilita las pruebas, los cambios tecnológicos y el mantenimiento.

Por ejemplo, si en el futuro necesito cambiar la forma en que consulto los datos, podría modificar la implementación de infraestructura sin tener que reescribir las reglas centrales del negocio.

## 3. ¿Cómo decido entre un monolito y microservicios?

Esta es una decisión importante en una entrevista de Líder Técnico.

- Monolito modular: lo elegiría cuando el sistema y el equipo pueden trabajar de manera eficiente dentro de una aplicación desplegable, manteniendo módulos con límites claros. Reduce la complejidad operativa.
- Microservicios: los consideraría cuando existen necesidades concretas de despliegue independiente, escalabilidad diferenciada, autonomía de equipos o aislamiento de determinados componentes.

No elegiría microservicios simplemente porque sean una tendencia. Implican complejidad adicional en comunicación distribuida, observabilidad, consistencia de datos, seguridad y operación.

Mi principio sería comenzar con la arquitectura más sencilla que satisfaga los requisitos y evolucionarla cuando existan razones de negocio y técnicas para hacerlo.

## 4. ¿Cómo garantizaría calidad y mantenibilidad?

Como líder técnico, establecería estándares y prácticas desde el inicio:

- Aplicación de principios SOLID y bajo acoplamiento.
- Pruebas unitarias, de integración y de contrato cuando correspondan.
- Revisión de código y análisis estático.
- Gestión explícita de errores y validación de entradas.
- Autenticación y autorización mediante mecanismos adecuados, como OAuth 2.0 y OpenID Connect.
- CI/CD para automatizar compilación, pruebas y despliegues.
- Observabilidad con logs estructurados, métricas y trazas distribuidas.
- Documentación de las decisiones arquitectónicas mediante ADR (Architecture Decision Records).

También revisaría periódicamente la deuda técnica y los indicadores de calidad, priorizando los cambios que reduzcan riesgos o aporten valor real.

## 5. Ejemplo práctico en una empresa

Supongamos que debemos desarrollar una plataforma de gestión de pedidos.

Yo dividiría la solución en módulos claramente delimitados, como clientes, catálogo, pedidos y pagos. Utilizaría ASP.NET Core para exponer las APIs, una capa de aplicación para coordinar los casos de uso, un dominio con las reglas del negocio y una capa de infraestructura para persistencia e integraciones.

Si al confirmar un pedido necesitamos notificar a un sistema externo, evaluaría mensajería asíncrona para desacoplar los procesos. Para evitar inconsistencias entre la base de datos y los mensajes, consideraría patrones como Transactional Outbox, según los requisitos de fiabilidad.

Además, definiría cómo manejar reintentos, idempotencia, seguridad, monitoreo y fallos parciales. La arquitectura no se limita a organizar clases y proyectos: también debe contemplar cómo se comporta el sistema en producción.

## 6. ¿Cómo explicaría mi responsabilidad como Líder Técnico?

“Mi enfoque para diseñar una arquitectura empresarial comienza con entender el negocio, identificar los requisitos funcionales y no funcionales, y traducirlos en decisiones técnicas justificadas.

Defino los límites de los componentes, las responsabilidades de cada capa, los mecanismos de integración y las estrategias de seguridad, pruebas y despliegue. En el ecosistema .NET suelo considerar Clean Architecture y principios SOLID para mantener el código desacoplado y testeable.

También procuro que las decisiones arquitectónicas sean compartidas con el equipo, documentadas y evaluadas según sus ventajas, costos y riesgos. Mi objetivo no es construir la arquitectura más compleja, sino la que resuelva las necesidades actuales y permita evolucionar el sistema sin generar costos innecesarios.”

La idea clave para la entrevista: diseñar una arquitectura empresarial consiste en equilibrar las necesidades del negocio, la calidad técnica y la complejidad operativa. Un buen Líder Técnico no solo define cómo se construye el sistema, sino también por qué se toman determinadas decisiones y cómo se mantendrá y evolucionará en el tiempo.