Sí. La clave del ejercicio está en interpretar correctamente **“se invoca una vez el método y debe hacer el recorrido de las 15 luces”**.

Yo lo plantearía como un problema de **estado + recorrido**, y en una prueba técnica evitaría hacer 15 llamadas manuales.

Por ejemplo, si tenemos:

```
[💡][⚫][⚫][⚫][⚫][⚫][⚫][⚫][⚫][⚫][⚫][⚫][⚫][⚫][⚫]
```

la ejecución del método debería producir:

```
[💡][⚫][⚫]...
[⚫][💡][⚫]...
[⚫][⚫][💡]...
...
[⚫][⚫][⚫]...[💡]
```

## Una implementación sencilla en C#

Supongamos que cada foco tiene un estado `Encendido/Apagado`:

```
public class Foco
{
    public bool Encendido { get; private set; }

    public void Encender()
    {
        Encendido = true;
    }

    public void Apagar()
    {
        Encendido = false;
    }
}
```

Y tenemos nuestra serie de 15 focos:

```
public class SerieDeLuces
{
    private readonly Foco[] focos;

    public SerieDeLuces()
    {
        focos = Enumerable.Range(0, 15)
                          .Select(_ => new Foco())
                          .ToArray();
    }

    public void Recorrer()
    {
        for (int i = 0; i < focos.Length; i++)
        {
            // Apagar todos
            for (int j = 0; j < focos.Length; j++)
            {
                focos[j].Apagar();
            }

            // Encender solamente el actual
            focos[i].Encender();

            // Aquí podría haber una pausa para visualizar el recorrido
            Console.WriteLine($"Foco {i + 1} encendido");
        }
    }
}
```

Y se invoca **una sola vez**:

```
var serie = new SerieDeLuces();

serie.Recorrer();
```

### Pero hay una mejora importante

En una entrevista técnica, probablemente te preguntaría:

> “¿Por qué apagar los 15 focos en cada iteración si sabes que solamente tienes que mover el foco encendido?”

Puedes hacerlo mucho más eficiente manteniendo la posición anterior:

```
public void Recorrer()
{
    int focoAnterior = -1;

    for (int i = 0; i < focos.Length; i++)
    {
        if (focoAnterior >= 0)
        {
            focos[focoAnterior].Apagar();
        }

        focos[i].Encender();

        Console.WriteLine($"Foco {i + 1} encendido");

        focoAnterior = i;
    }
}
```

Así, conceptualmente, haces:

```
1. Enciendo foco 1
2. Apago foco 1
   Enciendo foco 2
3. Apago foco 2
   Enciendo foco 3
...
15. Apago foco 14
    Enciendo foco 15
```

Esto tiene una complejidad **O(n)**, mientras que la primera implementación tiene **O(n²)** por apagar todos los focos en cada vuelta.

Si el ejercicio además requiere que **se vea físicamente el recorrido** (por ejemplo, un foco encendido durante 500 ms antes de pasar al siguiente), entonces entraría `async/await` y `Task.Delay`. Ahí cambia un poco la solución y probablemente es lo que realmente quieren evaluar en la prueba.

----
----
----

Sí, estás **correcto**. De hecho, estás identificando dos conceptos diferentes que conviene separar:

## 1\. ¿Qué estructura de datos usaría?

Un **arreglo (array)** es una muy buena elección.

Tienes una cantidad fija de 15 focos y cada uno tiene una posición:

```
Índice:   0    1    2    3    4   ...   14
          ↓    ↓    ↓    ↓    ↓          ↓
Focos:   [💡] [⚫] [⚫] [⚫] [⚫] ...    [⚫]
```

En C#:

```
Foco[] focos = new Foco[15];
```

El arreglo encaja especialmente bien porque:

- Sabemos que son **15 elementos**.
- Los focos tienen una **posición determinada**.
- Podemos acceder directamente a un foco mediante su índice.
- El recorrido es secuencial: `0 → 1 → 2 → ... → 14`.

