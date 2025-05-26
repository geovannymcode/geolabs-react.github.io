
# 💻 Ejemplo del mundo real: Lista de tareas (sin React)

En esta sección crearemos una aplicación sencilla utilizando solo **HTML y JavaScript moderno**, sin frameworks ni bibliotecas externas. Esta práctica tiene como objetivo aplicar los conceptos aprendidos en la Hora 1:

✅ Agregar una tarea  
✅ Ver tareas existentes  
✅ Marcar tareas como completadas  
✅ Filtrar tareas pendientes (al marcar con checkbox)

---

## 📁 Estructura básica de archivos

```plaintext
hora-1-js-modern/
├── index.html
└── app.js
```

---

## 📄 index.html

Este archivo define la estructura de la interfaz de usuario: título, formulario de entrada y lista de tareas.

```html
<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Gestor de Tareas</title>
</head>
<body>
  <h1>📝 Lista de Tareas</h1>
  <form id="taskForm">
    <input type="text" id="taskInput" placeholder="Nueva tarea..." required />
    <button type="submit">Agregar</button>
  </form>

  <ul id="taskList"></ul>

  <script src="app.js"></script>
</body>
</html>
```

### 🧠 Explicación:
- `<form>`: captura el texto de la nueva tarea.
- `<input>`: campo para escribir el título.
- `<ul>`: lista donde se mostrarán dinámicamente las tareas.
- `<script>`: enlaza el archivo JavaScript donde se encuentra la lógica.

---

## 📄 app.js

Este archivo contiene toda la lógica funcional de nuestra aplicación:

```js
const tasks = [] // Lista de tareas

const form = document.getElementById('taskForm')
const input = document.getElementById('taskInput')
const list = document.getElementById('taskList')

// Evento para manejar el envío del formulario
form.addEventListener('submit', (e) => {
  e.preventDefault()
  const title = input.value.trim()
  if (title !== '') {
    addTask(title)
    input.value = ''
  }
})

// Función para agregar una nueva tarea al array
function addTask(title) {
  const task = {
    id: Date.now(),
    title,
    completed: false
  }
  tasks.push(task)
  renderTasks()
}

// Función para marcar o desmarcar una tarea como completada
function toggleTask(id) {
  const task = tasks.find(t => t.id === id)
  if (task) task.completed = !task.completed
  renderTasks()
}

// Función para renderizar todas las tareas en pantalla
function renderTasks() {
  list.innerHTML = ''
  tasks.forEach(({ id, title, completed }) => {
    const li = document.createElement('li')
    li.innerHTML = `
      <input type="checkbox" ${completed ? 'checked' : ''} onchange="toggleTask(${id})" />
      <span style="text-decoration: ${completed ? 'line-through' : 'none'}">${title}</span>
    `
    list.appendChild(li)
  })
}
```

---

### 🧠 Explicación del código paso a paso:

#### 1. Variables iniciales
- `tasks`: array donde se guardan todas las tareas.
- `form`, `input`, `list`: referencias a elementos HTML.

#### 2. Manejo del formulario
- Escuchamos el evento `submit` del formulario.
- Evitamos que la página se recargue (`e.preventDefault()`).
- Si el campo no está vacío, se llama a `addTask()`.

#### 3. Agregar tarea
- Crea un nuevo objeto con `id`, `title` y `completed`.
- Se agrega al array `tasks`.
- Se llama a `renderTasks()` para actualizar la vista.

#### 4. Cambiar estado de la tarea
- `toggleTask(id)`: busca la tarea por ID y cambia su estado (`completed`).
- Llama a `renderTasks()` para reflejar el cambio.

#### 5. Renderizar tareas
- Limpia el contenido actual del `<ul>`.
- Recorre todas las tareas y crea dinámicamente un `<li>` con:
  - Un checkbox que refleja y modifica el estado.
  - El texto de la tarea.
  - Estilo tachado si está completada.

---

### ✅ Resultado esperado

La aplicación te permitirá agregar tareas, verlas en lista, marcarlas como completadas con un checkbox y visualizarlas con estilo tachado.

---

> Esta versión sin React sirve como punto de comparación para lo que construiremos en la Hora 2: una versión equivalente usando componentes, estado (`useState`) y props en React.
