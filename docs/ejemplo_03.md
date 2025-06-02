
# 💻 Ejemplo del mundo real: Lista de tareas dinámica en React (`useState` + eventos)

Este ejemplo práctico implementa una aplicación React completa con `useState` y eventos, aplicando los conceptos clave vistos en la Hora 3. Es una base sólida para comprender cómo React maneja datos internos, entradas del usuario y re-renderizado de la interfaz de forma declarativa.

---

## 🎯 Funcionalidades implementadas

- Ingreso de texto por parte del usuario mediante un formulario controlado.
- Gestión de estado para almacenar y renderizar tareas.
- Comunicación entre componentes padre e hijo a través de `props`.
- Actualización automática de la interfaz sin recargas.

---

## 📁 Estructura del proyecto

```plaintext
tarea-react/
├── src/
│   ├── App.jsx
│   └── components/
│       ├── TaskForm.jsx
│       └── TaskItem.jsx
```

---

## 📄 App.jsx

El componente principal `App` mantiene el estado global de la lista de tareas. Define la función `agregarTarea` que será pasada a `TaskForm`.

```jsx
import { useState } from 'react'
import TaskForm from './components/TaskForm'
import TaskItem from './components/TaskItem'

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
    <div style={{ maxWidth: "600px", margin: "0 auto" }}>
      <h1>📝 Lista de Tareas</h1>
      <TaskForm onAdd={agregarTarea} />
      <ul>
        {tareas.length === 0 ? (
          <p>No hay tareas todavía. ¡Agrega una!</p>
        ) : (
          tareas.map((t) => (
            <TaskItem key={t.id} titulo={t.titulo} completado={t.completado} />
          ))
        )}
      </ul>
    </div>
  )
}

export default App
```

---

## 🧠 Explicación del flujo

- `useState([])` inicializa la lista de tareas como un array vacío.
- La función `agregarTarea` crea una nueva tarea y actualiza el estado usando spread operator (`...`).
- Al cambiar el estado, React vuelve a renderizar el componente y muestra la nueva tarea.

---

## 📄 TaskForm.jsx

Este componente gestiona el formulario. Internamente maneja el estado del input con `useState`. Al enviar el formulario, llama a `onAdd` (recibido como prop desde `App`).

```jsx
import { useState } from 'react'

function TaskForm({ onAdd }) {
  const [titulo, setTitulo] = useState("")

  const handleSubmit = (e) => {
    e.preventDefault()
    const valor = titulo.trim()
    if (valor === "") return
    onAdd(valor)
    setTitulo("")
  }

  return (
    <form onSubmit={handleSubmit} style={{ marginBottom: "1rem" }}>
      <input
        type="text"
        value={titulo}
        onChange={(e) => setTitulo(e.target.value)}
        placeholder="Nueva tarea"
        style={{ padding: "0.5rem", width: "80%" }}
      />
      <button type="submit" style={{ padding: "0.5rem" }}>Agregar</button>
    </form>
  )
}

export default TaskForm
```

---

## 📄 TaskItem.jsx

Este componente muestra una tarea individual. Por ahora es solo visual, pero en la siguiente hora se expandirá para incluir acciones como completar o eliminar.

```jsx
function TaskItem({ titulo, completado }) {
  return (
    <li style={{ marginBottom: "0.5rem" }}>
      <span style={{
        textDecoration: completado ? "line-through" : "none",
        fontSize: "1.1rem"
      }}>
        {titulo}
      </span>
    </li>
  )
}

export default TaskItem
```

---

## ✅ Resultado esperado

- El usuario puede escribir una tarea.
- Al presionar "Agregar", se inserta en la lista.
- Si no hay tareas, aparece un mensaje indicando que la lista está vacía.
- Cada acción se refleja al instante gracias al estado de React.

---

## 🔁 Recomendaciones para extensión en clase

- Agregar `console.log(tareas)` para mostrar cómo cambia el estado.
- Usar herramientas como React Developer Tools para observar el flujo de estado.
- Discutir el patrón de lifting state up aplicado entre `TaskForm` y `App`.

---

> Esta implementación sirve como puente hacia la Hora 4, donde agregaremos funcionalidades como marcar tareas como completadas, eliminarlas y gestionar el estado visual de la interfaz de manera más robusta.
