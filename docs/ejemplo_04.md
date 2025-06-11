
# 💻 Ejemplo del mundo real: Filtrado dinámico de tareas completadas

En esta sección, extenderemos la aplicación de tareas para permitir el **filtrado condicional** de elementos, permitiendo a los usuarios alternar entre ver todas las tareas o solo aquellas que están pendientes de completar.

---

## ✅ ¿Qué aprenderá el estudiante?

- Usar `useState` para manejar un **estado booleano de visibilidad**.
- Aplicar lógica condicional para **filtrar listas** antes de renderizarlas.
- Manejar eventos de cambio (`onChange`) con checkboxes para capturar las decisiones del usuario.

---

## 📁 Estructura del proyecto

```
tarea-react/
├── src/
│   ├── App.jsx
│   └── components/
│       ├── TaskItem.jsx
│       ├── TaskForm.jsx
│       └── TaskFilter.jsx
```

---

## 📄 `TaskFilter.jsx`

Un componente que permite al usuario **activar o desactivar el filtro** de tareas completadas mediante un checkbox.

```jsx
function TaskFilter({ soloPendientes, onToggle }) {
  return (
    <div style={{ marginBottom: '1rem' }}>
      <label>
        <input
          type="checkbox"
          checked={soloPendientes}
          onChange={(e) => onToggle(e.target.checked)}
        />
        Mostrar solo tareas pendientes
      </label>
    </div>
  )
}

export default TaskFilter
```

---

## 📄 `App.jsx`

Actualizamos este componente para incluir:

- Estado adicional `soloPendientes`.
- Función `toggleFiltro` que cambia el filtro.
- Lógica para renderizar las tareas de forma condicional.

```jsx
import { useState } from 'react'
import TaskItem from './components/TaskItem'
import TaskForm from './components/TaskForm'
import TaskFilter from './components/TaskFilter'

function App() {
  const [tareas, setTareas] = useState([])
  const [soloPendientes, setSoloPendientes] = useState(false)

  const agregarTarea = (titulo) => {
    const nuevaTarea = {
      id: Date.now(),
      titulo,
      completado: false
    }
    setTareas([...tareas, nuevaTarea])
  }

  const toggleFiltro = (activo) => {
    setSoloPendientes(activo)
  }

  const tareasFiltradas = soloPendientes
    ? tareas.filter(t => !t.completado)
    : tareas

  return (
    <div>
      <h1>📝 Lista de Tareas</h1>
      <TaskForm onAdd={agregarTarea} />
      <TaskFilter soloPendientes={soloPendientes} onToggle={toggleFiltro} />
      {tareasFiltradas.length === 0 ? (
        <p>No hay tareas para mostrar</p>
      ) : (
        <ul>
          {tareasFiltradas.map(t => (
            <TaskItem
              key={t.id}
              titulo={t.titulo}
              completado={t.completado}
            />
          ))}
        </ul>
      )}
    </div>
  )
}

export default App
```

---

## 📄 `TaskItem.jsx`

Agregamos la funcionalidad de marcar una tarea como completada directamente desde la lista, simulando un caso realista.

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

> **Nota:** Si deseas permitir marcar tareas como completadas, puedes añadir un `onClick` o checkbox aquí también.

---

## 🧪 Ejercicio propuesto resuelto

**Objetivo:** implementar un filtro visual que permita mostrar solo tareas pendientes.

```jsx
// Paso 1: Estado booleano "soloPendientes"
// Paso 2: onChange del checkbox modifica ese estado
// Paso 3: Usamos .filter() antes de renderizar las tareas
```

---

## ✅ Resultado esperado

- El usuario puede agregar tareas nuevas.
- Puede activar un checkbox para mostrar solo tareas que aún no se han completado.
- Si no hay tareas que coincidan con el filtro, se muestra un mensaje de “No hay tareas para mostrar”.

---

## 🧠 Reflexión

Este ejemplo representa un **flujo típico en aplicaciones interactivas**: el usuario toma una acción, se actualiza el estado, y la interfaz responde dinámicamente. Además de reforzar `useState` y listas dinámicas, se introduce una lógica condicional simple pero poderosa — una habilidad esencial en React.

