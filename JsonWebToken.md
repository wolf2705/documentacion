
- [JSON Web Token](#json-web-token)
  - [1. ¿Cómo funciona JWT?](#1-cómo-funciona-jwt)
  - [2. ¿Qué partes componen un JWT?](#2-qué-partes-componen-un-jwt)
  - [3. ¿Cómo lo implementaría en .NET Core?](#3-cómo-lo-implementaría-en-net-core)
  - [4. ¿Qué buenas prácticas aplicaría como líder técnico?](#4-qué-buenas-prácticas-aplicaría-como-líder-técnico)
  - [5. ¿Cuál es la diferencia entre autenticación y autorización?](#5-cuál-es-la-diferencia-entre-autenticación-y-autorización)

---

# JSON Web Token

“JWT, que significa JSON Web Token, es un estándar que permite transmitir información entre dos partes de forma segura mediante un token firmado digitalmente. En el desarrollo con .NET Core se utiliza frecuentemente para implementar autenticación y autorización en aplicaciones Web API.

Yo lo explicaría en tres partes principales:

## 1. ¿Cómo funciona JWT?

Cuando un usuario inicia sesión, el backend valida sus credenciales. Si son correctas, genera un token JWT y se lo devuelve al cliente. El cliente lo envía en las siguientes peticiones HTTP, normalmente en la cabecera `Authorization`, con el esquema `Bearer`.

El flujo sería el siguiente:

1. El usuario envía sus credenciales al endpoint de login.
2. La API valida las credenciales contra la fuente de usuarios.
3. Si son válidas, genera un JWT firmado.
4. El cliente almacena el token y lo envía en cada petición protegida.
5. El middleware de autenticación de ASP.NET Core valida la firma, la expiración y las demás condiciones configuradas.
6. Si el token es válido, la API identifica al usuario y evalúa sus permisos antes de ejecutar la operación.

## 2. ¿Qué partes componen un JWT?

Un JWT tiene tres secciones separadas por puntos:

`Header.Payload.Signature`

- Header: indica el tipo de token y el algoritmo de firma.
- Payload: contiene los claims, es decir, los datos o atributos asociados al usuario, como su identificador, rol o fecha de expiración.
- Signature: permite verificar que el token no fue alterado y que fue firmado con la clave correspondiente.

Por ejemplo, el payload podría contener el identificador del usuario, su rol y los tiempos de emisión y expiración.

Algo importante que destacaría como líder técnico es que un JWT firmado no está necesariamente cifrado. Su contenido normalmente puede decodificarse, por lo que nunca incluiría contraseñas, secretos ni información sensible en el payload.

</br>

1. Background/Context
Para un desarrollador o arquitecto, entender la anatomía de un JWT es crucial para garantizar la seguridad de la aplicación. Un error común es pensar que el JWT "oculta" la información; en realidad, el JWT **protege la integridad**, no la confidencialidad. El contenido es legible por cualquier persona, pero es imposible de modificar sin invalidar la firma.

2. Technical Answer
Un JWT se compone de tres secciones separadas por puntos (`.`), cada una codificada en **Base64Url**. Aquí el detalle de cada una:

**1. Header (Cabecera)**
Es la parte más pequeña y define los parámetros técnicos del token. Normalmente contiene dos campos:
*   **`alg` (Algoritmo):** El algoritmo de cifrado usado para crear la firma. Los más comunes son **HS256** (HMAC con SHA-256, usa una clave secreta compartida) o **RS256** (RSA con SHA-256, usa un par de claves pública/privada).
*   **`typ` (Tipo):** Indica que el objeto es un **JWT**.

*Ejemplo decodificado:*
```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

**2. Payload (Cuerpo / Claims)**
Es la parte central que contiene los **Claims** (declaraciones), que son piezas de información sobre el usuario y el token mismo. Se dividen en:
*   **Registered Claims:** Campos estándar predefinidos para interoperabilidad:
    *   `iss` (Issuer): Quién emitió el token.
    *   `exp` (Expiration Time): Cuándo expira el token (crucial para la seguridad).
    *   `sub` (Subject): El ID único del usuario.
    *   `iat` (Issued At): Cuándo fue creado.
*   **Public/Private Claims:** Información personalizada añadida por el desarrollador, como el rol del usuario o su nombre.

*Ejemplo decodificado:*
```json
{
  "sub": "1234567890",
  "name": "John Doe",
  "role": "admin",
  "exp": 1730000000
}
```
**⚠️ Nota Crítica:** Como el Payload está solo en Base64 (no cifrado), **nunca** se deben guardar contraseñas, números de tarjeta de crédito o datos sensibles aquí.

**3. Signature (Firma)**
Es la parte que garantiza que el token no ha sido manipulado. Se genera siguiendo este proceso:
1. Se toma el **Header** codificado en Base64.
2. Se toma el **Payload** codificado en Base64.
3. Se unen ambos con un punto.
4. Se pasa ese string por el algoritmo definido en el Header usando una **Clave Secreta** que solo el servidor conoce.

*Fórmula conceptual:*
`HMACSHA256( base64UrlEncode(header) + "." + base64UrlEncode(payload), secret_key )`

**¿Cómo funciona la validación?**
Cuando el servidor recibe el token, repite el proceso de firma usando el Header y Payload recibidos y su propia clave secreta. Si el resultado es idéntico a la **Signature** que viene en el token, el servidor sabe que el token es auténtico. Si un atacante cambia el `role` de "user" a "admin" en el Payload, la firma ya no coincidirá y el servidor rechazará la petición inmediatamente.




## 3. ¿Cómo lo implementaría en .NET Core?

En ASP.NET Core utilizaría el middleware de autenticación JWT Bearer, configurando la validación del emisor, la audiencia, la expiración y la firma.

Un ejemplo simplificado en C# sería:
csharp
```csharp
builder.Services
    .AddAuthentication(
        JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(options =>
    {
        options.TokenValidationParameters =
            new TokenValidationParameters
            {
                ValidateIssuer = true,
                ValidIssuer = builder.Configuration[
                    "Jwt:Issuer"],

                ValidateAudience = true,
                ValidAudience = builder.Configuration[
                    "Jwt:Audience"],

                ValidateIssuerSigningKey = true,
                IssuerSigningKey = new SymmetricSecurityKey(
                    Encoding.UTF8.GetBytes(
                        builder.Configuration[
                            "Jwt:SecretKey"]!)),

                ValidateLifetime = true,
                ClockSkew = TimeSpan.FromSeconds(30)
            };
    });

builder.Services.AddAuthorization();

var app = builder.Build();

app.UseAuthentication();
app.UseAuthorization();

app.MapControllers();

app.Run();
```

En este ejemplo, la clave se obtiene de la configuración, pero en producción la almacenaría en un gestor seguro de secretos, como Azure Key Vault, y utilizaría una clave robusta. En escenarios con varios servicios, evaluaría el uso de claves asimétricas y un proveedor de identidad centralizado.

Para proteger un endpoint, podría utilizar:

csharp
```csharp
[Authorize]
[HttpGet("profile")]
public IActionResult GetProfile()
{
    return Ok();
}
```

Y para restringirlo por rol:

csharp
```csharp
[Authorize(Roles = "Admin")]
[HttpDelete("{id}")]
public IActionResult Delete(int id)
{
    return NoContent();
}
```

## 4. ¿Qué buenas prácticas aplicaría como líder técnico?

- Tokens de corta duración: reduciría el impacto de un token comprometido.
- Refresh tokens: los utilizaría cuando sea necesario renovar sesiones, con rotación y revocación controladas.
- HTTPS obligatorio: para proteger el token durante el transporte.
- Validación completa: verificaría firma, emisor, audiencia y expiración; no confiaría únicamente en decodificar el token.
- Principio de mínimo privilegio: asignaría únicamente los permisos necesarios y evitaría depender exclusivamente de roles cuando se requieran políticas más específicas.
- Almacenamiento seguro: en aplicaciones web evaluaría cuidadosamente cookies `HttpOnly`, `Secure` y `SameSite`, o el almacenamiento apropiado según el tipo de cliente, evitando exponer tokens a JavaScript cuando sea posible.
- Revocación: tendría presente que un JWT autocontenido normalmente sigue siendo válido hasta que expire, salvo que se implemente un mecanismo adicional de revocación.

## 5. ¿Cuál es la diferencia entre autenticación y autorización?

La autenticación responde a la pregunta: ¿quién eres?

La autorización responde a: ¿qué tienes permitido hacer?

JWT puede transportar información utilizada en ambos procesos, pero el hecho de que un token sea válido no significa que el usuario tenga permiso para ejecutar cualquier operación.

En conclusión, como líder técnico utilizaría JWT cuando necesitamos una autenticación basada en tokens, especialmente en APIs REST y arquitecturas distribuidas. Sin embargo, no lo elegiría automáticamente para todos los sistemas: evaluaría si conviene usar un proveedor de identidad como Microsoft Entra ID u OpenID Connect, cómo gestionar la renovación y revocación de tokens y cuáles son los requisitos de seguridad de la aplicación.”