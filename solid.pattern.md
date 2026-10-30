- [Principios SOLID](#principios-solid)
- [Ejemplos de Implementación](#ejemplos-de-implementación)

---

# Principios SOLID


1. Background/Context
En una entrevista para Technical Lead, no basta con definir los acrónimos de SOLID; es fundamental demostrar cómo estos principios reducen la deuda técnica, facilitan la mantenibilidad y permiten que el código sea extensible sin romper funcionalidades existentes. Se espera que el candidato relacione estos conceptos con la arquitectura del software y la capacidad de coordinar a un equipo para escribir código limpio y testeable.

2. Technical Answer
SOLID es un conjunto de cinco principios de diseño orientado a objetos que buscan hacer el software más comprensible, flexible y mantenible:

*   **S - Single Responsibility Principle (Principio de Responsabilidad Única):** Una clase debe tener una única razón para cambiar. Esto implica que cada módulo o clase debe encargarse de una sola funcionalidad. Por ejemplo, una clase `OrderProcessor` debe gestionar la lógica del pedido, pero no debe encargarse de escribir el log en la base de datos o enviar correos electrónicos; para ello debe delegar en clases especializadas como `Logger` o `EmailService`.
*   **O - Open/Closed Principle (Principio de Abierto/Cerrado):** Las entidades de software deben estar abiertas para su extensión, pero cerradas para su modificación. Se logra mediante el uso de abstracciones (interfaces o clases abstractas). Si necesitamos agregar un nuevo método de pago, no debemos modificar la lógica existente del procesador de pagos, sino crear una nueva implementación de una interfaz `IPaymentMethod`.
*   **L - Liskov Substitution Principle (Principio de Sustitución de Liskov):** Los objetos de una subclase deben poder reemplazar a los objetos de la clase base sin alterar el funcionamiento correcto del programa. Si una clase `Bird` tiene un método `Fly()`, pero creamos una subclase `Ostrich` (Avestruz) que no puede volar, estamos violando este principio. La solución es extraer `IFlyable` a una interfaz separada.
*   **I - Interface Segregation Principle (Principio de Segregación de Interfaces):** Es preferible tener muchas interfaces específicas que una sola interfaz general. No se debe obligar a una clase a implementar métodos que no utiliza. En lugar de una interfaz `IMachine` con `Print()`, `Fax()` y `Scan()`, es mejor tener `IPrinter`, `IFax` e `IScanner`.
*   **D - Dependency Inversion Principle (Principio de Inversión de Dependencias):** Los módulos de alto nivel no deben depender de módulos de bajo nivel; ambos deben depender de abstracciones. En .NET, esto se implementa mediante la Inyección de Dependencias (DI). El controlador de una API no debe instanciar directamente un `SqlRepository`, sino depender de una interfaz `IRepository`, permitiendo cambiar la base de datos sin afectar la lógica de negocio.


# Ejemplos de Implementación

1. Background/Context
Para un perfil de Technical Lead, el código no solo debe "funcionar", sino que debe ser un ejemplo de arquitectura sostenible. Al presentar ejemplos de SOLID, es vital resaltar que el objetivo es eliminar el acoplamiento fuerte y facilitar la creación de pruebas unitarias (Unit Tests). Un código que sigue SOLID permite que los desarrolladores trabajen en diferentes módulos simultáneamente sin generar conflictos constantes en el control de versiones y reduce el riesgo de introducir regresiones al añadir nuevas funcionalidades.

2. Technical Answer
A continuación se presenta la implementación de cada principio en C#:

**S - Single Responsibility Principle**
Evitamos que una clase haga todo. Separamos la lógica de negocio de la persistencia y la notificación.

```csharp
public class Order {
    public int Id { get; set; }
    public decimal Total { get; set; }
}

// Responsabilidad: Gestionar la lógica del pedido
public class OrderService {
    public void ProcessOrder(Order order) {
        // Lógica de procesamiento...
    }
}

// Responsabilidad: Persistencia de datos
public class OrderRepository {
    public void Save(Order order) {
        // Guardar en base de datos...
    }
}

// Responsabilidad: Notificaciones
public class NotificationService {
    public void SendEmail(string message) {
        // Enviar correo...
    }
}
```

**O - Open/Closed Principle**
Utilizamos interfaces para que el sistema crezca mediante la adición de nuevas clases, no modificando las existentes.

```csharp
public interface IPaymentMethod {
    void ProcessPayment(decimal amount);
}

public class CreditCardPayment : IPaymentMethod {
    public void ProcessPayment(decimal amount) => Console.WriteLine($"Pagando {amount} con Tarjeta.");
}

public class PayPalPayment : IPaymentMethod {
    public void ProcessPayment(decimal amount) => Console.WriteLine($"Pagando {amount} con PayPal.");
}

public class PaymentProcessor {
    public void ExecutePayment(IPaymentMethod method, decimal amount) {
        method.ProcessPayment(amount); // No importa el método, el procesador no cambia
    }
}
```

**L - Liskov Substitution Principle**
Aseguramos que las subclases no rompan la expectativa de la clase base. Si una capacidad no es universal, se extrae a una interfaz.

```csharp
public class Bird { 
    public void Eat() => Console.WriteLine("Comiendo..."); 
}

public interface IFlyable {
    void Fly();
}

public class Eagle : Bird, IFlyable {
    public void Fly() => Console.WriteLine("El águila vuela.");
}

public class Ostrich : Bird {
    // El avestruz es un ave, pero no implementa IFlyable, evitando errores en tiempo de ejecución.
}
```

**I - Interface Segregation Principle**
Dividimos interfaces "gordas" en interfaces pequeñas y específicas para evitar implementaciones vacías o innecesarias.

```csharp
public interface IPrinter {
    void Print(string document);
}

public interface IScanner {
    void Scan(string document);
}

// Una impresora básica solo implementa lo que necesita
public class SimplePrinter : IPrinter {
    public void Print(string document) => Console.WriteLine("Imprimiendo...");
}

// Una multifuncional implementa ambas
public class MultiFunctionDevice : IPrinter, IScanner {
    public void Print(string document) => Console.WriteLine("Imprimiendo...");
    public void Scan(string document) => Console.WriteLine("Escaneando...");
}
```

**D - Dependency Inversion Principle**
Dependemos de abstracciones, no de implementaciones concretas, utilizando Inyección de Dependencias.

```csharp
public interface IUserRepository {
    void AddUser(string name);
}

public class SqlUserRepository : IUserRepository {
    public void AddUser(string name) => Console.WriteLine($"Guardando {name} en SQL Server.");
}

public class UserService {
    private readonly IUserRepository _repository;

    // El servicio no sabe si es SQL, MongoDB o un Mock para tests
    public UserService(IUserRepository repository) {
        _repository = repository;
    }

    public void CreateUser(string name) {
        _repository.AddUser(name);
    }
}
```
