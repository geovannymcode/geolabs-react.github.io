# React 🚀

# Repaso de la Clase Anterior

**"Antes de continuar con el nuevo tema, hagamos un repaso rápido de lo que vimos la clase pasada:"**

## JavaScript Moderno
Refrescamos conceptos clave como las arrow functions para escribir funciones de forma más concisa, métodos de arrays para transformar datos, manipulación de objetos y destructuring para extraer datos de forma más limpia.

```javascript
// Arrow functions
const saludar = (nombre) => `Hola ${nombre}`;

// Métodos de arrays
const numeros = [1, 2, 3, 4];
const duplicados = numeros.map(num => num * 2); // [2, 4, 6, 8]
const pares = numeros.filter(num => num % 2 === 0); // [2, 4]
const suma = numeros.reduce((acc, num) => acc + num, 0); // 10

// Destructuring - extraer propiedades de objetos
const persona = { nombre: 'Ana', edad: 30, ciudad: 'Madrid' };
const { nombre, edad } = persona; // Extraemos solo nombre y edad
// Ahora podemos usar 'nombre' y 'edad' directamente

// Destructuring de arrays
const colores = ['rojo', 'verde', 'azul'];
const [primero, segundo] = colores; // primero = 'rojo', segundo = 'verde'
```

## Fundamentos de React
Vimos qué es React, una librería de JavaScript para construir interfaces de usuario de forma declarativa, por qué es útil (reutilización de componentes, manejo eficiente del DOM), y cómo nos permite crear aplicaciones más organizadas y mantenibles.

```jsx
// React nos permite pensar en componentes reutilizables
// En lugar de manipular el DOM directamente, describimos cómo debería verse
```

## Componentes y JSX
Aprendimos que los componentes son funciones que retornan elementos de interfaz, son los bloques de construcción de React, y JSX nos permite escribir HTML dentro de JavaScript de manera natural y expresiva.

```jsx
// Componente simple
function MiComponente() {
  return <h1>¡Hola desde React!</h1>;
}

// JSX permite mezclar HTML con JavaScript
function Bienvenida() {
  const usuario = "María";
  return <h2>Bienvenida, {usuario}!</h2>;
}
```

## Estado con useState
Exploramos cómo manejar datos que cambian en el tiempo usando el hook `useState`, permitiendo que nuestros componentes sean dinámicos e interactivos.

```jsx
const [contador, setContador] = useState(0);
const [nombre, setNombre] = useState('');
```

**Explicación detallada:**
- Usamos `const` porque aunque el valor del estado cambia, las variables `contador` y `setContador` nunca se reasignan
- `contador` es la variable que contiene el valor actual del estado
- `setContador` es la función que usamos para actualizar el estado
- `useState(0)` inicializa el estado con el valor 0
- Destructuring: `useState` retorna un array con dos elementos, los extraemos con `[contador, setContador]`

## Props
Vimos cómo los componentes pueden comunicarse entre sí pasando datos de padres a hijos através de props, creando un flujo de información claro.

```jsx
// Componente padre
<Saludo nombre="Juan" edad={25} />

// Componente hijo
function Saludo({ nombre, edad }) {
  return <h1>Hola {nombre}, tienes {edad} años</h1>;
}
```

## Listas y Eventos
Practicamos cómo renderizar listas de elementos dinámicamente y cómo responder a interacciones del usuario con event handlers.

```jsx
// Renderizar lista usando .map()
// .map() transforma cada elemento del array en un componente JSX
{usuarios.map(usuario => <li key={usuario.id}>{usuario.nombre}</li>)}
// Por cada usuario en el array, crea un <li> con su nombre
// key={usuario.id} es obligatorio para que React identifique cada elemento

// Manejar eventos
<button onClick={() => setContador(contador + 1)}>Incrementar</button>
```

**"¿Alguna duda sobre estos conceptos antes de avanzar al siguiente tema?"**

---

## 🎯 Lo que vamos a aprender hoy
1. **useEffect** - Para hacer cosas cuando el componente cambia
2. **useContext** - Para compartir información entre páginas
3. **Formularios** - Para que los usuarios escriban datos
4. **Rutas** - Para tener varias páginas en tu app
5. **Fetch** - Para traer información de internet

---

# 1. useEffect - "Haz algo cuando..."

## ¿Qué es useEffect?
Es como decirle a React: **"Cuando pase X cosa, haz Y"**