Incluso podrías utilizar una `List<Foco>`, pero para este problema el arreglo expresa mejor la idea de **cantidad fija de elementos**.

---

## 2\. ¿Qué tipo de algoritmo es?

Aquí hay algo interesante: **no es un algoritmo complejo ni pertenece a una categoría especial como búsqueda binaria, ordenamiento, etc.**

Principalmente estás implementando un:

### Algoritmo de recorrido (traversal)

Tienes una colección:

```
[0][1][2][3][4][5]...[14]
```

y recorres sus elementos **uno por uno**:

```
for (int i = 0; i < focos.Length; i++)
{
    // trabajar con focos[i]
}
```

En cada iteración haces una operación sobre el foco actual.

Conceptualmente:

```
i = 0 → encender foco 0
i = 1 → encender foco 1
i = 2 → encender foco 2
...
i = 14 → encender foco 14
```

Por eso, si te preguntaran en una entrevista:

> **¿Qué algoritmo estás utilizando?**

Una buena respuesta sería:

> "Es un algoritmo de recorrido secuencial sobre una estructura de datos lineal. En este caso utilizo un arreglo para representar los 15 focos y un ciclo `for` para recorrerlos en orden."

Eso suena mucho más preciso que simplemente decir _"es un for"_.

---

## 3\. Hay otro concepto importante: el estado

Aquí está probablemente **la parte más interesante del ejercicio**.

No solamente estás recorriendo un arreglo. Tienes una regla:

> **En todo momento solamente puede haber un foco encendido.**

Por lo tanto, tienes que mantener un **estado**.

Por ejemplo:

```
focos = [false, false, false, false, false]
```

Inicialmente todos están apagados.

Después:

```
focos = [true, false, false, false, false]
```

Después:

```
focos = [false, true, false, false, false]
```

Después:

```
focos = [false, false, true, false, false]
```

Y así sucesivamente.

Una forma incluso más sencilla de representar el estado es guardar **qué posición está encendida**:

```
int focoEncendido = 0;
```

Luego:

```
focoEncendido = 0
        ↓
[💡][⚫][⚫][⚫]...

focoEncendido = 1
           ↓
[⚫][💡][⚫][⚫]...

focoEncendido = 2
              ↓
[⚫][⚫][💡][⚫]...
```

Esto nos lleva a una posible implementación bastante elegante.

```
public void Recorrer()
{
    for (int i = 0; i < focos.Length; i++)
    {
        if (i > 0)
        {
            focos[i - 1].Apagar();
        }

        focos[i].Encender();
    }
}
```

Aquí el algoritmo es:

1. Recorrer el arreglo.
2. Apagar el foco anterior.
3. Encender el foco actual.
4. Continuar con el siguiente.

Y la complejidad es:

**Tiempo: O(n)** **Espacio adicional: O(1)**

Para 15 focos obviamente da igual en rendimiento, pero en una prueba técnica demuestra que entiendes el algoritmo y no solamente que sabes escribir un `for`.

### En resumen

| Concepto | En este ejercicio |
| --- | --- |
| Estructura de datos | **Arreglo (****`array`****)** |
| Tipo de estructura | **Lineal** |
| Algoritmo | **Recorrido secuencial** |
| Control del recorrido | `for` |
| Estado | Posición del foco encendido |
| Complejidad | **O(n)** |
| Espacio adicional | **O(1)** |

Y hay una distinción que te recomiendo aprender muy bien para este tipo de pruebas:

**estructura de datos ≠ algoritmo.**

El **arreglo** es la estructura donde guardas los focos. El **recorrido secuencial** es el algoritmo que utilizas para procesarlos.

Si quieres, podemos seguir desmenuzando el ejercicio como si estuviéramos preparándote para una **entrevista técnica de C#**, empezando por qué preguntas podría hacerte el entrevistador después de esta solución.


