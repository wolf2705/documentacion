- [OpenID Connect](#openid-connect)
  - [1. ¿Cómo funciona OpenID Connect?](#1-cómo-funciona-openid-connect)
  - [2. ¿Qué tipos de tokens utiliza?](#2-qué-tipos-de-tokens-utiliza)
  - [3. ¿Cuál es la diferencia entre OAuth 2.0 y OIDC?](#3-cuál-es-la-diferencia-entre-oauth-20-y-oidc)
  - [4. ¿Cómo lo implementaría en .NET Core?](#4-cómo-lo-implementaría-en-net-core)
  - [5. ¿Qué buenas prácticas aplicaría como líder técnico?](#5-qué-buenas-prácticas-aplicaría-como-líder-técnico)
  - [6. ¿Cómo se relacionan los tres conceptos?](#6-cómo-se-relacionan-los-tres-conceptos)
    - [OpenID Connect](#openid-connect-1)
    - [OAuth 2.0](#oauth-20)
    - [JWT](#jwt)

---

# OpenID Connect

“OpenID Connect, conocido como OIDC, es un protocolo de autenticación construido sobre OAuth 2.0 que permite verificar la identidad de un usuario y obtener información básica sobre él de forma estandarizada.

En un proyecto con .NET Core, lo utilizaría cuando necesitamos que los usuarios inicien sesión de manera segura, centralizar la gestión de identidades y permitir que diferentes aplicaciones compartan un sistema de autenticación.

## 1. ¿Cómo funciona OpenID Connect?

Imaginemos que tenemos una aplicación empresarial desarrollada en ASP.NET Core y queremos que los empleados inicien sesión con sus cuentas corporativas.

El flujo sería el siguiente:

1. El usuario intenta acceder a la aplicación.
2. La aplicación lo redirige al proveedor de identidad, por ejemplo, Microsoft Entra ID.
3. El usuario se autentica con sus credenciales y, si está configurado, completa la autenticación multifactor.
4. El proveedor devuelve un código de autorización a la aplicación.
5. La aplicación intercambia ese código por los tokens correspondientes.
6. La aplicación recibe un ID Token, que contiene información sobre la identidad autenticada, y puede recibir también un access token para acceder a APIs.
7. La aplicación valida la respuesta y establece la sesión del usuario.

Así, la aplicación no necesita gestionar directamente las contraseñas de los usuarios.

## 2. ¿Qué tipos de tokens utiliza?

Los principales son:

- ID Token: contiene información sobre la autenticación y la identidad del usuario, como su identificador único (`sub`). Normalmente utiliza el formato JWT.
- Access Token: permite acceder a una API según los permisos concedidos. Puede ser un JWT o tener otro formato.
- Refresh Token: cuando está permitido por el flujo y el proveedor, permite solicitar nuevos tokens sin repetir todo el proceso de autenticación.

Es importante no confundirlos: el ID Token está destinado a la aplicación cliente, mientras que el access token está destinado a la API protegida. No utilizaría un ID Token como sustituto de un access token para autorizar llamadas a una API.

## 3. ¿Cuál es la diferencia entre OAuth 2.0 y OIDC?

OAuth 2.0 está enfocado en la autorización: permite conceder acceso a recursos.

OIDC añade una capa de identidad y autenticación sobre OAuth 2.0. Define cómo la aplicación puede comprobar quién inició sesión y obtener información estandarizada sobre ese usuario.

Por ejemplo:

- Si una aplicación necesita consultar los pedidos de un usuario mediante una API, utilizaría OAuth 2.0 para gestionar el acceso.
- Si necesito que ese usuario inicie sesión en una aplicación empresarial, utilizaría OpenID Connect.
- Si necesito ambas cosas, utilizaría OIDC para la autenticación y los access tokens de OAuth 2.0 para acceder a las APIs.

## 4. ¿Cómo lo implementaría en .NET Core?

En una aplicación web ASP.NET Core utilizaría el middleware de autenticación OpenID Connect y una biblioteca mantenida, en lugar de implementar el protocolo manualmente.

Un ejemplo simplificado sería:

csharp
```csharp
builder.Services
    .AddAuthentication(options =>
    {
        options.DefaultScheme = "Cookies";
        options.DefaultChallengeScheme = "oidc";
    })
    .AddCookie("Cookies")
    .AddOpenIdConnect("oidc", options =>
    {
        options.Authority =
            builder.Configuration["Auth:Authority"];

        options.ClientId =
            builder.Configuration["Auth:ClientId"];

        options.ClientSecret =
            builder.Configuration["Auth:ClientSecret"];

        options.ResponseType = "code";
        options.UsePkce = true;

        options.Scope.Add("openid");
        options.Scope.Add("profile");
    });

var app = builder.Build();

app.UseAuthentication();
app.UseAuthorization();

app.MapDefaultControllerRoute();

app.Run();
```

Este ejemplo requiere configurar el registro de la aplicación en el proveedor de identidad, incluyendo el URI de redirección y las credenciales correspondientes. En producción protegería el secreto mediante un gestor de secretos y ajustaría los scopes, la sesión y el resto de las opciones según el tipo de aplicación.

Para una Web API, en cambio, normalmente configuraría JWT Bearer para validar los access tokens emitidos por el proveedor, en lugar de utilizar el middleware OIDC como si la API fuera una aplicación interactiva.

## 5. ¿Qué buenas prácticas aplicaría como líder técnico?

- Utilizaría Authorization Code con PKCE para los clientes compatibles.
- Validaría correctamente la firma, el emisor, la audiencia, los tiempos y los demás parámetros requeridos del ID Token.
- No confiaría en claims recibidos sin validar su procedencia.
- Aplicaría autorización mediante políticas y permisos en el backend.
- Protegería las sesiones, los secretos y los tokens.
- Centralizaría la identidad en un proveedor confiable y evitaría duplicar la lógica de autenticación en cada microservicio.
- Consideraría la autenticación multifactor y las políticas de acceso condicional cuando los requisitos de seguridad lo justifiquen.

## 6. ¿Cómo se relacionan los tres conceptos?

### OpenID Connect

¿Quién es el usuario?

Autenticación e identidad

### OAuth 2.0

¿A qué recursos puede acceder?

Autorización delegada

### JWT

¿Cómo se representa la información del token?

Formato de token, cuando se utiliza

En conclusión, como líder técnico elegiría OpenID Connect para resolver la autenticación de usuarios de forma estandarizada y OAuth 2.0 para gestionar el acceso a recursos protegidos. En .NET Core integraría ambos mediante bibliotecas consolidadas y un proveedor de identidad, manteniendo separadas la autenticación, la autorización y la lógica de negocio.”