### ¿Por qué necesitamos useEffect?
Imagina que quieres:
- Cambiar el título de la página cuando el usuario haga algo
- Cargar datos cuando se abra una página
- Limpiar un timer cuando se cierre un componente
- Escuchar el scroll de la página

**El problema:** React no sabe cuándo hacer estas cosas automáticamente.
**La solución:** useEffect le dice a React exactamente cuándo hacerlas.

### ¿Cómo funciona?
React tiene un "ciclo de vida":
1. **Monta** el componente (lo muestra por primera vez)
2. **Actualiza** el componente (cuando cambia algo)
3. **Desmonta** el componente (cuando ya no se necesita)

useEffect nos permite "engancharnos" a estos momentos y decir:
- "Haz esto cuando se monte"
- "Haz esto cuando se actualice"
- "Haz esto antes de desmontarlo"

### Instalación:
```bash
npm install react-router-dom
```

## Formulario completo con validación:
```jsx
import React, { useState, useEffect } from 'react';

function Contador() {
  const [numero, setNumero] = useState(0);

  // Esto se ejecuta cada vez que cambia 'numero'
  useEffect(() => {
    document.title = `Tienes ${numero} clicks`;
  }, [numero]);

  return (
    <div>
      <h1>Número: {numero}</h1>
      <button onClick={() => setNumero(numero + 1)}>
        Hacer click
      </button>
    </div>
  );
}
```

**¿Qué hace?** Cada vez que haces click, cambia el título de la pestaña del navegador.

### Los 3 casos más comunes:

```jsx
// 1. Hacer algo UNA SOLA VEZ cuando inicia el componente
useEffect(() => {
  console.log("¡Hola! El componente arrancó");
}, []); // Array vacío = solo una vez

// 2. Hacer algo CADA VEZ que cambia algo específico
useEffect(() => {
  console.log("El contador cambió");
}, [contador]); // Solo cuando 'contador' cambia

// 3. Hacer algo SIEMPRE que el componente se actualiza
useEffect(() => {
  console.log("Algo cambió");
}); // Sin array = siempre
```

---

# 2. useContext - "Compartir datos fácil"

## ¿Qué es Context?
Context es como tener un **almacén global** en tu aplicación donde puedes guardar información que necesitas en muchos lugares.

## El problema sin Context:
Imagina que tienes el nombre del usuario en la página principal, pero lo necesitas en 5 componentes diferentes. Tendrías que pasarlo de padre a hijo, a nieto, etc.

Esto se llama "prop drilling" y se ve así:
```jsx
// 😵 Prop drilling - pasando datos por muchos niveles
App → Header → Navigation → UserMenu → UserName
```

**Problemas del prop drilling:**
- Muchos componentes intermedios reciben props que no usan
- Si cambias algo, tienes que actualizar muchos archivos
- Se vuelve confuso y difícil de mantener

## La solución con Context:
Es como tener una **caja común** donde todos pueden tomar lo que necesiten directamente.

```jsx
// 😊 Con Context - acceso directo
App (tiene los datos) ← Context → UserName (usa los datos)
```

### ¿Cuándo usar Context?
**Úsalo para:**
- Información del usuario (nombre, foto, permisos)
- Tema de la aplicación (modo oscuro/claro)
- Idioma de la aplicación
- Configuraciones globales

**NO lo uses para:**
- Estados que solo necesita un componente
- Datos que cambian muy frecuentemente
- Estados locales de formularios

### Ejemplo práctico:
```jsx
import React, { createContext, useContext, useState } from 'react';

// 1. Crear la "caja común"
const UsuarioContext = createContext();

// 2. Componente que guarda los datos
function App() {
  const [usuario, setUsuario] = useState("Juan");

  return (
    <UsuarioContext.Provider value={{ usuario, setUsuario }}>
      <Header />
      <Perfil />
    </UsuarioContext.Provider>
  );
}

// 3. Componentes que usan los datos
function Header() {
  const { usuario } = useContext(UsuarioContext);
  return <h1>Bienvenido, {usuario}!</h1>;
}

function Perfil() {
  const { usuario, setUsuario } = useContext(UsuarioContext);
  
  return (
    <div>
      <p>Tu nombre: {usuario}</p>
      <button onClick={() => setUsuario("María")}>
        Cambiar a María
      </button>
    </div>
  );
}
```

**¿Qué hace?** Tanto Header como Perfil pueden usar y cambiar el nombre del usuario sin complicaciones.

