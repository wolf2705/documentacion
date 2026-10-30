
- [Test Driven Development](#test-driven-development)
- [Acceptance Test Driven Development](#acceptance-test-driven-development)
- [Diferencias entre Acceptance Test Driven Development y Test Driven Development](#diferencias-entre-acceptance-test-driven-development-y-test-driven-development)
- [Otras Metodologías](#otras-metodologías)
    - [1. Metodologías de Diseño y Desarrollo de Código](#1-metodologías-de-diseño-y-desarrollo-de-código)
    - [2. Metodologías de Arquitectura](#2-metodologías-de-arquitectura)
    - [3. Metodologías de Gestión y Flujo de Trabajo](#3-metodologías-de-gestión-y-flujo-de-trabajo)


# Test Driven Development

>Que son las metodologias Test Driven Development

**1. Antecedentes**
Tradicionalmente, el desarrollo de software seguía un modelo lineal donde las pruebas se realizaban al final del ciclo de vida (después de escribir todo el código). Este enfoque generaba un problema crítico: si se descubría un error arquitectónico fundamental durante la fase de pruebas, corregirlo implicaba reescribir grandes porciones de código ya terminado, lo que elevaba los costos y los tiempos de entrega. El TDD surge como una inversión de este proceso, proponiendo que la prueba no sea la verificación final, sino la guía del diseño.

**2. Respuesta técnica**
El **Test Driven Development (TDD)** es una metodología de desarrollo basada en ciclos cortos de retroalimentación llamados **"Ciclos Red-Green-Refactor"**. En lugar de escribir la implementación primero, el desarrollador sigue estrictamente estos tres pasos:

*   **Red (Rojo):** Se escribe una prueba unitaria mínima que define una funcionalidad específica. Al ejecutarla, la prueba **debe fallar**, ya que el código de producción aún no existe. Esto valida que la prueba es correcta y que realmente está probando algo que falta.
*   **Green (Verde):** Se escribe la cantidad mínima de código necesaria para que la prueba pase. No se busca la perfección ni la optimización en este paso, sino simplemente cumplir con el contrato definido por la prueba.
*   **Refactor (Refactorizar):** Una vez que la prueba es exitosa, se limpia el código. Se eliminan duplicados, se mejora la nomenclatura y se optimiza la estructura, con la seguridad total de que la prueba (ya escrita) detectará cualquier regresión si algo se rompe.

Desde la perspectiva de un líder técnico, el TDD no es solo una técnica de pruebas, sino una **herramienta de diseño**. Obliga al desarrollador a pensar en el "qué" (el contrato y la interfaz) antes que en el "cómo" (la implementación), lo que resulta en un código más modular, desacoplado y con una cobertura de pruebas intrínseca, facilitando enormemente la integración continua (CI) y la entrega continua (CD).

---
>Como podriamos ejemplificar la implementacion de TTD?

**1. Antecedentes**
Para ejemplificar TDD, es necesario plantear un escenario de negocio simple pero con reglas claras. Imaginemos que necesitamos desarrollar un módulo de **Validación de Contraseñas**. La regla de negocio es: *"La contraseña debe tener al menos 8 caracteres"*. 

Si lo hiciéramos de forma tradicional, escribiríamos la clase `PasswordValidator`, el método `IsValid`, y luego intentaríamos probarlo. En TDD, haremos lo contrario: definiremos el éxito antes de escribir la lógica.

**2. Respuesta técnica**
A continuación, se detalla la implementación siguiendo el ciclo **Red-Green-Refactor** en C#.

**Paso 1: Red (Escribir la prueba que falla)**
Creamos la prueba antes que la clase. Noten que el código ni siquiera compilará al principio porque la clase `PasswordValidator` no existe.

```csharp
[Fact]
public void IsValid_PasswordTooShort_ShouldReturnFalse()
{
    // Arrange
    var validator = new PasswordValidator(); // Error: la clase no existe aún
    var password = "123";

    // Act
    var result = validator.IsValid(password);

    // Assert
    Assert.False(result);
}
```

**Paso 2: Green (Escribir el código mínimo para pasar)**
Ahora creamos la clase y el método. Para cumplir con la prueba, escribimos la lógica más simple posible.

```csharp
public class PasswordValidator
{
    public bool IsValid(string password)
    {
        // Implementación mínima para que la prueba pase
        if (password.Length < 8) return false;
        return true;
    }
}
```
*Ejecutamos la prueba $\rightarrow$ **Pass (Verde)**.*

**Paso 3: Refactor (Optimizar y expandir)**
Ahora que tenemos una base segura, podemos mejorar el código o añadir más reglas. Supongamos que ahora el negocio pide que también contenga un número. Repetimos el ciclo:

1.  **Red:** Escribimos una prueba `IsValid_NoNumber_ShouldReturnFalse` que falla.
2.  **Green:** Modificamos el método `IsValid` para incluir la validación de números usando LINQ:
    ```csharp
    public bool IsValid(string password)
    {
        return password.Length >= 8 && password.Any(char.IsDigit);
    }
    ```
3.  **Refactor:** Verificamos que el código sea legible y que las pruebas anteriores sigan pasando.

**Conclusión del ejemplo:**
El resultado final es un método `IsValid` que ha sido "moldeado" por las pruebas. El desarrollador nunca escribió código "por si acaso", sino que cada línea de código existe porque hay una prueba que la justifica.

---
---
---

# Acceptance Test Driven Development
>Como definir metodologías Acceptance Test Driven Development 

**1. Antecedentes**
Mientras que el TDD se enfoca en la perspectiva del desarrollador (pruebas unitarias, detalles técnicos y diseño interno), existía una brecha de comunicación entre el equipo técnico y los stakeholders (clientes, Product Owners o analistas de negocio). A menudo, el equipo desarrollaba funciones que pasaban todas las pruebas unitarias (TDD), pero que no eran lo que el cliente esperaba. El **Acceptance TDD (ATDD)** surge para cerrar esta brecha, moviendo el foco desde "está bien programado" hacia "está haciendo lo que el negocio necesita".

**2. Respuesta técnica**
El **Acceptance Test Driven Development (ATDD)** es una metodología colaborativa donde los criterios de aceptación de una funcionalidad se definen **antes** de que comience el desarrollo, mediante la creación de pruebas de aceptación automatizadas.

A diferencia del TDD, que es una actividad principalmente técnica, el ATDD es un proceso de **"Tres Amigos"** (Product Owner, Desarrollador y QA), quienes acuerdan el comportamiento esperado del sistema.

**El proceso de implementación técnica sigue este flujo:**

1.  **Definición de Escenarios (Gherkin):** Se redactan los requerimientos en un lenguaje natural estructurado, generalmente usando el formato **Given-When-Then** (Dado que - Cuando - Entonces).
    *   *Ejemplo:* **Dado que** el usuario tiene un saldo de $10, **Cuando** intenta retirar $20, **Entonces** el sistema debe mostrar un error de "Saldo Insuficiente".
2.  **Automatización del Test de Aceptación:** Estos escenarios se traducen en pruebas automatizadas (utilizando herramientas como **SpecFlow** en .NET o **Cucumber**). En este punto, la prueba falla porque la funcionalidad no existe.
3.  **Desarrollo mediante TDD:** El equipo de desarrollo comienza a escribir el código siguiendo el ciclo de TDD (Red-Green-Refactor) hasta que la funcionalidad es lo suficientemente completa como para hacer pasar la prueba de aceptación.
4.  **Validación Final:** Una vez que la prueba de aceptación pasa, la funcionalidad se considera "Done" (Terminada) según la definición de negocio.

**Diferencia clave para un Líder Técnico:**
Mientras que el TDD garantiza que el código es **robusto y mantenible** (calidad técnica), el ATDD garantiza que el producto es **útil y correcto** (valor de negocio). En un flujo de trabajo maduro, el ATDD envuelve al TDD: el test de aceptación define la meta, y los tests unitarios guían el camino para llegar a ella.

---
---
---

# Diferencias entre Acceptance Test Driven Development y Test Driven Development 
>Que Diferencias hay entre estas dos metodologias? 

**1. Antecedentes**
En la gestión de proyectos de software, es común confundir el TDD con el ATDD porque ambos comparten la premisa de "escribir la prueba antes que el código". Sin embargo, operan en niveles de abstracción completamente diferentes. El TDD es una herramienta de **micro-diseño** (enfocada en la implementación), mientras que el ATDD es una herramienta de **macro-definición** (enfocada en el requerimiento).

**2. Respuesta técnica**
La diferencia fundamental radica en el **objetivo**, el **alcance** y los **participantes**. A continuación, se detalla la comparativa técnica:

| Atributo                  | Test Driven Development (TDD)                              | Acceptance TDD (ATDD)                                             |
| :------------------------ | :--------------------------------------------------------- | :---------------------------------------------------------------- |
| **Objetivo Principal**    | Diseño de código, calidad técnica y prevención de bugs.    | Validación de requerimientos y alineación con el negocio.         |
| **Nivel de Prueba**       | **Unitario:** Prueba métodos, clases y funciones aisladas. | **End-to-End / Integración:** Prueba flujos completos de usuario. |
| **Quién lo define**       | El Desarrollador.                                          | El "Trío" (Product Owner, QA y Desarrollador).                    |
| **Lenguaje**              | Código técnico (C#, xUnit, NUnit).                         | Lenguaje natural estructurado (Gherkin / BDD).                    |
| **Pregunta que responde** | "¿Estoy construyendo la solución correctamente?"           | "¿Estoy construyendo la solución correcta?"                       |
| **Momento del ciclo**     | Durante la codificación de la funcionalidad.               | Antes de iniciar la codificación de la funcionalidad.             |

**Sinergia entre ambas (El flujo ideal):**
En un entorno de alta ingeniería, estas metodologías no compiten, sino que se complementan en una jerarquía de cascada:

1.  **ATDD** define el **Escenario de Aceptación** $\rightarrow$ *"El usuario puede iniciar sesión"*. (La prueba falla).
2.  El desarrollador usa **TDD** para crear las piezas necesarias:
    *   TDD para la clase `UserRepository`.
    *   TDD para el servicio `AuthService`.
    *   TDD para el validador de credenciales.
3.  Una vez que todas las piezas pequeñas funcionan, el **Test de Aceptación (ATDD)** finalmente pasa.

Como Líder Técnico, implementar ambas asegura que el equipo no solo entregue código sin errores, sino que entregue exactamente lo que el cliente pidió, eliminando el riesgo de retrabajo por malentendidos de requerimientos.

---
---
---

# Otras Metodologías 
>Que Diferencias hay entre estas dos metodologias? 

Dado que estamos en el contexto de una entrevista para un **Líder Técnico en .NET**, no me limitaré solo a metodologías de prueba, sino que abarcaré las metodologías de **desarrollo, arquitectura y gestión**, ya que un líder debe dominar el ciclo de vida completo del software (SDLC).

Aquí tienes las más relevantes divididas por categorías:

---

### 1. Metodologías de Diseño y Desarrollo de Código

**A. Behavior Driven Development (BDD)**
*   **Antecedentes:** Es la evolución natural del ATDD. Busca que la comunicación sea tan natural que incluso alguien que no sabe programar pueda leer las pruebas.
*   **Técnica:** Utiliza el lenguaje **Gherkin** (`Given`, `When`, `Then`). Se centra en el *comportamiento* del sistema desde la perspectiva del usuario. En .NET, la herramienta estándar es **SpecFlow**.

**B. Domain Driven Design (DDD)**
*   **Antecedentes:** En proyectos complejos, el código suele volverse un caos porque la lógica de negocio se mezcla con la lógica de base de datos o de interfaz.
*   **Técnica:** Se enfoca en modelar el software basándose en el "Dominio" (el negocio). Introduce conceptos como **Ubiquitous Language** (lenguaje común entre negocio y técnicos), **Bounded Contexts** (contextos delimitados) y **Aggregates**. Es fundamental para diseñar microservicios.

---

### 2. Metodologías de Arquitectura

**A. Clean Architecture / Onion Architecture**
*   **Antecedentes:** El problema de las arquitecturas en capas tradicionales es que la lógica de negocio suele depender de la base de datos, haciendo que cambiar de DB o de framework sea una pesadilla.
*   **Técnica:** Propone capas concéntricas donde la dependencia siempre va hacia adentro. El núcleo (**Domain/Entities**) no depende de nada. La capa externa (UI, DB, APIs) es solo un detalle. Se basa fuertemente en el **Principio de Inversión de Dependencias**.

**B. CQRS (Command Query Responsibility Segregation)**
*   **Antecedentes:** En aplicaciones de alta escala, usar el mismo modelo para leer datos y para escribir datos genera cuellos de botella y complejidad.
*   **Técnica:** Separa la aplicación en dos caminos: los **Commands** (que cambian el estado/escriben) y las **Queries** (que solo leen datos). A menudo se combina con **Event Sourcing** para guardar el historial de cambios en lugar de solo el estado actual.

---

### 3. Metodologías de Gestión y Flujo de Trabajo

**A. Scrum**
*   **Antecedentes:** El modelo "Waterfall" (cascada) era demasiado lento y rígido para el software moderno.
*   **Técnica:** Framework ágil basado en iteraciones fijas llamadas **Sprints**. Incluye roles claros (Scrum Master, Product Owner, Development Team) y ceremonias (Daily, Sprint Planning, Review y Retrospective).

**B. Kanban**
*   **Antecedentes:** Scrum puede ser demasiado rígido para equipos de mantenimiento o soporte donde las prioridades cambian cada hora.
*   **Técnica:** Se enfoca en la **visualización del flujo de trabajo** y la limitación del **WIP (Work In Progress)**. No hay sprints, sino un flujo continuo de tareas que se mueven por un tablero.

**C. DevOps / CI-CD**
*   **Antecedentes:** La fricción entre el equipo de Desarrollo (que quiere cambiar cosas) y el de Operaciones (que quiere estabilidad) generaba despliegues lentos y riesgosos.
*   **Técnica:** No es solo una herramienta, sino una cultura. Implementa la **Integración Continua (CI)** (pruebas automáticas en cada commit) y la **Entrega Continua (CD)** (despliegue automatizado a producción).
---
**Consejo para la entrevista:** 
Si te preguntan esto, no solo menciones los nombres. Un Líder Técnico destaca cuando dice: 

**"Dependiendo del proyecto, elijo la metodología. Por ejemplo, para un sistema complejo de seguros usaría **DDD** con **Clean Architecture** y gestionaría el equipo con **Scrum**, mientras que para un equipo de soporte usaría **Kanban**"**

---
---
---