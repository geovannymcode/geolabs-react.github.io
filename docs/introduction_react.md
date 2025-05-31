
# Introducción a React, JSX y Componentes

## 🎯 Objetivo de esta hora

Familiarizarse con los fundamentos esenciales de React, incluyendo el uso de JSX, la construcción de componentes funcionales y el paso de datos mediante `props`. Al finalizar esta hora, tendrás una comprensión clara de cómo estructurar una aplicación React sencilla y cómo renderizar componentes reutilizables en la interfaz de usuario.

---

## 🧠 Conceptos clave

| Concepto           | Explicación detallada                                                                                       |
|--------------------|-------------------------------------------------------------------------------------------------------------|
| **¿Qué es React?** | Es una biblioteca de JavaScript para construir interfaces de usuario. Su enfoque principal es la reutilización de componentes y la actualización eficiente del DOM usando un modelo declarativo. |
| **JSX**            | Es una sintaxis que permite escribir HTML dentro de JavaScript. Aunque parece HTML, es transformado por React en llamadas a `React.createElement`. |
| **Componentes**    | Son funciones de JavaScript que retornan JSX. Cada componente representa una unidad reutilizable de la interfaz. |
| **Props**          | Son los "parámetros" que se envían a los componentes. Permiten personalizar su comportamiento y contenido.   |
| **Vite**           | Es una herramienta de construcción rápida y moderna para proyectos frontend. Se usa aquí para crear la aplicación base de React con configuración mínima. |

---

## ⚙️ Crear la aplicación con Vite

Ejecuta los siguientes comandos en tu terminal para crear y correr tu proyecto React con Vite:

```bash
npm create vite@latest tarea-react -- --template react
cd tarea-react
npm install
npm run dev
```

Estructura del proyecto inicial:

```
tarea-react/
├── index.html
└── src/
    ├── main.jsx
    ├── App.jsx
    └── components/
        └── TaskItem.jsx
```

---

## 📄 `src/main.jsx`

Este es el **punto de entrada de la aplicación**. Aquí React se conecta con el HTML tradicional (`index.html`) e inyecta la app React dentro del elemento con ID `root`.

```jsx
import React from 'react'
import ReactDOM from 'react-dom/client'
import App from './App'

ReactDOM.createRoot(document.getElementById('root')).render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
)
```

> 🔍 `React.StrictMode` es una herramienta para detectar problemas potenciales durante el desarrollo.

---

## 📄 `src/App.jsx`

Este componente representa la **estructura principal de nuestra aplicación**. Por ahora contiene una lista de tareas simulada y muestra cada una mediante el componente `TaskItem`.

```jsx
import TaskItem from './components/TaskItem'

function App() {
  const tareas = [
    { id: 1, titulo: "Aprender React", completado: false },
    { id: 2, titulo: "Practicar ejercicios", completado: true }
  ]

  return (
    <div>
      <h1>📝 Lista de Tareas</h1>
      <ul>
        {tareas.map(t => (
          <TaskItem key={t.id} titulo={t.titulo} completado={t.completado} />
        ))}
      </ul>
    </div>
  )
}

export default App
```

> 💡 Se utiliza `.map()` para renderizar múltiples componentes `TaskItem` de forma dinámica.

---

## 📄 `src/components/TaskItem.jsx`

Este componente recibe dos `props` desde el componente padre: el `titulo` de la tarea y si está `completado`. Muestra el texto con un estilo tachado si está marcada como hecha.

```jsx
function TaskItem({ titulo, completado }) {
  return (
    <li>
      <span style={{ textDecoration: completado ? 'line-through' : 'none' }}>
        {titulo}
      </span>
    </li>
  )
}

export default TaskItem
```

---

## 🧠 Explicación pedagógica paso a paso

### Paso 1: JSX — HTML en JavaScript

JSX permite escribir estructuras similares a HTML dentro del código JavaScript. Esto mejora la claridad visual del código y permite componer interfaces complejas fácilmente.

```jsx
const elemento = <h2>Hola Mundo</h2>;
```

### Paso 2: Componentes funcionales

Un componente es simplemente una función que devuelve JSX:

```jsx
function Saludo({ nombre }) {
  return <p>Hola {nombre}</p>;
}
```

Este componente puede ser reutilizado con diferentes `props`.

### Paso 3: Props — parámetros visuales

Los `props` permiten enviar datos a los componentes. Esto hace que nuestros componentes sean reutilizables y modulares.

```jsx
<TaskItem titulo="Leer documentación" completado={true} />
```

---

## ✅ Resultado esperado

La aplicación mostrará una lista de tareas, cada una con su título. Las tareas completadas se verán con texto tachado. Aunque aún no hay lógica para agregar o modificar tareas, este es el primer paso para construir una app funcional con React.