---

# 3. Formularios - "Capturar lo que escribe el usuario"

## ¿Por qué son importantes los formularios?
Los formularios son la forma principal en que los usuarios interactúan con tu aplicación:
- Registro de usuarios
- Login
- Búsquedas
- Comentarios
- Configuraciones

## Componentes Controlados vs No Controlados

### Componentes NO Controlados (❌ No recomendado)
El HTML maneja el estado del input:
```jsx
// React no sabe qué hay en el input
<input type="text" />
```

### Componentes Controlados (✅ Recomendado)
React maneja el estado del input:
```jsx
// React siempre sabe qué hay en el input
const [valor, setValor] = useState("");
<input value={valor} onChange={(e) => setValor(e.target.value)} />
```

**¿Por qué controlados?**
- React siempre sabe el valor actual
- Puedes validar en tiempo real
- Puedes formatear la entrada
- Puedes resetear el formulario fácilmente
- Es más predecible

## Estados Derivados
Son valores que se calculan automáticamente basados en otros estados:

```jsx
const [nombre, setNombre] = useState("");
const [email, setEmail] = useState("");

// Estados derivados - se calculan automáticamente
const formularioCompleto = nombre && email;
const mensaje = nombre ? `Hola ${nombre}` : "Escribe tu nombre";
const puedeEnviar = formularioCompleto && email.includes("@");
```

**Ventajas:**
- No necesitas useState adicionales
- Se actualizan automáticamente
- Menos posibilidad de errores
```jsx
function MiFormulario() {
  const [nombre, setNombre] = useState("");

  return (
    <div>
      <input 
        type="text"
        value={nombre}
        onChange={(e) => setNombre(e.target.value)}
        placeholder="Escribe tu nombre"
      />
      <p>Hola, {nombre}!</p>
    </div>
  );
}
```

## Lo básico: un input controlado

```jsx
function MiFormulario() {
  const [nombre, setNombre] = useState("");

  return (
    <div>
      <input 
        type="text"
        value={nombre}
        onChange={(e) => setNombre(e.target.value)}
        placeholder="Escribe tu nombre"
      />
      <p>Hola, {nombre}!</p>
    </div>
  );
}
```
```jsx
function FormularioRegistro() {
  const [datos, setDatos] = useState({
    nombre: "",
    email: ""
  });
  const [error, setError] = useState("");

  const manejarCambio = (e) => {
    const { name, value } = e.target;
    setDatos({
      ...datos,
      [name]: value
    });
  };

  const enviarFormulario = (e) => {
    e.preventDefault(); // Evita que la página se recargue
    
    if (!datos.nombre || !datos.email) {
      setError("Por favor completa todos los campos");
      return;
    }
    
    setError("");
    alert(`Registro exitoso para ${datos.nombre}`);
  };

  return (
    <form onSubmit={enviarFormulario}>
      <div>
        <input
          name="nombre"
          value={datos.nombre}
          onChange={manejarCambio}
          placeholder="Tu nombre"
        />
      </div>
      
      <div>
        <input
          name="email"
          type="email"
          value={datos.email}
          onChange={manejarCambio}
          placeholder="Tu email"
        />
      </div>

      {error && <p style={{color: 'red'}}>{error}</p>}
      
      <button type="submit">Registrarse</button>
    </form>
  );
}
```

---

# 4. React Router - "Tener varias páginas"

## ¿Qué es React Router?
React por defecto crea aplicaciones de "una sola página" (SPA - Single Page Application). React Router te permite simular que tienes múltiples páginas.

### SPA vs Páginas Tradicionales

**Páginas tradicionales:**
- Cada URL es un archivo HTML diferente
- Al hacer click, el navegador carga una página nueva completa
- La página "parpadea" al cargar

**SPA con React Router:**
- Todo es el mismo archivo HTML
- JavaScript cambia el contenido según la URL
- No hay "parpadeo", es más rápido y fluido

### Conceptos clave

**BrowserRouter:** Es el contenedor principal que habilita el routing en tu app.

**Routes:** Define todas las rutas posibles de tu aplicación.

**Route:** Cada ruta individual que conecta una URL con un componente.

**Link:** Como un `<a>` pero para SPAs - no recarga la página.

