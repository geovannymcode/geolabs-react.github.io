# 💻 Ejemplo del mundo real: Lista de tareas con React (versión estática)

En este ejemplo construiremos una versión **estática** de la aplicación de tareas utilizando **React y JSX**. Aunque no tendrá lógica de estado ni eventos, sentará las bases para las siguientes etapas. Aplicaremos:

✅ Componentes funcionales  
✅ Uso de `props`  
✅ JSX para construir interfaces  
✅ Buenas prácticas de estructura en React  

---

## 🛠️ Prerrequisitos

Asegúrate de tener instalado **Node.js**. Luego, abre una terminal y ejecuta:

```bash
npm create vite@latest tarea-react -- --template react
cd tarea-react
npm install
npm run dev
```

---

## 📁 Estructura del proyecto

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

## 📄 1. `index.html`

Archivo base ubicado en la raíz. Define el contenedor donde React renderiza la app.

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

🔎 **Nota:** El `div` con ID `root` es donde React montará la aplicación.

---

## 📄 2. `main.jsx`

Punto de entrada. Monta el componente principal (`App`) en el DOM.

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

💡 `React.StrictMode` ayuda a identificar problemas potenciales en desarrollo.

---

## 📄 3. `App.jsx`

Componente principal. Contiene una lista simulada de tareas y renderiza cada una con `TaskItem`.

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

Componente presentacional que muestra cada tarea y aplica estilo según si está completada.

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

## 🔍 Explicación técnica

### 📌 Uso de props

Los datos `titulo` y `completado` se pasan desde `App` al componente `TaskItem` como propiedades.

### 🔁 Renderizado dinámico

Se utiliza `.map()` sobre el arreglo de tareas para generar múltiples elementos `TaskItem`.

### 🎨 Estilo condicional

El texto se tacha si la tarea está marcada como completada (`completed: true`).

---

## ✅ Resultado esperado

Una interfaz básica con una lista de tareas. Las tareas completadas aparecerán con texto tachado. Esta es la base visual sobre la cual construiremos interactividad en la Hora 3 y Hora 4.

---

## 🧪 Ejercicio propuesto

Modifica `App.jsx` para que la lista de tareas se obtenga desde un archivo externo `data.js` que contenga el array `tareas`. Importa este array usando `import { tareas } from './data.js'`.