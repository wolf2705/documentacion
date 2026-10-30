# React

1. Background/Context
Para un Technical Lead, React no es simplemente una librería para crear interfaces, sino un ecosistema de gestión de estado y renderizado eficiente. El enfoque de un líder no debe estar en "cómo hacer un componente", sino en la **estandarización, la mantenibilidad y el rendimiento**. Un líder técnico debe decidir la arquitectura de la aplicación para evitar que el proyecto se convierta en un "espagueti de componentes", asegurando que el equipo siga patrones que faciliten el testing, el despliegue continuo y la escalabilidad del código a medida que el producto crece.

2. Technical Answer
Desde la perspectiva de liderazgo técnico, React se analiza bajo los siguientes pilares estratégicos:

**Arquitectura y Organización del Código**
El mayor riesgo en proyectos de React es la falta de estructura. Un líder debe imponer un patrón de organización, como:
*   **Atomic Design:** Dividir la UI en Átomos, Moléculas, Organismos, Plantillas y Páginas para fomentar la reutilización máxima.
*   **Container/Presentational Pattern:** Separar los componentes que manejan la lógica y llamadas a API (Containers) de aquellos que solo se encargan de la representación visual (Presentational/Dumb Components).
*   **Custom Hooks:** Extraer la lógica de negocio de los componentes hacia Hooks personalizados para evitar la duplicación de código y facilitar las pruebas unitarias independientes de la UI.

**Gestión de Estado (State Management)**
La elección de la herramienta de estado define la complejidad del proyecto. El líder debe evaluar:
*   **Estado Local (`useState`, `useReducer`):** Para datos efímeros y aislados.
*   **Estado Global (`Context API`):** Para datos que cambian poco y son consumidos por muchos (ej. Tema, Idioma, Usuario autenticado). Evitar el "Provider Hell" y el re-renderizado masivo.
*   **Librerías Externas (`Redux Toolkit`, `Zustand`):** Para estados complejos, globales y con flujos de datos predecibles.
*   **Server State (`React Query`, `SWR`):** Fundamental para separar el estado de la UI del estado que viene del servidor, gestionando automáticamente el almacenamiento en caché, la sincronización y los estados de carga/error.

**Rendimiento y Optimización (Performance)**
Un líder debe monitorear y guiar al equipo en la prevención de re-renders innecesarios:
*   **Memoización:** Uso estratégico de `React.memo`, `useMemo` y `useCallback` para evitar que componentes pesados se rendericen si sus props no han cambiado.
*   **Code Splitting:** Implementar `React.lazy` y `Suspense` para cargar solo los módulos necesarios, reduciendo el tamaño del bundle inicial y mejorando el *First Contentful Paint* (FCP).
*   **Virtualización:** Implementar herramientas como `react-window` o `react-virtualized` cuando se manejan listas de miles de elementos para no colapsar el DOM.

**Calidad y Ecosistema**
El liderazgo técnico se refleja en la implementación de un pipeline de calidad:
*   **Tipado Estricto:** Uso obligatorio de **TypeScript** para reducir errores en tiempo de ejecución y mejorar el autocompletado y la documentación implícita del código.
*   **Estrategia de Testing:** Definir una pirámide de pruebas:
    *   *Unit Tests:* Con **Jest** y **React Testing Library** para lógica y componentes aislados.
    *   *E2E Tests:* Con **Cypress** o **Playwright** para flujos críticos de usuario.
*   **Estándares de Estilo:** Configuración de **ESLint** y **Prettier** para que el código sea uniforme, independientemente de quién lo escriba.

## Pros y Cons

1. Background/Context
Al evaluar una tecnología para un proyecto empresarial, un Technical Lead no busca la "mejor" herramienta en términos absolutos, sino la que mejor se adapte a los objetivos del negocio, la experiencia del equipo y los requerimientos técnicos. La elección de React implica aceptar ciertas ventajas operativas a cambio de una carga de gestión arquitectónica, ya que, al ser una librería y no un framework completo (como Angular), deja muchas decisiones en manos del equipo de desarrollo.

2. Technical Answer
A continuación se presenta el análisis de ventajas y desventajas de React desde una perspectiva de ingeniería y gestión:

