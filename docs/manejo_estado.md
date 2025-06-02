# Manejo de estado con `useState` y gestión de eventos en React

## 🎯 Objetivo de esta hora

Dominar el manejo del estado en React utilizando el hook `useState` y comprender cómo gestionar eventos del usuario (como formularios) para construir una interfaz interactiva. En esta sesión transformaremos la aplicación estática de tareas en una solución dinámica que reacciona a la entrada del usuario en tiempo real.

---

## 🧠 Conceptos clave

| Concepto       | Explicación profesional                                                                 |
|----------------|------------------------------------------------------------------------------------------|
| `useState`     | Hook fundamental de React que permite declarar variables de estado y actualizarlas dentro de componentes funcionales. |
| Eventos        | Permiten capturar y responder a acciones del usuario, como escribir en un campo de texto o enviar un formulario. |
| Formularios    | Estructuras HTML que permiten la entrada de datos. En React se controlan con `useState` para mantener sincronización entre la vista y el estado. |
| Re-renderizado | React vuelve a renderizar el componente cuando el estado cambia, garantizando que la UI siempre refleje el estado actual. |

---

## 🧩 Estructura del proyecto

```plaintext
tarea-react/
└── src/
    ├── App.jsx
    └── components/
        ├── TaskItem.jsx
        └── TaskForm.jsx
```

---

## 📄 `App.jsx` – Componente raíz

Este componente gestiona el estado de las tareas. Define la función `agregarTarea`, que permite insertar nuevas tareas en la lista, y delega responsabilidades a los componentes hijos (`TaskForm` y `TaskItem`).

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

## 📄 `TaskForm.jsx` – Componente controlado de entrada

Maneja internamente el valor del campo de entrada usando `useState` y envía la información al componente padre cuando se envía el formulario.

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

## 📄 `TaskItem.jsx` – Componente de presentación

Recibe los datos de la tarea como props (`titulo`, `completado`) y los renderiza. En esta etapa no tiene lógica interactiva, solo muestra el contenido.

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

### Paso 1: Declarar el estado con `useState`

El hook `useState([])` inicializa el estado de `tareas` como un array vacío. Cada vez que agregamos una nueva tarea, usamos `setTareas([...tareas, nuevaTarea])` para actualizar la lista sin mutar el estado original.

### Paso 2: Capturar eventos del formulario

Usamos `onSubmit` para interceptar el envío del formulario y evitar el comportamiento por defecto del navegador (recargar la página). Luego validamos que el input no esté vacío antes de enviar los datos al componente padre.

### Paso 3: Comunicación entre componentes

- `App` define la lógica de negocio (`agregarTarea`).
- `TaskForm` captura el texto ingresado por el usuario.
- A través de `props`, se conecta `TaskForm` con `App` y se desencadena la acción correspondiente.

### Paso 4: Actualización automática de la interfaz

Al ejecutar `setTareas`, React detecta que el estado cambió y vuelve a renderizar automáticamente los componentes afectados (en este caso, la lista de tareas).

---

## ✅ Resultado esperado

El usuario podrá:

- Ingresar una nueva tarea en un campo de texto.
- Hacer clic en “Agregar” para que la tarea se muestre en la lista.
- Observar que la interfaz responde inmediatamente, sin necesidad de recargar la página.

---

> Este ejercicio marca el inicio del desarrollo de una aplicación verdaderamente interactiva. En la siguiente hora incorporaremos funcionalidades adicionales como marcar tareas completadas, eliminar tareas y aplicar filtros visuales condicionales.
