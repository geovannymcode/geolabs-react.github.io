
# 🕐 Hora 2: Introducción a React + JSX + Componentes

## 🎯 Objetivo de esta hora

Comprender la estructura básica de una aplicación React, aprender cómo se escriben los componentes funcionales y cómo usar JSX para construir interfaces dinámicas.

---

## 🧠 Conceptos clave

| Tema               | Descripción                                                                 |
|--------------------|-----------------------------------------------------------------------------|
| ¿Qué es React?     | Librería declarativa para construir interfaces de usuario basada en componentes reutilizables. |
| JSX                | Sintaxis que combina JavaScript y HTML. Se transpila a `React.createElement`. |
| Componentes        | Funciones que retornan JSX y representan partes reutilizables de la UI.     |
| Props              | Parámetros que se pasan a los componentes para hacerlos dinámicos.          |
| Proyecto con Vite  | Crear una app React con una estructura moderna y rápida para desarrollo.    |

---

## 📁 Estructura inicial del proyecto con Vite

```bash
npm create vite@latest tarea-react -- --template react
cd tarea-react
npm install
npm run dev
```

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

## 📄 src/main.jsx

Este archivo es el punto de entrada de la aplicación. Renderiza el componente principal (`App`) en el DOM.

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

---

## 📄 src/App.jsx

Componente principal donde gestionaremos la lista de tareas. Usaremos `useState` y `props`.

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

---

## 📄 src/components/TaskItem.jsx

Componente que representa cada tarea. Recibe `titulo` y `completado` como props.

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

## 🧠 Explicación paso a paso

### 1. ¿Qué es JSX?
JSX permite escribir código que **parece HTML dentro de JavaScript**, pero en realidad se transforma en llamadas a `React.createElement`.

```jsx
const titulo = <h1>Hola Mundo</h1>
```

### 2. ¿Qué es un componente?
Una función que retorna JSX. Puede recibir `props` como parámetros:

```jsx
function Saludo({ nombre }) {
  return <p>Hola {nombre}</p>
}
```

### 3. ¿Qué son props?
Las props son como los "argumentos" que se pasan a los componentes para hacerlos reutilizables y dinámicos.

---

## ✅ Resultado esperado

La aplicación mostrará una lista de tareas, cada una representada por un componente `TaskItem`, con texto tachado si la tarea está completada.

---

> En la Hora 3 trabajaremos con `useState` para gestionar el estado dinámico y permitir que el usuario agregue nuevas tareas desde un formulario.