**Pros (Ventajas)**
*   **Ecosistema Masivo y Comunidad:** Es la librería más popular del mercado. Esto se traduce en una facilidad enorme para contratar desarrolladores, encontrar librerías de terceros probadas y resolver problemas rápidamente gracias a la documentación comunitaria.
*   **Virtual DOM y Eficiencia:** El uso del DOM virtual permite actualizar solo las partes de la página que realmente cambiaron, lo que optimiza el rendimiento de la interfaz en aplicaciones con actualizaciones de datos frecuentes.
*   **Componentización y Reutilización:** Fomenta la creación de piezas de UI independientes y modulares. Esto acelera el desarrollo a largo plazo, ya que permite crear una propia "librería de componentes" interna.
*   **Flexibilidad Arquitectónica:** Al ser una librería, no impone una estructura rígida. El líder técnico puede elegir la mejor herramienta para el routing (React Router), el estado (Zustand, Redux) o el consumo de APIs (React Query) según la necesidad del proyecto.
*   **React Native:** Permite aprovechar la base de conocimientos del equipo para saltar al desarrollo móvil nativo con una curva de aprendizaje mínima, compartiendo gran parte de la lógica de negocio.

**Cons (Desventajas)**
*   **Fatiga de Decisiones (Decision Fatigue):** Al no ser un framework "todo incluido", el equipo debe decidir y configurar cada pieza del puzzle. Si el líder no define estándares claros, cada desarrollador puede implementar las cosas de manera diferente, generando inconsistencias.
*   **Curva de Aprendizaje en Conceptos Modernos:** Aunque el JSX es sencillo, dominar los Hooks, la gestión de efectos (`useEffect`) y la optimización de renders requiere un nivel de seniority considerable para evitar bugs de rendimiento o fugas de memoria (*memory leaks*).
*   **Ritmo de Actualización Acelerado:** React evoluciona rápidamente (ej. la transición de componentes de clase a funciones, y ahora a *Server Components*). Esto puede generar deuda técnica rápidamente si el proyecto no se actualiza o si el equipo no se mantiene al día.
*   **SEO y Renderizado Inicial:** Por defecto, React renderiza en el cliente (Client-Side Rendering), lo que puede afectar el SEO y la velocidad de carga percibida. Para solucionar esto, se requiere añadir capas de complejidad adicional como **Next.js** para implementar SSR (Server-Side Rendering) o SSG (Static Site Generation).
*   **Dependencia de Librerías Externas:** Para tareas básicas (como formularios o validaciones), a menudo se depende de librerías de terceros. Si una librería clave deja de recibir mantenimiento, el proyecto queda vulnerable o requiere una migración costosa.


React vs Angular

1. Background/Context
Desde la posición de un Technical Lead, la comparativa entre React y Angular no es una batalla de "cuál es mejor", sino una elección entre dos filosofías de ingeniería: **Libertad vs. Estructura**. React es una librería que ofrece flexibilidad y permite construir la arquitectura a medida, mientras que Angular es un framework robusto y opinado que dicta exactamente cómo debe construirse la aplicación. La decisión final depende del tamaño del equipo, la complejidad del proyecto y la necesidad de estandarización inmediata.

2. Technical Answer
A continuación se presenta la comparativa técnica detallada:

| Atributo | React | Angular |
| :--- | :--- | :--- |
| **Naturaleza** | Librería de UI (Enfoque en la Vista). | Framework Full-featured (Plataforma completa). |
| **Filosofía** | Minimalista y flexible. El desarrollador elige el resto del stack. | Opinado y estructurado. Incluye routing, HTTP y formularios nativamente. |
| **Lenguaje** | JavaScript / JSX (TypeScript opcional pero recomendado). | TypeScript obligatorio (fuertemente tipado). |
| **Renderizado** | Virtual DOM (actualizaciones parciales eficientes). | Incremental DOM (optimizado para cambios masivos). |
| **Flujo de Datos** | Unidireccional (One-way data binding). | Bidireccional (Two-way data binding). |
| **Curva de Aprendizaje** | Baja al inicio, alta para dominar el ecosistema y el rendimiento. | Alta desde el inicio debido a conceptos como Inyección de Dependencias y RxJS. |
| **Estandarización** | Depende del líder técnico y los acuerdos del equipo. | Alta y nativa; cualquier desarrollador de Angular entiende la estructura de otro proyecto. |

**Cuándo elegir React (Análisis de Líder)**
*   **Proyectos con requerimientos cambiantes:** Cuando se necesita iterar rápido y tener flexibilidad para cambiar librerías de estado o routing.
*   **Equipos con fuerte base de JS/TS:** Donde se prefiere una integración ligera y modular.
*   **Aplicaciones enfocadas en la UX/UI:** Donde el control granular sobre el renderizado y la velocidad de interacción son críticos.
*   **Estrategia Multiplataforma:** Si el objetivo es compartir lógica con una app móvil vía React Native.

