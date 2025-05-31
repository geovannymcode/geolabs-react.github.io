
# 🕐 Hora 3: Manejo de estado con `useState` y eventos

## 🎯 Objetivo de esta hora

Aprender a manejar el estado interno de los componentes usando el hook `useState` y gestionar eventos de formulario en React. Implementaremos una versión dinámica de la lista de tareas, permitiendo al usuario **agregar nuevas tareas** y actualizar la interfaz en tiempo real.

---

## 🧠 Conceptos clave

| Concepto       | Explicación                                                                 |
|----------------|------------------------------------------------------------------------------|
| `useState`     | Hook de React que permite declarar y actualizar variables de estado.        |
| Eventos        | Mecanismo para capturar acciones del usuario (como clicks o envío de formularios). |
| Formularios    | Elementos que permiten al usuario introducir datos en la interfaz.          |
| Renderizado    | React renderiza automáticamente cuando cambia el estado.                    |

---

## 🧩 Estructura de archivos

```plaintext
tarea-react/
└── src/
    ├── App.jsx
    ├── components/
    │   ├── TaskItem.jsx
    │   └── TaskForm.jsx
```

---

## 📄 `App.jsx`

Este archivo define la lógica principal de la aplicación: estado de tareas, función para agregar nuevas tareas y renderizado del formulario + lista.

```jsx
import { useState } from 'react'
import TaskItem from './components/TaskItem'
import TaskForm from './components/TaskForm'

function App() {
  const [tareas, setTareas] = useState([])

  const agregarTarea = (titulo) => {
    const nuevaTarea = {
      id: Date.now(),
      titulo,
      completado: false
    }
    setTareas([...tareas, nuevaTarea])
  }

  return (
    <div>
      <h1>📝 Lista de Tareas</h1>
      <TaskForm onAdd={agregarTarea} />
      <ul>
        {tareas.map((t) => (
          <TaskItem key={t.id} titulo={t.titulo} completado={t.completado} />
        ))}
      </ul>
    </div>
  )
}

export default App
```

---

## 📄 `TaskForm.jsx`

Componente de formulario controlado: gestiona el valor del input y envía el dato al componente padre.

```jsx
import { useState } from 'react'

function TaskForm({ onAdd }) {
  const [titulo, setTitulo] = useState("")

  const handleSubmit = (e) => {
    e.preventDefault()
    if (titulo.trim() === "") return
    onAdd(titulo)
    setTitulo("")
  }

  return (
    <form onSubmit={handleSubmit}>
      <input
        type="text"
        value={titulo}
        onChange={(e) => setTitulo(e.target.value)}
        placeholder="Nueva tarea"
      />
      <button type="submit">Agregar</button>
    </form>
  )
}

export default TaskForm
```

---

## 📄 `TaskItem.jsx`

Sin cambios respecto a la Hora 2, aún muestra tareas recibidas por `props`.

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

### Paso 1: `useState`
- Declaramos el estado `tareas` con `useState([])` para guardar la lista.
- Usamos `setTareas` para actualizar la lista de forma reactiva.

### Paso 2: Manejo de eventos
- Usamos `onSubmit` en el formulario para capturar el evento cuando el usuario agrega una tarea.
- Usamos `onChange` para actualizar el valor del input conforme el usuario escribe.

### Paso 3: Comunicación padre-hijo
- El componente `App` pasa la función `agregarTarea` como `prop` al componente `TaskForm`.
- `TaskForm` la llama al enviar el formulario, pasando el valor ingresado.

### Paso 4: Actualización de interfaz
- Cada vez que se agrega una nueva tarea, React vuelve a renderizar el componente `App` y muestra la lista actualizada.

---

## ✅ Resultado esperado

- El usuario puede escribir una tarea en el formulario.
- Al hacer clic en “Agregar”, la tarea aparece en la lista.
- La interfaz se actualiza automáticamente sin recargar la página.

---