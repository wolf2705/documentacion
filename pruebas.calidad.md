
- [1. API Testing (Postman)](#1-api-testing-postman)
- [1. Pruebas funcionales](#1-pruebas-funcionales)
- [1. Pruebas de integración](#1-pruebas-de-integración)
- [4. Performance Testing](#4-performance-testing)
- [5. Contract Testing](#5-contract-testing)



> Podrias dar una explicacion de cada uno de estos conceptos?
>
> - API Testing (Postman)
> - Pruebas funcionales
> - Pruebas de integración
> - Performance Testing
> - Contract Testing

Claro. Estos conceptos están muy relacionados con QA (Quality Assurance) y las pruebas de software, pero cada uno responde a una pregunta diferente. Te los explico de forma práctica y con ejemplos.

## 1. API Testing (Postman)

API Testing consiste en probar directamente las APIs de una aplicación para verificar que funcionan correctamente.

Una API permite que diferentes sistemas o componentes se comuniquen entre sí. Por ejemplo:

```text
Frontend → API → Backend → Base de datos
```

Con herramientas como Postman puedes enviar peticiones HTTP y verificar las respuestas.

**¿Qué se prueba?**

Por ejemplo, una API tiene:

```text
POST /api/users
```

Y recibe:

```text
{
  "name": "Juan",
  "email": "juan@example.com"
}
```

Esperamos una respuesta:

```text
201 Created
```

```
{
  "id": 123,
  "name": "Juan",
  "email": "juan@example.com"
}
```

En API Testing podrías validar:

- Código HTTP (`200`, `201`, `400`, `401`, `404`, `500`, etc.).
- Estructura del JSON.
- Datos retornados.
- Headers.
- Autenticación y autorización.
- Validaciones.
- Manejo de errores.
- Tiempo de respuesta.
- Diferentes escenarios de entrada.

**Ejemplo de prueba**

Caso: crear un usuario sin email.

```
{
  "name": "Juan"
}
```

Esperaríamos algo como:

```
400 Bad Request
```

y un mensaje indicando que el email es obligatorio.

En resumen: API Testing verifica que los servicios/backend respondan correctamente ante diferentes solicitudes.

## 1. Pruebas funcionales

Las pruebas funcionales verifican que el sistema haga lo que se supone que debe hacer según los requisitos.

La pregunta principal es:

> ¿La funcionalidad funciona correctamente desde el punto de vista del negocio?

Por ejemplo, tenemos una aplicación de e-commerce con un botón:

**"Agregar al carrito"**

Una prueba funcional podría ser:

1. Ingresar a la tienda.
1. Seleccionar un producto.
1. Hacer clic en "Agregar al carrito".
1. Ir al carrito.
1. Verificar que el producto aparezca.
1. Verificar cantidad y precio.
1. También se prueban casos negativos

Por ejemplo:

- Intentar comprar sin iniciar sesión.
- Introducir una tarjeta inválida.
- Dejar campos obligatorios vacíos.
- Introducir una cantidad negativa.
- Utilizar un cupón expirado.

Ejemplo

Supongamos que el requisito dice:

> "Un usuario puede retirar dinero siempre que tenga saldo suficiente."

Podríamos tener:

| Caso | Saldo  | Retiro | Resultado esperado |
| ---- | ------ | ------ | ------------------ |
| 1    | $1,000 | $500   | Retiro exitoso     |
| 2    | $1,000 | $1,000 | Retiro exitoso     |
| 3    | $1,000 | $1,500 | Error              |
| 4    | $0     | $100   | Error              |

En resumen: las pruebas funcionales verifican que las funcionalidades cumplan los requisitos.

## 1. Pruebas de integración

Las pruebas de integración verifican que diferentes componentes del sistema funcionen correctamente cuando interactúan entre sí.

Aquí la pregunta es:

> ¿Los diferentes componentes se comunican correctamente?

Por ejemplo, imaginemos un sistema de compras:

```
Frontend
↓
Order API
↓
Payment Service
↓
Database
```

Cada componente podría funcionar correctamente por separado, pero eso no garantiza que funcionen correctamente juntos.

Ejemplo

Un usuario realiza una compra.

El sistema debería:

```
1. Crear orden
   ↓
2. Procesar pago
   ↓
3. Guardar pago
   ↓
4. Actualizar orden
   ↓
5. Enviar confirmación
```

Una prueba de integración podría verificar que:

> Cuando Order Service crea una orden, se comunica correctamente con Payment Service y la orden queda actualizada después de un pago exitoso.

**¿Qué problemas puede encontrar?**

Por ejemplo:

- Un servicio espera userId, pero otro envía user_id.
- Un servicio espera un string, pero recibe un integer.
- Un endpoint cambió y otro servicio sigue utilizando la versión anterior.
- La base de datos no recibe correctamente la información.
- Un servicio devuelve 200, pero otro espera 201.
- Los datos se transforman incorrectamente entre servicios.

En resumen: las pruebas de integración verifican que las piezas del sistema trabajen correctamente entre ellas.

## 4. Performance Testing

El Performance Testing evalúa cómo se comporta una aplicación bajo diferentes niveles de carga.

La pregunta principal es:

> ¿El sistema sigue funcionando correctamente cuando tiene usuarios, solicitudes o datos en cantidad?

Por ejemplo, tenemos:

```
10 usuarios
100 usuarios
1,000 usuarios
10,000 usuarios
```

Queremos saber cómo responde el sistema.

Se pueden medir cosas como:

- Tiempo de respuesta.
- Número de solicitudes por segundo.
- Uso de CPU.
- Uso de memoria.
- Errores.
- Capacidad máxima.
- Estabilidad.

Ejemplo

Tenemos:

```
GET /api/products
```

Con 10 usuarios:

```
Response time: 150 ms
Errors: 0%
```

Con 1,000 usuarios:

```
Response time: 2,500 ms
Errors: 8%
```

Eso podría indicar que el sistema comienza a tener problemas bajo carga.

**Algunos tipos comunes**

**Load Testing**

Prueba el sistema con una carga esperada.

> "¿Puede soportar 5,000 usuarios simultáneos?"

**Stress Testing**

Lleva el sistema más allá de su capacidad normal.

> "¿Qué pasa si tenemos 20,000 usuarios?"

**Spike Testing**

Aumenta la carga rápidamente.

```
100 usuarios
↓
↓
10,000 usuarios
```

> "¿Qué pasa si de repente recibimos muchísimo tráfico?"

**Endurance Testing**

Mantiene una carga durante mucho tiempo.

> "¿Puede funcionar correctamente durante 12 horas?"

Herramientas comunes para esto incluyen JMeter, k6 y Gatling.

En resumen: Performance Testing verifica velocidad, estabilidad, capacidad y comportamiento bajo carga.

## 5. Contract Testing

Este concepto es especialmente importante en arquitecturas con microservicios.

Contract Testing verifica que dos sistemas que se comunican entre sí respeten un contrato previamente acordado.

La pregunta principal es:

> ¿El proveedor de una API sigue entregando lo que el consumidor espera?

Por ejemplo:

Tenemos:

```
Frontend / Consumer
↓
User API
↓
Database
```

El consumidor espera que la API responda:

```
{
"id": 123,
"name": "Juan",
"email": "juan@example.com"
}
```

Ese formato forma parte del contrato.

Pero imaginemos que el equipo del backend cambia la respuesta a:

```
{
"userId": 123,
"fullName": "Juan",
"emailAddress": "juan@example.com"
}
```

La API podría estar funcionando perfectamente desde el punto de vista del backend.

Sin embargo, el consumidor puede romperse, porque esperaba:

```
id
name
email
```

y ahora recibe:

```
userId
fullName
emailAddress
```

Contract Testing permite detectar este problema antes de llegar a producción.

¿Qué verifica?

Por ejemplo:

Request esperado:

```
GET /users/123
```

Response esperada:

```
{
"id": 123,
"name": "Juan",
"email": "juan@example.com"
}
```

El contrato puede definir:

- Endpoint.
- Método HTTP.
- Headers.
- Parámetros.
- Campos obligatorios.
- Tipos de datos.
- Estructura de la respuesta.
- Códigos HTTP esperados.

Una herramienta muy conocida para esto es Pact.

En resumen: Contract Testing verifica que dos sistemas mantengan el acuerdo sobre cómo se comunican.

¿Cuál es la diferencia entre todos?

Una forma sencilla de recordarlos es pensar en qué pregunta responde cada prueba:

| Tipo                   | Pregunta que responde                               |
| ---------------------- | --------------------------------------------------- |
| API Testing            | ¿La API responde correctamente?                     |
| Pruebas funcionales    | ¿La funcionalidad hace lo que debería?              |
| Pruebas de integración | ¿Los componentes funcionan correctamente juntos?    |
| Performance Testing    | ¿El sistema funciona bien bajo carga?               |
| Contract Testing       | ¿Los sistemas respetan el contrato de comunicación? |

Y visualmente:

```
                    SISTEMA
                       │
        ┌──────────────┼──────────────┐
        │              │              │
        ▼              ▼              ▼
   Funcional      Integración     Performance
        │              │              │
   "¿Funciona?"   "¿Se conectan?"  "¿Aguanta?"
                       │
                       ▼
                Contract Testing
                "¿Respetan el
                contrato?"
                      │
                      ▼
                API Testing
                "¿La API responde
                correctamente?"
```

Un ejemplo completo

Imagina una API de login:

```
POST /login
```

API Testing

Compruebas:

```
POST /login
→ 200 OK
→ token recibido
→ estructura JSON correcta
```

Prueba funcional

Compruebas:

```
Usuario + contraseña correctos
→ usuario puede iniciar sesión
```

Y:

```
Contraseña incorrecta
→ aparece mensaje de error
```

Prueba de integración

Compruebas:

```
Login API
↓
Auth Service
↓
User Database
↓
Token Service
```

Todos deben funcionar correctamente juntos.

Performance Testing

Compruebas:

```
1,000 usuarios
→ ¿Cuánto tarda /login?
→ ¿Cuántos errores aparecen?
→ ¿El servidor soporta la carga?
```

Contract Testing

Compruebas que la API siga entregando algo compatible con lo que espera el consumidor:

```
{
"token": "...",
"expiresIn": 3600
}
```

Si el backend cambia expiresIn por expiration, el Contract Test podría detectar el cambio antes de que afecte al consumidor.

Una regla fácil para una entrevista de QA

Si te preguntan la diferencia, puedes decir:

> **Functional Testing** valida que el negocio funcione correctamente.
> **API Testing** valida directamente los endpoints y sus respuestas.
> **Integration Testing** valida la comunicación entre componentes.
> **Performance Testing** valida el comportamiento bajo carga.
> **Contract Testing** valida que los sistemas que se comunican mantengan el contrato acordado.

Esa distinción es bastante buena para empezar a prepararte para una entrevista de **QA Automation / API Testing**.