**Cuándo elegir Angular (Análisis de Líder)**
*   **Aplicaciones Enterprise masivas:** Donde participan decenas de desarrolladores y se requiere que todos sigan exactamente el mismo patrón para evitar el caos.
*   **Proyectos con lógica de negocio compleja:** Donde el uso de Inyección de Dependencias y la estructura de servicios facilitan la organización de reglas de negocio pesadas.
*   **Necesidad de estabilidad a largo plazo:** Al ser un framework mantenido por Google con todo integrado, hay menos riesgo de que una librería externa "muera" y rompa la aplicación.
*   **Equipos familiarizados con Java/C#:** El modelo de clases, decoradores y servicios de Angular es muy similar al de los frameworks de backend empresariales (.NET/Spring), lo que facilita la transición de desarrolladores Fullstack.

## Ejercicio de React a Resolver

1. Background/Context
En una entrevista para un puesto de **Technical Lead**, el evaluador no busca ver si sabes hacer un `map()` o un `useState`. Lo que realmente quieren observar es tu capacidad para:
1.  **Diseñar la arquitectura:** Cómo organizas los componentes y el flujo de datos.
2.  **Manejar el rendimiento:** Cómo evitas renders innecesarios.
3.  **Gestionar el estado:** Cómo decides qué es estado local y qué es global.
4.  **Escribir código limpio:** Aplicación de principios SOLID y legibilidad.

2. Technical Answer
El ejercicio más común y efectivo es la creación de una **"Aplicación de Búsqueda de Datos con API"** (por ejemplo, un buscador de usuarios de GitHub o de películas). 

Aunque parece simple, el candidato es evaluado en los siguientes puntos críticos:

### El Desafío Típico: "GitHub User Search"
**Consigna:** Crear una interfaz donde el usuario escriba un nombre en un input, se haga una petición a la API de GitHub, se muestre la lista de usuarios y, al hacer clic en uno, se muestre el detalle del perfil.

### Lo que el entrevistador evaluará (y donde debes destacar):

**1. Manejo de Efectos y API (Data Fetching)**
*   **Mal:** Hacer el `fetch` directamente en el cuerpo del componente.
*   **Bien:** Usar `useEffect` correctamente o, mejor aún, encapsular la lógica en un **Custom Hook** (ej. `useGitHubSearch`) para separar la lógica de la vista.
*   **Plus:** Implementar un **Debounce** en el input de búsqueda para no saturar la API con una petición por cada tecla presionada.

**2. Gestión de Estados de Carga y Error**
*   **Mal:** Mostrar solo la lista de resultados.
*   **Bien:** Implementar explícitamente tres estados: `isLoading`, `error` y `data`. Manejar el caso donde la API devuelve 404 o el límite de peticiones ha sido alcanzado.

**3. Optimización de Rendimiento**
*   **Mal:** Que toda la aplicación se renderice cada vez que el usuario escribe una letra.
*   **Bien:** Usar `React.memo` en los componentes de la lista de usuarios para que solo se rendericen si sus datos cambian.

**4. Estructura de Componentes**
*   **Mal:** Un solo archivo de 300 líneas con todo el código.
*   **Bien:** Dividir la UI en componentes pequeños y reutilizables:
    *   `<SearchBar />` (Input y botón).
    *   `<UserList />` (Contenedor de la lista).
    *   `<UserItem />` (Fila individual del usuario).
    *   `<UserProfile />` (Vista de detalle).

**5. Tipado con TypeScript**
*   **Mal:** Usar `any` en la respuesta de la API.
*   **Bien:** Definir interfaces claras para el usuario (`interface GitHubUser { login: string, avatar_url: string... }`).

### Ejemplo de "Respuesta Nivel Lead" en el código:
Si te piden resolverlo, menciona mientras escribes:
> *"Voy a extraer la lógica de la API a un Custom Hook para que el componente sea puramente presentacional. Implementaré un debounce de 300ms para optimizar el tráfico de red y usaré TypeScript para asegurar que el contrato con la API de GitHub sea respetado en toda la aplicación".*

## Código Ejemplo y Solución

1. Background/Context
Para resolver este ejercicio a nivel de Technical Lead, no nos limitaremos a que el código "funcione". Aplicaremos una arquitectura profesional: **Separación de preocupaciones (Separation of Concerns)**. 

Dividiremos la solución en tres capas:
1.  **Capa de Servicio:** Encargada de la comunicación con la API.
2.  **Capa de Lógica (Custom Hook):** Encargada de gestionar el estado, la carga, los errores y el debounce.
3.  **Capa de Vista (Componentes):** Componentes atómicos y presentacionales.

