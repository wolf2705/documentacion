
- [Security Considerations](#security-considerations)

---

# Security Considerations

1. Background/Context
Desde la perspectiva de un Technical Lead, la seguridad no es un "complemento" que se añade al final, sino un pilar arquitectónico. No se trata solo de saber implementar un login, sino de elegir la estrategia adecuada basándose en el balance entre **seguridad, escalabilidad y experiencia de usuario (UX)**. Un líder debe ser capaz de decidir, por ejemplo, cuándo utilizar un enfoque *stateful* (basado en sesiones) frente a uno *stateless* (JWT), evaluar los riesgos de almacenamiento de tokens en el cliente (XSS vs CSRF) y diseñar sistemas de identidad que permitan la interoperabilidad mediante estándares industriales para evitar el "reinventar la rueda".

2. Technical Answer
A continuación, la explicación técnica de cada punto orientada a la toma de decisiones arquitectónicas:

*   **Autenticación y Autorización:** Son conceptos distintos pero complementarios. La **Autenticación (AuthN)** es el proceso de validar la identidad del usuario ("¿Quién eres?"), generalmente mediante credenciales, MFA o biometría. La **Autorización (AuthZ)** es el proceso de determinar qué permisos tiene ese usuario ya identificado ("¿Qué tienes permitido hacer?"). Como líder, debes definir si usarás **RBAC** (Role-Based Access Control), basado en roles como "Admin" o "User", o **ABAC** (Attribute-Based Access Control), que es más granular y depende de atributos (ej. "Solo puede editar si es el dueño del documento y es horario laboral").

*  **Session y Cookies:** Es el enfoque *stateful* tradicional. El servidor crea una sesión y guarda un ID en una cookie en el navegador del cliente. 
    *   *Ventaja:* El servidor tiene control total y puede revocar la sesión instantáneamente.
    *   *Desafío de escala:* En microservicios, requiere un almacenamiento compartido (como Redis) para que cualquier instancia del servidor reconozca la sesión, o el uso de *sticky sessions* en el balanceador de carga, lo cual complica el escalado horizontal.

*   **JWT (JSON Web Token):** Es el estándar para la comunicación *stateless*. El token contiene toda la información necesaria (claims) firmada digitalmente por el servidor.
    *   *Ventaja:* El servidor no necesita consultar una base de datos o caché para validar al usuario; solo verifica la firma. Ideal para arquitecturas de microservicios.
    *   *Riesgo:* Son difíciles de revocar antes de que expiren. Si un token es robado, es válido hasta su fecha de vencimiento a menos que implementes una "lista negra" (blacklist) en Redis, lo cual vuelve a introducir estado en el sistema.

*   **Access Tokens y Refresh Tokens:** Estrategia para mitigar los riesgos de los JWT.
    *   **Access Token:** Tiene vida corta (ej. 15 minutos). Se usa para acceder a los recursos. Si se filtra, el daño es limitado en tiempo.
    *   **Refresh Token:** Tiene vida larga (ej. 7 días) y se usa exclusivamente para solicitar un nuevo Access Token sin que el usuario tenga que loguearse de nuevo. Se almacena de forma más segura y puede ser revocado por el servidor para cerrar la sesión del usuario remotamente.

*   **SSO (Single Sign-On):** Arquitectura que permite al usuario autenticarse una sola vez y obtener acceso a múltiples aplicaciones independientes. Centraliza la gestión de identidades en un **Identity Provider (IdP)**. Reduce la fricción del usuario y centraliza la seguridad y el cumplimiento (compliance) de las políticas de contraseñas y MFA.

*   **OAuth 2.0:** No es un protocolo de autenticación, sino un **framework de autorización**. Permite que una aplicación acceda a recursos de otra aplicación en nombre del usuario sin conocer su contraseña. Se basa en el concepto de **Scopes** (alcances), donde el usuario autoriza específicamente qué puede hacer la aplicación (ej. "esta app solo puede leer mi calendario, no enviar correos"). Sus flujos más comunes son el *Authorization Code Flow* (para apps web) y el *Client Credentials Flow* (para comunicación M2M entre microservicios).

*   **OpenID Connect (OIDC):** Es una capa de identidad construida **sobre OAuth 2.0**. Mientras que OAuth 2.0 dice "tienes permiso para acceder a esto", OIDC añade la respuesta a "¿quién es el usuario?". Introduce el **ID Token** (un JWT) que contiene información del perfil del usuario y el endpoint `/userinfo`. Es el estándar moderno que utilizan Google, Microsoft (Azure AD) y Auth0 para implementar el login social y empresarial.
