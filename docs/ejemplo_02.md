# 💻 Ejemplo del mundo real: Lista de tareas con React (versión estática)

En este ejemplo vamos a construir una versión **estática** de la aplicación de tareas utilizando **React y JSX**. Esta versión no tendrá lógica de estado ni eventos aún; su objetivo es aplicar lo aprendido en la Hora 2:

✅ Componentes funcionales  
✅ Uso de `props`  
✅ JSX para construir interfaces  
✅ Buenas prácticas de estructura en React  

---

## 📁 Estructura del proyecto

```plaintext
tarea-react/
├── index.html
└── src/
    ├── main.jsx
    ├── App.jsx
    └── components/
        └── TaskItem.jsx
```

---

## 📄 1. `index.html`

Este archivo se encuentra en la raíz del proyecto (`tarea-react/`) y contiene el punto de montaje de la app.

```html
<!DOCTYPE html>
<html lang="es">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Lista de Tareas</title>
  </head>
  <body>
    <div id="root"></div>
    <script type="module" src="/src/main.jsx"></script>
  </body>
</html>
```

---

## 📄 2. `main.jsx`

Archivo que renderiza el componente raíz (`App`) dentro del elemento con ID `root`.

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

## 📄 3. `App.jsx`

Este es el componente principal. Aquí se define una lista de tareas simulada (como si viniera de una base de datos o API) y se renderiza dinámicamente con `.map()`.

```jsx
import TaskItem from './components/TaskItem'

function App() {
  const tareas = [
    { id: 1, titulo: "Estudiar React", completado: false },
    { id: 2, titulo: "Hacer ejercicio", completado: true },
    { id: 3, titulo: "Leer documentación", completado: false }
  ]

  return (
    <div>
      <h1>📝 Lista de Tareas</h1>
      <ul>
        {tareas.map((tarea) => (
          <TaskItem
            key={tarea.id}
            titulo={tarea.titulo}
            completado={tarea.completado}
          />
        ))}
      </ul>
    </div>
  )
}

export default App
```

---

## 📄 4. `TaskItem.jsx`

Este componente funcional recibe props (`titulo` y `completado`) y los usa para mostrar cada tarea. Aplica un estilo tachado si la tarea está completada.

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

### Paso 1: Estructura del proyecto
Creamos una carpeta `components` donde se colocarán los componentes reutilizables como `TaskItem`.

### Paso 2: Uso de props
Pasamos datos (`titulo` y `completado`) desde el componente padre (`App`) hacia el componente hijo (`TaskItem`) usando props.

### Paso 3: Uso de `.map()` en JSX
Recorremos un array de objetos para generar múltiples instancias del componente `TaskItem`. Esto es clave en React para renderizar listas.

### Paso 4: JSX con lógica condicional
Aplicamos una condición dentro del estilo para mostrar el texto tachado si la tarea está marcada como completada.

---

## ✅ Resultado esperado

Una interfaz básica en React que muestra una lista de tareas con texto normal o tachado según si están completadas. Aunque aún no es interactiva, representa la base de la aplicación que construiremos completamente en la siguiente hora con `useState`.

---