2. Technical Answer
Aquí tienes la solución completa utilizando **React** y **TypeScript**.

### 1. Capa de Servicio (`githubService.ts`)
Aislamos la petición HTTP para que, si la API cambia o decidimos usar Axios, solo modifiquemos un archivo.

```typescript
export interface GitHubUser {
  login: string;
  avatar_url: string;
  html_url: string;
}

export const fetchGitHubUsers = async (query: string): Promise<GitHubUser[]> {
  const response = await fetch(`https://api.github.com/search/users?q=${query}`);
  if (!response.ok) throw new Error('Error al obtener usuarios');
  const data = await response.json();
  return data.items;
};
```

### 2. Capa de Lógica: Custom Hook (`useGitHubSearch.ts`)
Aquí es donde demuestras tu seniority manejando el ciclo de vida y la optimización.

```typescript
import { useState, useEffect } from 'react';
import { fetchGitHubUsers, GitHubUser } from './githubService';

export const useGitHubSearch = (query: string) => {
  const [users, setUsers] = useState<GitHubUser[]>([]);
  const [isLoading, setIsLoading] = useState(false);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    // 1. Implementación de DEBOUNCE para optimizar llamadas a la API
    const handler = setTimeout(async () => {
      if (!query) {
        setUsers([]);
        return;
      }

      setIsLoading(true);
      setError(null);
      try {
        const data = await fetchGitHubUsers(query);
        setUsers(data);
      } catch (err) {
        setError('Hubo un problema al buscar usuarios');
      } finally {
        setIsLoading(false);
      }
    }, 500); // Espera 500ms después de que el usuario deje de escribir

    // 2. Limpieza del timeout para evitar memory leaks y llamadas innecesarias
    return () => clearTimeout(handler);
  }, [query]);

  return { users, isLoading, error };
};
```

### 3. Capa de Vista: Componentes Presentacionales

**Componente de Item (`UserItem.tsx`)**
Usamos `React.memo` para evitar re-renders si el usuario no ha cambiado.

```tsx
import React from 'react';
import { GitHubUser } from './githubService';

interface Props { user: GitHubUser; }

export const UserItem = React.memo(({ user }: Props) => {
  console.log(`Rendering: ${user.login}`); // Para demostrar optimización
  return (
    <div style={{ display: 'flex', alignItems: 'center', gap: '10px', padding: '10px', borderBottom: '1px solid #eee' }}>
      <img src={user.avatar_url} alt={user.login} width={40} height={40} style={{ borderRadius: '50%' }} />
      <a href={user.html_url} target="_blank" rel="noreferrer">{user.login}</a>
    </div>
  );
});
```

**Componente Principal (`App.tsx`)**

```tsx
import React, { useState } from 'react';
import { useGitHubSearch } from './useGitHubSearch';
import { UserItem } from './UserItem';

export const App = () => {
  const [searchTerm, setSearchTerm] = useState('');
  const { users, isLoading, error } = useGitHubSearch(searchTerm);

  return (
    <div style={{ maxWidth: '500px', margin: '0 auto', fontFamily: 'Arial' }}>
      <h1>GitHub Search</h1>
      <input 
        type="text" 
        placeholder="Buscar usuario..." 
        value={searchTerm}
        onChange={(e) => setSearchTerm(e.target.value)}
        style={{ width: '100%', padding: '10px', marginBottom: '20px' }}
      />

      {isLoading && <p>Cargando usuarios...</p>}
      {error && <p style={{ color: 'red' }}>{error}</p>}
      
      {!isLoading && !error && (
        <div>
          {users.length > 0 ? (
            users.map(user => <UserItem key={user.id} user={user} />)
          ) : (
            searchTerm && <p>No se encontraron usuarios.</p>
          )}
        </div>
      )}
    </div>
  );
};
```

### Por qué esta solución es de nivel "Lead":
1.  **Separación de Capas:** Si mañana cambian la API de GitHub por una de GitLab, solo cambias el archivo `githubService.ts`.
2.  **Rendimiento (UX):** El **Debounce** evita que la app se bloquee o sea baneada por la API por exceso de peticiones.
3.  **Eficiencia de Renderizado:** El uso de `React.memo` asegura que solo los elementos nuevos o modificados se dibujen en pantalla.
4.  **Tipado Robusto:** TypeScript elimina la ambigüedad de los datos que vienen de la red.
5.  **Manejabilidad:** El código es modular, fácil de testear (puedes testear el hook independientemente de la UI) y fácil de leer.
