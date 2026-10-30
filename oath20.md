- [OAuth 2.0](#oauth-20)
  - [1. ¿Cómo funciona OAuth 2.0?](#1-cómo-funciona-oauth-20)
  - [2. ¿Cuáles son los principales componentes?](#2-cuáles-son-los-principales-componentes)
  - [3. ¿Qué tipos de flujo existen?](#3-qué-tipos-de-flujo-existen)
  - [4. ¿Cómo lo implementaría en .NET Core?](#4-cómo-lo-implementaría-en-net-core)
  - [5. ¿Cuál es la diferencia entre OAuth 2.0, JWT y OpenID Connect?](#5-cuál-es-la-diferencia-entre-oauth-20-jwt-y-openid-connect)
  - [6. ¿Qué decisiones tomaría como líder técnico?](#6-qué-decisiones-tomaría-como-líder-técnico)


# OAuth 2.0

“OAuth 2.0 es un protocolo de autorización que permite que una aplicación acceda a determinados recursos de un usuario sin necesidad de que la aplicación conozca o almacene directamente su contraseña.

En el contexto de .NET Core, lo utilizaría para proteger APIs, delegar el acceso a recursos y permitir que aplicaciones web, móviles o servicios se comuniquen de forma segura.

## 1. ¿Cómo funciona OAuth 2.0?

Pensemos en una aplicación que necesita consultar información de un usuario mediante una API. En lugar de compartir sus credenciales con esa aplicación, se utiliza un servidor de autorización que emite un token con permisos específicos.

El flujo general es el siguiente:

1. El usuario intenta acceder a una aplicación.
2. La aplicación solicita autorización a un servidor de autorización.
3. El usuario concede su consentimiento cuando corresponde.
4. El servidor de autorización emite un access token.
5. La aplicación envía ese token a la API en la cabecera `Authorization: Bearer`.
6. La API valida el token y comprueba que tenga los permisos necesarios antes de permitir el acceso.

Por ejemplo, una aplicación podría tener permiso para consultar el calendario de un usuario, pero no para eliminar sus eventos.

## 2. ¿Cuáles son los principales componentes?

- Resource Owner: normalmente, el usuario propietario de los datos.
- Client: la aplicación que solicita acceso a los recursos.
- Authorization Server: el servidor que autentica al usuario cuando corresponde y emite los tokens.
- Resource Server: la API que protege los recursos y valida los tokens.
- Access Token: la credencial que permite acceder a los recursos dentro de los permisos concedidos.
- Scope: define el alcance de los permisos solicitados, por ejemplo, `read:orders`.

## 3. ¿Qué tipos de flujo existen?

OAuth 2.0 define diferentes mecanismos de autorización. Los más relevantes son:

- Authorization Code con PKCE: lo recomendaría para aplicaciones web, móviles y clientes públicos. PKCE ayuda a proteger el código de autorización frente a su interceptación.
- Client Credentials: lo utilizaría para comunicación entre servicios que actúan en nombre propio, sin un usuario interactivo.
- Refresh Token: permite obtener nuevos access tokens cuando se cumplen las condiciones de renovación establecidas por el servidor de autorización.

Evitaría el flujo Implicit y el flujo Resource Owner Password Credentials en nuevos desarrollos, ya que existen alternativas más seguras.

## 4. ¿Cómo lo implementaría en .NET Core?

Como líder técnico, evitaría implementar manualmente todo el protocolo si existe un proveedor de identidad confiable. Por ejemplo, podría utilizar Microsoft Entra ID u otro proveedor compatible con OAuth 2.0 y OpenID Connect.

En una API ASP.NET Core, una configuración simplificada con JWT Bearer podría ser:

csharp
```csharp
builder.Services
    .AddAuthentication(
        JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(options =>
    {
        options.Authority =
            builder.Configuration["Auth:Authority"];

        options.Audience =
            builder.Configuration["Auth:Audience"];

        options.RequireHttpsMetadata = true;
    });

builder.Services.AddAuthorization();

var app = builder.Build();

app.UseAuthentication();
app.UseAuthorization();

app.MapControllers();

app.Run();
```

Esta configuración presupone que el proveedor emite tokens compatibles con JWT y que la autoridad y la audiencia están configuradas correctamente. También verificaría que el proveedor y la biblioteca validen los permisos requeridos por cada endpoint.

Por ejemplo, un endpoint podría exigir un permiso específico mediante una política de autorización.

## 5. ¿Cuál es la diferencia entre OAuth 2.0, JWT y OpenID Connect?

Esta es una pregunta frecuente en entrevistas técnicas.

| Concepto              | Función principal                                             |
| --------------------- | ------------------------------------------------------------- |
| OAuth 2.0             | Delegar autorización y acceso a recursos.                     |
| JWT                   | Formato de token que transporta claims y puede estar firmado. |
| OpenID Connect (OIDC) | Añade una capa de identidad y autenticación sobre OAuth 2.0.  |

Un detalle importante: OAuth 2.0 no exige que todos los access tokens sean JWT. También pueden ser tokens opacos que la API valida mediante un mecanismo de introspección.

Si el objetivo es iniciar sesión y conocer la identidad del usuario, utilizaría OpenID Connect. Si el objetivo es conceder acceso a una API, utilizaría OAuth 2.0.

## 6. ¿Qué decisiones tomaría como líder técnico?

En un proyecto real evaluaría:

- Centralizar la autenticación en un proveedor de identidad.
- Aplicar el principio de mínimo privilegio mediante scopes y políticas.
- Usar HTTPS, proteger los secretos y limitar la duración de los tokens.
- Separar autenticación y autorización dentro de la arquitectura.
- Definir cómo gestionar la renovación, revocación y rotación de credenciales.
- Implementar pruebas de seguridad y verificar que cada API rechace tokens inválidos o insuficientes.

En resumen, OAuth 2.0 permite delegar el acceso seguro a recursos, mientras que JWT es un posible formato de token y OpenID Connect añade capacidades estandarizadas de autenticación e identidad. En una arquitectura .NET Core, esta distinción me permite diseñar una solución segura, escalable y fácil de mantener.”