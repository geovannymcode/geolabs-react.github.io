# 🚀 Introducción a React - Guía Súper Detallada para Principiantes

## 📋 Índice
1. [¿Qué es React?](#qué-es-react)
2. [Primer ejemplo: Hola Mundo](#primer-ejemplo-hola-mundo)
3. [JSX: La sintaxis especial de React](#jsx-javascript--xml)
4. [Creando tu primer proyecto React](#primer-proyecto-react)
5. [Componentes: Los bloques de construcción](#componentes)
6. [Estado: Haciendo tu app interactiva](#estado)
7. [Fragmentos: Agrupando elementos](#fragmentos)
8. [Estilos en React](#estilos-globales)

---

## 🤔 ¿Qué es React?

### Definición simple para principiantes

React es una **herramienta de JavaScript** que nos ayuda a crear interfaces de usuario (lo que ves en la pantalla) de manera más fácil y eficiente. Piensa en React como un "constructor de LEGO" para páginas web: te proporciona piezas reutilizables que puedes combinar para crear aplicaciones complejas.

### 🎯 Características clave explicadas

#### 1. **Declaratividad**
**¿Qué significa?** En lugar de decirle al navegador CÓMO hacer algo paso a paso, le dices QUÉ quieres que muestre.

**Analogía:** 
- **Imperativo** (JavaScript tradicional): "Ve a la cocina, abre el refrigerador, saca la leche, cierra el refrigerador, busca un vaso..."
- **Declarativo** (React): "Quiero un vaso de leche"

**Ejemplo práctico:**
```javascript
// Imperativo (JavaScript tradicional)
const div = document.createElement('div');
div.innerHTML = 'Hola';
div.style.color = 'blue';
document.body.appendChild(div);

// Declarativo (React)
return <div style={{color: 'blue'}}>Hola</div>
```

#### 2. **Actualizaciones selectivas del DOM**
**¿Qué es el DOM?** Document Object Model - es la representación de tu página HTML que el navegador usa.

**El problema:** Actualizar el DOM es LENTO. Si cambias todo cada vez que algo pequeño cambia, tu app será lenta.

**La solución de React:** Solo actualiza las partes que realmente cambiaron.

**Analogía:** Es como editar un documento - no reescribes todo el documento cuando corriges una palabra, solo cambias esa palabra.

#### 3. **Flujo de datos unidireccional**
**¿Qué significa?** Los datos fluyen en una sola dirección: de arriba hacia abajo (de componentes padres a hijos).

**Visualización:**
```
App (tiene los datos principales)
  ↓
Header (recibe datos del App)
  ↓
Menu (recibe datos del Header)
```

**Beneficio:** Es más fácil entender de dónde vienen los datos y rastrear errores.

#### 4. **Componentes reutilizables**
**¿Qué son?** Piezas de código que puedes usar múltiples veces, como moldes.

**Ejemplo del mundo real:** Un botón que usas en toda tu app:
```jsx
// Defines una vez
const BotonAzul = ({texto}) => (
  <button style={{background: 'blue'}}>{texto}</button>
);

// Usas muchas veces
<BotonAzul texto="Guardar" />
<BotonAzul texto="Cancelar" />
<BotonAzul texto="Enviar" />
```

### 🌐 Versatilidad de React

React no solo sirve para páginas web. Con el mismo conocimiento puedes crear:

- **Páginas web** (React DOM)
- **Apps móviles** (React Native) - Instagram, Facebook
- **Apps de escritorio** (Electron) - Discord, VS Code
- **Aplicaciones del servidor** (Next.js)

---

## 👋 Primer ejemplo: Hola Mundo

### Código básico de React

```jsx
const divRoot = document.querySelector('#root');

ReactDOM.render(
  <h1>Hola Mundo</h1>,
  divRoot
);
```

### 🔍 Explicación línea por línea

#### Línea 1: `const divRoot = document.querySelector('#root');`
- **¿Qué hace?** Busca un elemento HTML con `id="root"`
- **¿Por qué?** React necesita un lugar donde "vivir" en tu página HTML
- **Analogía:** Es como decirle a React "aquí es donde vas a construir todo"

#### Línea 3-6: `ReactDOM.render(...)`
- **ReactDOM**: Es el puente entre React y el navegador
- **render**: Significa "mostrar" o "dibujar"
- **Primer parámetro** `<h1>Hola Mundo</h1>`: QUÉ mostrar
- **Segundo parámetro** `divRoot`: DÓNDE mostrarlo

### 📦 HTML mínimo necesario

```html
<!DOCTYPE html>
<html>
<head>
    <title>Mi App React</title>
</head>
<body>
    <!-- React va a vivir aquí -->
    <div id="root"></div>
    
    <!-- Scripts de React -->
    <script src="react.js"></script>
    <script src="react-dom.js"></script>
</body>
</html>
```

---

## 🎨 JSX: JavaScript + XML

### ¿Qué es JSX?

JSX es una **sintaxis especial** que te permite escribir algo que parece HTML dentro de JavaScript. NO es HTML real, es azúcar sintáctico que se transforma en JavaScript.

### Comparación: JSX vs JavaScript puro

**Con JSX (fácil de leer):**
```jsx
const elemento = (
  <div className="contenedor">
    <h1>Título</h1>
    <p>Este es un párrafo</p>
  </div>
);
```

**Sin JSX (difícil de leer):**
```javascript
const elemento = React.createElement(
  'div',
  { className: 'contenedor' },
  React.createElement('h1', null, 'Título'),
  React.createElement('p', null, 'Este es un párrafo')
);
```

### 🎯 Reglas importantes de JSX

#### 1. **Las etiquetas deben cerrarse**
```jsx
// ❌ Mal
<img src="foto.jpg">
<br>

// ✅ Bien
<img src="foto.jpg" />
<br />
```

#### 2. **Usa `className` en lugar de `class`**
```jsx
// ❌ HTML normal
<div class="mi-clase">

// ✅ JSX
<div className="mi-clase">
```

#### 3. **JavaScript va entre llaves `{}`**
```jsx
const nombre = "Juan";
const edad = 25;

return (
  <div>
    <h1>Hola {nombre}</h1>
    <p>Tienes {edad} años</p>
    <p>En 5 años tendrás {edad + 5}</p>
  </div>
);
```

---

## 🛠️ Primer Proyecto React: Configuración Manual

### Estructura del proyecto explicada

```
01-intro-react/
├── index.html      # Página principal
├── src/           # Carpeta de código fuente
│   └── app.js     # Tu código React
```

### 📄 index.html 

```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8" />
    <title>React App</title>
</head>
<body>
    <!-- React necesita un contenedor vacío -->
    <div id="root"></div>

    <!-- 1. React: La librería principal -->
    <script src="https://unpkg.com/react@18/umd/react.development.js"></script>
    
    <!-- 2. ReactDOM: Para interactuar con el navegador -->
    <script src="https://unpkg.com/react-dom@18/umd/react-dom.development.js"></script>
    
    <!-- 3. Babel: Traduce JSX a JavaScript -->
    <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>

    <!-- 4. Tu código (nota el type="text/babel") -->
    <script type="text/babel">
        // Variables en React
        const productName = "Zapatos deportivos";
        const price = 99.99;
        
        // Componente React
        const ProductInfo = () => {
            return (
                <div>
                    <h1>Producto: {productName}</h1>
                    <p>Precio: ${price}</p>
                    <p>Con descuento: ${price * 0.9}</p>
                </div>
            );
        };
        
        // Renderizar en el DOM
        const root = document.getElementById('root');
        ReactDOM.render(<ProductInfo />, root);
    </script>
</body>
</html>
```

### 🔑 Conceptos clave del ejemplo

#### 1. **Las tres librerías necesarias**
- **React**: El núcleo, contiene la lógica de componentes
- **ReactDOM**: Conecta React con el navegador
- **Babel**: Traduce JSX a JavaScript que el navegador entiende

#### 2. **type="text/babel"**
Le dice al navegador que este script contiene JSX y necesita ser transformado por Babel.

#### 3. **Variables en JSX**
```jsx
const precio = 100;
<p>El precio es: {precio}</p>        // Muestra: El precio es: 100
<p>Con IVA: {precio * 1.21}</p>      // Muestra: Con IVA: 121
<p>Oferta: {precio > 50 ? 'Caro' : 'Barato'}</p>  // Muestra: Oferta: Caro
```

---

## 🧩 Componentes: Los bloques de construcción

### ¿Qué es un componente?

Un componente es una **pieza reutilizable de interfaz**. Piensa en ellos como funciones que devuelven HTML (JSX).

### Anatomía de un componente

```jsx
// 1. Definición del componente (es una función)
function MiComponente(props) {
    // 2. Lógica del componente (opcional)
    const saludo = "Hola " + props.nombre;
    
    // 3. Lo que muestra (return con JSX)
    return (
        <div>
            <h1>{saludo}</h1>
            <p>Bienvenido a React</p>
        </div>
    );
}

// 4. Uso del componente
<MiComponente nombre="Juan" />
```

### 📦 Tipos de componentes

#### 1. **Componente funcional (moderno)**
```jsx
const Saludo = ({ nombre }) => {
    return <h1>Hola {nombre}</h1>;
};

// Versión aún más corta
const Saludo = ({ nombre }) => <h1>Hola {nombre}</h1>;
```

#### 2. **Componente de clase (antiguo, menos común)**
```jsx
class Saludo extends React.Component {
    render() {
        return <h1>Hola {this.props.nombre}</h1>;
    }
}
```

### 🎯 Props: Pasando datos a componentes

**Props** (propiedades) son la forma de pasar información a un componente.

```jsx
// Definir componente que recibe props
const TarjetaProducto = ({ nombre, precio, imagen }) => {
    return (
        <div className="tarjeta">
            <img src={imagen} alt={nombre} />
            <h3>{nombre}</h3>
            <p>${precio}</p>
        </div>
    );
};

// Usar el componente con diferentes props
<TarjetaProducto 
    nombre="Laptop" 
    precio={1200} 
    imagen="laptop.jpg" 
/>

<TarjetaProducto 
    nombre="Mouse" 
    precio={25} 
    imagen="mouse.jpg" 
/>
```

### 🏗️ Composición de componentes

Los componentes pueden contener otros componentes:

```jsx
const App = () => {
    return (
        <div>
            <Header />
            <main>
                <Sidebar />
                <Content />
            </main>
            <Footer />
        </div>
    );
};
```

### 🖼️ Ejemplo Visual Real: Aplicación de Productos

![Estructura de componentes con navegación y renderizado dinámico](./files/imagen1.png)

#### **Imagen 1: Estructura de componentes con navegación**

La primera imagen muestra una aplicación React real con la siguiente estructura:

**Análisis de la interfaz:**

1. **Componente Principal (`ProductApp`)** - Contenedor rojo
   - Es el componente raíz que contiene toda la aplicación
   - Gestiona qué componente mostrar según la navegación

2. **Menú de Navegación (`MenuItem`)** - Items azules
   - `Inicio` - Muestra el feed principal
   - `Categorías` - Lista de categorías de productos
   - `Ofertas` - Productos en oferta
   - `ProductCard` - Componente de tarjeta de producto
   - `PersonForm` - Formulario de información personal

3. **Área de Contenido** - Área verde
   - Muestra el componente seleccionado
   - En este caso, muestra un feed tipo red social
   - Cada post es un componente reutilizable

**Código aproximado de esta estructura:**
```jsx
// ProductApp.jsx
const ProductApp = () => {
    const [vistaActual, setVistaActual] = useState('Inicio');
    
    return (
        <div className="product-app">
            <Menu onSeleccionar={setVistaActual} />
            <main className="contenido-principal">
                {vistaActual === 'Inicio' && <Feed />}
                {vistaActual === 'ProductCard' && <ProductCard />}
                {vistaActual === 'PersonForm' && <PersonForm />}
                {/* ... más vistas */}
            </main>
        </div>
    );
};

// Menu.jsx
const Menu = ({ onSeleccionar }) => {
    const opciones = ['Inicio', 'Categorías', 'Ofertas', 'ProductCard', 'PersonForm'];
    
    return (
        <nav className="menu">
            <h2>Menu</h2>
            {opciones.map(opcion => (
                <MenuItem 
                    key={opcion}
                    texto={opcion}
                    onClick={() => onSeleccionar(opcion)}
                />
            ))}
        </nav>
    );
};

// Feed.jsx (el área verde con posts)
const Feed = () => {
    const posts = [
        { id: 1, autor: 'John Smith', fecha: 'May 8', contenido: '...', likes: 1000 },
        { id: 2, autor: 'Abagail Libbie', fecha: 'May 3', contenido: '...', likes: 500 },
        // más posts...
    ];
    
    return (
        <div className="feed">
            {posts.map(post => (
                <Post key={post.id} {...post} />
            ))}
        </div>
    );
};
```

**Conceptos clave ilustrados:**
- **Navegación SPA** (Single Page Application): No recarga la página
- **Estado compartido**: `vistaActual` controla qué mostrar
- **Componentes reutilizables**: Cada `MenuItem` y `Post` es reutilizable
- **Props**: Pasar funciones (`onSeleccionar`) y datos

---

![Estado del componente: formulario dinámico](./files/imagen2.png)

#### **Imagen 2: Estado del formulario**

La segunda imagen muestra el componente `PersonForm` cuando está activo:

**Análisis del formulario:**

1. **Navegación actualizada**
   - `PersonForm` está resaltado (morado)
   - Indica que este componente está activo

2. **Formulario con múltiples campos**
   - First Name / Last Name
   - Birthday / Phone Number
   - Email / Occupation
   - Botones Save / Cancel

**Código del componente PersonForm con estado:**
```jsx
// PersonForm.jsx
const PersonForm = () => {
    // Estado para cada campo del formulario
    const [formData, setFormData] = useState({
        firstName: '',
        lastName: '',
        birthday: '',
        phoneNumber: '',
        email: '',
        occupation: ''
    });
    
    // Manejar cambios en los inputs
    const handleChange = (e) => {
        const { name, value } = e.target;
        setFormData(prevData => ({
            ...prevData,
            [name]: value
        }));
    };
    
    // Manejar envío del formulario
    const handleSubmit = (e) => {
        e.preventDefault();
        console.log('Datos guardados:', formData);
        // Aquí irían las acciones de guardado
    };
    
    const handleCancel = () => {
        // Limpiar formulario o navegar atrás
        setFormData({
            firstName: '',
            lastName: '',
            birthday: '',
            phoneNumber: '',
            email: '',
            occupation: ''
        });
    };
    
    return (
        <div className="person-form-container">
            <h2>Personal Information</h2>
            <form onSubmit={handleSubmit}>
                <div className="form-row">
                    <input
                        type="text"
                        name="firstName"
                        placeholder="First Name"
                        value={formData.firstName}
                        onChange={handleChange}
                    />
                    <input
                        type="text"
                        name="lastName"
                        placeholder="Last Name"
                        value={formData.lastName}
                        onChange={handleChange}
                    />
                </div>
                
                <div className="form-row">
                    <input
                        type="date"
                        name="birthday"
                        placeholder="Birthday"
                        value={formData.birthday}
                        onChange={handleChange}
                    />
                    <input
                        type="tel"
                        name="phoneNumber"
                        placeholder="Phone Number"
                        value={formData.phoneNumber}
                        onChange={handleChange}
                    />
                </div>
                
                <div className="form-row">
                    <input
                        type="email"
                        name="email"
                        placeholder="Email"
                        value={formData.email}
                        onChange={handleChange}
                    />
                    <input
                        type="text"
                        name="occupation"
                        placeholder="Occupation"
                        value={formData.occupation}
                        onChange={handleChange}
                    />
                </div>
                
                <div className="form-buttons">
                    <button type="submit" className="btn-save">
                        Save
                    </button>
                    <button type="button" onClick={handleCancel} className="btn-cancel">
                        Cancel
                    </button>
                </div>
            </form>
        </div>
    );
};
```

**Conceptos de estado ilustrados:**

1. **Estado del formulario vacío inicial**
```jsx
// Al cargar el componente
{
    firstName: '',
    lastName: '',
    birthday: '',
    phoneNumber: '',
    email: '',
    occupation: ''
}
```

2. **Estado mientras el usuario escribe**
```jsx
// Después de llenar algunos campos
{
    firstName: 'Juan',
    lastName: 'Pérez',
    birthday: '1990-05-15',
    phoneNumber: '555-0123',
    email: 'juan@email.com',
    occupation: 'Desarrollador'
}
```

3. **Flujo de actualización del estado**
```
Usuario escribe en input
        ↓
onChange se dispara
        ↓
handleChange se ejecuta
        ↓
setFormData actualiza el estado
        ↓
React re-renderiza el componente
        ↓
Input muestra el nuevo valor
```

**Características clave del formulario:**
- **Controlled inputs**: El valor viene del estado
- **Two-way binding**: Estado ↔ Input sincronizados
- **Single source of truth**: Todo el estado en un objeto
- **Inmutabilidad**: Usamos spread operator para actualizar

### 🎯 Resumen visual

Estas imágenes muestran perfectamente:
1. **Cómo los componentes se organizan** en una aplicación real
2. **Cómo funciona la navegación** sin recargar la página
3. **Cómo el estado controla** lo que se muestra
4. **Cómo los formularios manejan datos** en React

**Visualización de la jerarquía completa:**
```
ProductApp (componente raíz)
├── Menu (navegación lateral)
│   ├── MenuItem "Inicio"
│   ├── MenuItem "Categorías"
│   ├── MenuItem "Ofertas"
│   ├── MenuItem "ProductCard"
│   └── MenuItem "PersonForm"
└── ContenidoPrincipal (área dinámica)
    ├── Feed (cuando seleccionas "Inicio")
    │   ├── Post 1
    │   ├── Post 2
    │   └── ...más posts
    └── PersonForm (cuando seleccionas "PersonForm")
        ├── Inputs de formulario
        └── Botones de acción
```

---

## 💾 Estado: Haciendo tu app interactiva

### ¿Qué es el estado?

El **estado** es información que puede cambiar con el tiempo en tu componente. Cuando el estado cambia, React actualiza automáticamente lo que se muestra en pantalla.

### Ejemplo simple: Contador

```jsx
import { useState } from 'react';

const Contador = () => {
    // Declarar una variable de estado
    const [cuenta, setCuenta] = useState(0);
    
    return (
        <div>
            <h1>Contador: {cuenta}</h1>
            <button onClick={() => setCuenta(cuenta + 1)}>
                Incrementar
            </button>
            <button onClick={() => setCuenta(cuenta - 1)}>
                Decrementar
            </button>
        </div>
    );
};
```

### 🔍 Desglose del useState

```jsx
const [cuenta, setCuenta] = useState(0);
//     ↑         ↑              ↑
//   valor    función      valor inicial
//   actual   para cambiar
```

- **`cuenta`**: El valor actual del estado
- **`setCuenta`**: Función para actualizar el estado
- **`useState(0)`**: Hook que crea el estado con valor inicial 0

### 📝 Ejemplo más complejo: Formulario

```jsx
const FormularioContacto = () => {
    // Múltiples estados
    const [nombre, setNombre] = useState('');
    const [email, setEmail] = useState('');
    const [mensaje, setMensaje] = useState('');
    
    const handleSubmit = (e) => {
        e.preventDefault();
        console.log({ nombre, email, mensaje });
    };
    
    return (
        <form onSubmit={handleSubmit}>
            <input
                type="text"
                placeholder="Tu nombre"
                value={nombre}
                onChange={(e) => setNombre(e.target.value)}
            />
            
            <input
                type="email"
                placeholder="Tu email"
                value={email}
                onChange={(e) => setEmail(e.target.value)}
            />
            
            <textarea
                placeholder="Tu mensaje"
                value={mensaje}
                onChange={(e) => setMensaje(e.target.value)}
            />
            
            <button type="submit">Enviar</button>
            
            {/* Vista previa en tiempo real */}
            <div>
                <h3>Vista previa:</h3>
                <p>Nombre: {nombre}</p>
                <p>Email: {email}</p>
                <p>Mensaje: {mensaje}</p>
            </div>
        </form>
    );
};
```

### 🎯 Reglas del Estado

1. **Nunca modifiques el estado directamente**
```jsx
// ❌ MAL
cuenta = cuenta + 1;

// ✅ BIEN
setCuenta(cuenta + 1);
```

2. **El estado es asíncrono**
```jsx
setCuenta(cuenta + 1);
console.log(cuenta); // Todavía muestra el valor anterior
```

3. **Actualizaciones basadas en estado previo**
```jsx
// Si necesitas el valor anterior, usa una función
setCuenta(prevCuenta => prevCuenta + 1);
```

---

## 🎁 Fragmentos: Agrupando elementos sin divs extra

### El problema

React requiere que los componentes devuelvan UN solo elemento. Esto puede llevar a "div soup" (sopa de divs):

```jsx
// ❌ Esto da ERROR
const Componente = () => {
    return (
        <h1>Título</h1>
        <p>Párrafo</p>
    );
};

// 🤔 Funciona pero agrega un div innecesario
const Componente = () => {
    return (
        <div>  {/* div extra que no necesitamos */}
            <h1>Título</h1>
            <p>Párrafo</p>
        </div>
    );
};
```

### La solución: Fragments

#### Opción 1: Fragment explícito
```jsx
import { Fragment } from 'react';

const Componente = () => {
    return (
        <Fragment>
            <h1>Título</h1>
            <p>Párrafo</p>
        </Fragment>
    );
};
```

#### Opción 2: Sintaxis corta (recomendada)
```jsx
const Componente = () => {
    return (
        <>
            <h1>Título</h1>
            <p>Párrafo</p>
        </>
    );
};
```

### 🎯 ¿Por qué usar Fragments?

1. **DOM más limpio**: No agrega elementos HTML innecesarios
2. **Mejor rendimiento**: Menos nodos en el DOM
3. **CSS más fácil**: No interfiere con Flexbox o Grid

**Ejemplo práctico:**
```jsx
// Lista de definiciones sin divs extra
const Glosario = () => {
    return (
        <dl>
            <Termino />
            <Definicion />
        </dl>
    );
};

const Termino = () => (
    <>
        <dt>React</dt>
        <dt>React.js</dt>
    </>
);

const Definicion = () => (
    <dd>Una librería de JavaScript para construir interfaces</dd>
);
```

---

## 🎨 Estilos Globales en React

### Creando estilos globales

#### 1. Crear archivo CSS
```css
/* src/index.css */

/* Reset básico */
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

/* Estilos globales */
html, body {
    background-color: #21232a;
    color: white;
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
    font-size: 16px;
    line-height: 1.6;
}

/* Contenedor principal */
#root {
    min-height: 100vh;
    padding: 2rem;
}

/* Estilos reutilizables */
.contenedor {
    max-width: 1200px;
    margin: 0 auto;
}

.boton {
    background: #61dafb;
    color: #21232a;
    border: none;
    padding: 0.5rem 1rem;
    border-radius: 4px;
    cursor: pointer;
    font-size: 1rem;
}

.boton:hover {
    background: #4fc3f7;
}
```

#### 2. Importar en main.jsx
```jsx
// main.jsx o index.js
import React from 'react';
import ReactDOM from 'react-dom/client';
import './index.css';  // 👈 Importar estilos globales
import App from './App';

ReactDOM.createRoot(document.getElementById('root')).render(
    <React.StrictMode>
        <App />
    </React.StrictMode>
);
```

### 🎯 Diferentes formas de aplicar estilos en React

#### 1. **Clases CSS (className)**
```jsx
const Componente = () => (
    <div className="contenedor">
        <button className="boton boton-primario">
            Click me
        </button>
    </div>
);
```

#### 2. **Estilos inline**
```jsx
const Componente = () => (
    <div style={{ 
        backgroundColor: 'blue', 
        padding: '20px',
        borderRadius: '8px' 
    }}>
        Contenido
    </div>
);
```

#### 3. **CSS Modules (más avanzado)**
```css
/* Button.module.css */
.boton {
    background: blue;
    color: white;
}
```

```jsx
import styles from './Button.module.css';

const Button = () => (
    <button className={styles.boton}>Click</button>
);
```

### 📋 Mejores prácticas para estilos

1. **Usar rem en lugar de px para tamaños**
```css
/* Mejor escalabilidad */
font-size: 1.2rem;  /* en lugar de 19.2px */
padding: 1rem;      /* en lugar de 16px */
```

2. **Variables CSS para colores**
```css
:root {
    --color-primario: #61dafb;
    --color-fondo: #21232a;
    --color-texto: #ffffff;
}

body {
    background-color: var(--color-fondo);
    color: var(--color-texto);
}
```

3. **Mobile-first approach**
```css
/* Estilos para móvil primero */
.contenedor {
    padding: 1rem;
}

/* Luego ajustes para pantallas grandes */
@media (min-width: 768px) {
    .contenedor {
        padding: 2rem;
    }
}
```

---

## 🚀 Resumen y próximos pasos

### Lo que hemos aprendido:

1. **React** es una librería para crear interfaces de usuario
2. **JSX** nos permite escribir "HTML" en JavaScript
3. **Componentes** son bloques reutilizables de código
4. **Props** pasan datos a los componentes
5. **Estado** hace las apps interactivas
6. **Fragments** agrupan elementos sin divs extra
7. **Estilos** se pueden aplicar de varias formas


### 💡 Proyecto de práctica sugerido

Crea una app de lista de tareas con React que incluya:

- Componente de formulario para agregar tareas
- Lista de tareas con checkbox
- Contador de tareas pendientes
- Estilos personalizados
- Uso de estado para manejar las tareas