### ¿Por qué usar React Router?
- **URLs amigables:** `/productos/123` en lugar de `/#productos`
- **Botón atrás funciona:** El usuario puede navegar con el navegador
- **Compartir enlaces:** Las URLs se pueden guardar y compartir
- **SEO:** Los buscadores pueden indexar las páginas
- **Organización:** Cada "página" es un componente separado

### Tipos de rutas comunes:

```jsx
// Ruta fija
<Route path="/contacto" element={<Contacto />} />

// Ruta con parámetro
<Route path="/usuario/:id" element={<Usuario />} />

// Ruta catch-all (para 404)
<Route path="*" element={<NoEncontrado />} />
```

## Ejemplo súper simple:
```jsx
import { BrowserRouter, Routes, Route, Link } from 'react-router-dom';

// Páginas de tu app
function Inicio() {
  return <h1>Esta es la página de inicio</h1>;
}

function Contacto() {
  return <h1>Esta es la página de contacto</h1>;
}

function App() {
  return (
    <BrowserRouter>
      {/* Menú de navegación */}
      <nav>
        <Link to="/">Inicio</Link> | 
        <Link to="/contacto">Contacto</Link>
      </nav>

      {/* Aquí se muestran las páginas */}
      <Routes>
        <Route path="/" element={<Inicio />} />
        <Route path="/contacto" element={<Contacto />} />
      </Routes>
    </BrowserRouter>
  );
}
```

### Ejemplo súper simple:
```jsx
import { useParams } from 'react-router-dom';

function PerfilUsuario() {
  const { id } = useParams(); // Captura el ID de la URL
  
  return <h1>Perfil del usuario #{id}</h1>;
}

// En las rutas:
<Route path="/usuario/:id" element={<PerfilUsuario />} />

// Link que lleva a esa página:
<Link to="/usuario/123">Ver usuario 123</Link>
```

---

# 5. Fetch - "Traer datos de internet"

## ¿Qué es Fetch?
Fetch es la forma moderna de hacer peticiones HTTP en JavaScript. Es como "pedirle" datos a un servidor.

### ¿Por qué necesitamos Fetch?
Las aplicaciones modernas no tienen todos los datos guardados localmente. Necesitan:
- Mostrar información de usuarios
- Cargar productos de una tienda
- Enviar formularios al servidor
- Actualizar datos en tiempo real

### ¿Cómo funciona una petición HTTP?

1. **Tu app hace una petición:** "Oye servidor, dame la lista de usuarios"
2. **El servidor procesa:** Busca en la base de datos
3. **El servidor responde:** "Aquí tienes los usuarios en formato JSON"
4. **Tu app usa los datos:** Los muestra en pantalla

### Estados de una petición

Toda petición pasa por estos estados:

1. **Loading (Cargando):** La petición se está ejecutando
2. **Success (Éxito):** La petición fue exitosa, tienes datos
3. **Error:** Algo salió mal (no hay internet, servidor caído, etc.)

**¿Por qué manejar estos estados?**
- **UX mejor:** El usuario sabe qué está pasando
- **No errores visuales:** No mostrar datos que no existen
- **Manejo de problemas:** Qué hacer si falla la conexión

### Fetch vs useState vs useEffect

```jsx
// useState: Para guardar los datos que llegan
const [datos, setDatos] = useState(null);
const [cargando, setCargando] = useState(true);
const [error, setError] = useState(null);

// useEffect: Para ejecutar fetch cuando se monta el componente
useEffect(() => {
  // fetch: Para pedir los datos al servidor
  fetch('https://api.ejemplo.com/datos')
}, []);
```

### APIs públicas para practicar
- **JSONPlaceholder:** https://jsonplaceholder.typicode.com/
  - Usuarios, posts, comentarios falsos
- **OpenWeatherMap:** Datos del clima
- **Rick and Morty API:** Personajes de la serie
- **PokeAPI:** Información de Pokémon
```jsx
function ListaUsuarios() {
  const [usuarios, setUsuarios] = useState([]);
  const [cargando, setCargando] = useState(true);

  useEffect(() => {
    // Función para traer datos
    const traerUsuarios = async () => {
      try {
        const respuesta = await fetch('https://jsonplaceholder.typicode.com/users');
        const datos = await respuesta.json();
        setUsuarios(datos);
      } catch (error) {
        console.log('Error:', error);
      } finally {
        setCargando(false);
      }
    };

    traerUsuarios();
  }, []); // Solo una vez cuando inicia

  if (cargando) {
    return <p>Cargando usuarios...</p>;
  }

  return (
    <div>
      <h2>Lista de Usuarios</h2>
      {usuarios.map(usuario => (
        <div key={usuario.id}>
          <h3>{usuario.name}</h3>
          <p>{usuario.email}</p>
        </div>
      ))}
    </div>
  );
}
```

