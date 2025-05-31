
# 💻 Ejemplo del mundo real: Lista de tareas dinámica con React (`useState` + eventos)

En esta sección crearemos una versión completamente funcional de nuestra aplicación de tareas. Usaremos **`useState` para manejar el estado**, y componentes reutilizables para capturar datos, renderizar la lista y actualizarla dinámicamente.

---

## ✅ ¿Qué podrá hacer el usuario?

- Escribir una tarea en el formulario.
- Agregarla a la lista sin recargar la página.
- Visualizar tareas agregadas al instante.

---

## 📁 Estructura del proyecto

```
tarea-react/
├── src/
│   ├── App.jsx
│   └── components/
│       ├── TaskItem.jsx
│       └── TaskForm.jsx
```

---

## 📄 App.jsx

Define el estado de la aplicación, contiene la lista de tareas y gestiona la función para agregar nuevas.

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

## 📄 TaskForm.jsx

Formulario controlado: el input es manejado con estado local y envía la tarea al componente padre (`App`).

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

## 📄 TaskItem.jsx

Componente presentacional que recibe `titulo` y `completado` como props y los muestra.

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

## 🧠 Explicación profesional

### 🟢 Estado con `useState`
- `tareas`: representa la lista completa de tareas.
- `setTareas`: función que actualiza ese estado de forma inmutable.

### 🔄 Comunicación entre componentes
- `App` pasa `agregarTarea` como prop a `TaskForm`.
- `TaskForm` lo ejecuta cuando el usuario envía el formulario.
- `App` actualiza su estado y React vuelve a renderizar.

### 🧩 Reactividad en acción
Cada vez que `setTareas` actualiza la lista, React detecta el cambio y renderiza de nuevo el componente `App`, reflejando instantáneamente los cambios en la UI.

---

## ✅ Resultado esperado

Una lista dinámica de tareas donde el usuario puede:

- Ingresar texto en un formulario.
- Ver nuevas tareas agregadas automáticamente a la interfaz.
- Tener una experiencia interactiva sin recargar la página.

---