## El concepto básico:
```jsx
// Hook que puedes usar en cualquier componente
function useDatos(url) {
  const [datos, setDatos] = useState(null);
  const [cargando, setCargando] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const traerDatos = async () => {
      try {
        const respuesta = await fetch(url);
        const resultado = await respuesta.json();
        setDatos(resultado);
      } catch (err) {
        setError('Algo salió mal');
      } finally {
        setCargando(false);
      }
    };

    traerDatos();
  }, [url]);

  return { datos, cargando, error };
}

// Úsalo así:
function MiComponente() {
  const { datos, cargando, error } = useDatos('https://api.ejemplo.com/datos');

  if (cargando) return <p>Cargando...</p>;
  if (error) return <p>{error}</p>;

  return <div>{/* Mostrar datos */}</div>;
}
```

---

### Hook personalizado (para reutilizar):
## Lista de Tareas que todos entienden

```jsx
import React, { useState, useEffect, createContext, useContext } from 'react';
import { BrowserRouter, Routes, Route, Link } from 'react-router-dom';

// Context para compartir las tareas
const TareasContext = createContext();

function App() {
  const [tareas, setTareas] = useState([]);

  // Agregar nueva tarea
  const agregarTarea = (texto) => {
    const nuevaTarea = {
      id: Date.now(),
      texto: texto,
      completada: false
    };
    setTareas([...tareas, nuevaTarea]);
  };

  // Marcar tarea como completada
  const completarTarea = (id) => {
    setTareas(tareas.map(tarea => 
      tarea.id === id 
        ? { ...tarea, completada: !tarea.completada }
        : tarea
    ));
  };

  return (
    <BrowserRouter>
      <TareasContext.Provider value={{ tareas, agregarTarea, completarTarea }}>
        <div>
          <nav>
            <Link to="/">Inicio</Link> | 
            <Link to="/tareas">Mis Tareas</Link>
          </nav>

          <Routes>
            <Route path="/" element={<Inicio />} />
            <Route path="/tareas" element={<PaginaTareas />} />
          </Routes>
        </div>
      </TareasContext.Provider>
    </BrowserRouter>
  );
}

function Inicio() {
  const { tareas } = useContext(TareasContext);
  const completadas = tareas.filter(t => t.completada).length;

  return (
    <div>
      <h1>¡Bienvenido!</h1>
      <p>Tienes {completadas} tareas completadas de {tareas.length}</p>
    </div>
  );
}

function PaginaTareas() {
  const { tareas, agregarTarea, completarTarea } = useContext(TareasContext);
  const [nuevaTarea, setNuevaTarea] = useState("");

  const manejarEnvio = (e) => {
    e.preventDefault();
    if (nuevaTarea.trim()) {
      agregarTarea(nuevaTarea);
      setNuevaTarea("");
    }
  };

  return (
    <div>
      <h1>Mis Tareas</h1>
      
      <form onSubmit={manejarEnvio}>
        <input
          value={nuevaTarea}
          onChange={(e) => setNuevaTarea(e.target.value)}
          placeholder="Nueva tarea..."
        />
        <button type="submit">Agregar</button>
      </form>

      <ul>
        {tareas.map(tarea => (
          <li key={tarea.id}>
            <input
              type="checkbox"
              checked={tarea.completada}
              onChange={() => completarTarea(tarea.id)}
            />
            <span style={{
              textDecoration: tarea.completada ? 'line-through' : 'none'
            }}>
              {tarea.texto}
            </span>
          </li>
        ))}
      </ul>
    </div>
  );
}

export default App;
```

---

# 📝 Resumen para recordar

## useEffect
- **"Cuando pase algo, haz esto"**
- `[]` = solo una vez
- `[variable]` = cuando cambie esa variable

## useContext  
- **"Caja común para compartir datos"**
- Crear context → Provider → useContext

## Formularios
- **"Capturar lo que escribe el usuario"**
- `value` + `onChange` = input controlado

## Router
- **"Varias páginas en tu app"**
- BrowserRouter → Routes → Route

## Fetch
- **"Traer datos de internet"**
- useEffect + fetch + useState para loading

---