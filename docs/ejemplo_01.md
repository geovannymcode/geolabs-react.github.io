# 💻 Ejemplo del mundo real: Lista de tareas (sin React)

## 🎯 ¿Qué vamos a construir?

Vamos a crear una **aplicación de lista de tareas** completamente funcional usando solo HTML y JavaScript moderno. Esta es una aplicación clásica para aprender porque incluye las operaciones más comunes en desarrollo web:

- **CREATE** (Crear): Agregar nuevas tareas
- **READ** (Leer): Mostrar las tareas existentes
- **UPDATE** (Actualizar): Marcar tareas como completadas/pendientes
- **UI Dinámica**: Actualizar la interfaz cuando cambian los datos

### 🖼️ Vista previa del funcionamiento:

```
📝 Lista de Tareas
┌─────────────────────────────┐
│ Nueva tarea...    [Agregar] │
└─────────────────────────────┘

☐ Comprar leche
☑ Hacer ejercicio (tachado)
☐ Estudiar JavaScript
```

---

## 📁 Estructura de archivos explicada

```plaintext
hora-1-js-modern/
├── index.html    # La estructura visual (esqueleto)
└── app.js        # El cerebro de la aplicación (lógica)
```

**¿Por qué separar HTML y JavaScript?**
- **Separación de responsabilidades**: HTML define QUÉ mostrar, JS define CÓMO funciona
- **Mantenibilidad**: Es más fácil encontrar y modificar código
- **Buenas prácticas**: Es el estándar en desarrollo web profesional

---

## 📄 index.html - Análisis línea por línea

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
  
  <!-- Formulario para agregar tareas -->
  <form id="taskForm">
    <input 
      type="text" 
      id="taskInput" 
      placeholder="Nueva tarea..." 
      required 
    />
    <button type="submit">Agregar</button>
  </form>

  <!-- Contenedor para la lista de tareas -->
  <ul id="taskList"></ul>

  <!-- Conexión con JavaScript -->
  <script src="app.js"></script>
</body>
</html>
```

### 🔍 Explicación detallada de cada elemento:

#### 1. **Metadatos del documento**
```html
<meta charset="UTF-8" />
```
- Define la codificación de caracteres (permite usar ñ, tildes, emojis)

```html
<meta name="viewport" content="width=device-width, initial-scale=1.0"/>
```
- Hace que la página se vea bien en móviles

#### 2. **El formulario**
```html
<form id="taskForm">
```
- `id="taskForm"`: Identificador único para acceder desde JavaScript
- Los formularios agrupan campos de entrada y manejan el envío

#### 3. **Campo de entrada**
```html
<input type="text" id="taskInput" placeholder="Nueva tarea..." required />
```
- `type="text"`: Campo de texto simple
- `id="taskInput"`: Para accederlo desde JS
- `placeholder`: Texto de ayuda que desaparece al escribir
- `required`: No permite enviar el formulario vacío

#### 4. **Lista vacía**
```html
<ul id="taskList"></ul>
```
- Inicialmente vacía
- JavaScript agregará `<li>` dinámicamente aquí
- `<ul>` = Unordered List (lista sin numerar)

#### 5. **Conexión con JavaScript**
```html
<script src="app.js"></script>
```
- **IMPORTANTE**: Va al final del `<body>`
- Esto asegura que el HTML se cargue ANTES que el JavaScript
- Si lo pones en el `<head>`, el JS no encontrará los elementos

---

## 📄 app.js

### 🏗️ Estructura general del código

```js
// 1. DATOS - Donde guardamos la información
const tasks = []

// 2. REFERENCIAS - Conexiones con el HTML
const form = document.getElementById('taskForm')
const input = document.getElementById('taskInput')
const list = document.getElementById('taskList')

// 3. EVENTOS - Qué hacer cuando el usuario interactúa
form.addEventListener('submit', (e) => {
  // código...
})

// 4. FUNCIONES - Acciones que puede realizar la app
function addTask(title) { /* ... */ }
function toggleTask(id) { /* ... */ }
function renderTasks() { /* ... */ }
```

---

### 📊 1. Almacenamiento de datos

```js
const tasks = []
```

**¿Qué es esto?**
- Un array vacío que actuará como nuestra "base de datos" temporal
- Aquí guardaremos TODAS las tareas
- Al recargar la página, se pierde (más adelante aprenderemos localStorage)

**¿Cómo se verá con datos?**
```js
// Después de agregar algunas tareas:
const tasks = [
  { id: 1699123456789, title: "Comprar leche", completed: false },
  { id: 1699123467890, title: "Hacer ejercicio", completed: true },
  { id: 1699123478901, title: "Estudiar JavaScript", completed: false }
]
```

---

### 🔗 2. Referencias al DOM

```js
const form = document.getElementById('taskForm')
const input = document.getElementById('taskInput')
const list = document.getElementById('taskList')
```

**¿Qué es el DOM?**
- **Document Object Model**: Representación del HTML en JavaScript
- `document`: Objeto que representa toda la página
- `getElementById()`: Busca un elemento por su atributo `id`

**Analogía simple:**
```js
// Es como tener "atajos" a partes del HTML
const form = /* referencia a <form id="taskForm"> */
const input = /* referencia a <input id="taskInput"> */
const list = /* referencia a <ul id="taskList"> */
```

**¿Por qué guardar las referencias?**
```js
// MAL - Busca el elemento CADA VEZ (lento)
document.getElementById('taskInput').value = ''
document.getElementById('taskInput').focus()

// BIEN - Busca UNA VEZ y reutiliza (rápido)
const input = document.getElementById('taskInput')
input.value = ''
input.focus()
```

---

### 🎮 3. Manejo de eventos

```js
form.addEventListener('submit', (e) => {
  e.preventDefault()
  const title = input.value.trim()
  if (title !== '') {
    addTask(title)
    input.value = ''
  }
})
```

**Desglose línea por línea:**

#### `form.addEventListener('submit', (e) => {`
- **addEventListener**: "Escucha" cuando algo pasa
- **'submit'**: El evento que escuchamos (enviar formulario)
- **(e) =>**: Función que se ejecuta cuando pasa el evento
- **e**: Objeto con información del evento

#### `e.preventDefault()`
- **Problema**: Por defecto, los formularios recargan la página
- **Solución**: `preventDefault()` cancela ese comportamiento
- **Sin esto**: La página se recargaría y perderíamos todo

**Ejemplo visual del flujo:**
```
Usuario escribe "Comprar pan" → Presiona Enter o click en "Agregar"
                                            ↓
                                    Evento 'submit' se dispara
                                            ↓
                                    Se ejecuta nuestra función
                                            ↓
                        preventDefault() evita que se recargue la página
                                            ↓
                            Obtenemos el texto del input
                                            ↓
                            Si no está vacío, agregamos la tarea
```

#### `const title = input.value.trim()`
- **input.value**: Obtiene el texto que escribió el usuario
- **.trim()**: Elimina espacios al inicio y final
- **Ejemplo**: `"  Hola  "` → `"Hola"`

#### `if (title !== '')`
- Verifica que no esté vacío después de quitar espacios
- Evita agregar tareas vacías o solo con espacios

#### `input.value = ''`
- Limpia el campo después de agregar la tarea
- Mejor experiencia de usuario (UX)

---

### ➕ 4. Función addTask - Agregar tareas

```js
function addTask(title) {
  const task = {
    id: Date.now(),
    title,
    completed: false
  }
  tasks.push(task)
  renderTasks()
}
```

**Análisis detallado:**

#### Crear objeto tarea
```js
const task = {
  id: Date.now(),        // Genera ID único basado en timestamp
  title,                 // Shorthand para title: title
  completed: false       // Todas empiezan como pendientes
}
```

**¿Por qué `Date.now()` para el ID?**
- Devuelve los milisegundos desde 1970
- Es único (prácticamente imposible que se repita)
- Ejemplo: `1699123456789`

**Shorthand property:**
```js
// Forma larga
const task = {
  title: title
}

// Forma corta (ES6) - cuando la propiedad y variable tienen el mismo nombre
const task = {
  title
}
```

#### Agregar al array
```js
tasks.push(task)
```
- `push()`: Agrega un elemento al FINAL del array
- Modifica el array original

**Ejemplo del estado:**
```js
// Antes
tasks = []

// Después de addTask("Comprar pan")
tasks = [
  { id: 1699123456789, title: "Comprar pan", completed: false }
]

// Después de otra llamada
tasks = [
  { id: 1699123456789, title: "Comprar pan", completed: false },
  { id: 1699123467890, title: "Estudiar", completed: false }
]
```

---

### ✅ 5. Función toggleTask - Cambiar estado

```js
function toggleTask(id) {
  const task = tasks.find(t => t.id === id)
  if (task) task.completed = !task.completed
  renderTasks()
}
```

**Desglose del proceso:**

#### Buscar la tarea
```js
const task = tasks.find(t => t.id === id)
```
- `find()`: Busca el PRIMER elemento que cumple la condición
- `t => t.id === id`: Función que retorna true cuando encuentra la tarea
- Si no encuentra nada, devuelve `undefined`

**Ejemplo paso a paso:**
```js
// Si toggleTask(1699123456789) y tasks es:
tasks = [
  { id: 1699123456789, title: "Comprar pan", completed: false },
  { id: 1699123467890, title: "Estudiar", completed: true }
]

// find() revisa cada elemento:
// 1. ¿1699123456789 === 1699123456789? SÍ → Devuelve ese objeto
```

#### Cambiar el estado
```js
if (task) task.completed = !task.completed
```
- `if (task)`: Solo si encontró la tarea
- `!task.completed`: Invierte el valor booleano
  - Si era `true` → pasa a `false`
  - Si era `false` → pasa a `true`

**Tabla de verdad del operador NOT (!):**
| Valor original | !Valor |
|---------------|--------|
| true          | false  |
| false         | true   |

---

### 🎨 6. Función renderTasks - Mostrar en pantalla

```js
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

**Esta es la función más compleja. Vamos parte por parte:**

#### Limpiar la lista
```js
list.innerHTML = ''
```
- Borra TODO el contenido HTML dentro de `<ul>`
- Empezamos desde cero cada vez que renderizamos

**¿Por qué limpiar todo?**
- Es más simple que actualizar elementos individuales
- Evita duplicados
- Garantiza que la vista refleje exactamente el array

#### Recorrer las tareas
```js
tasks.forEach(({ id, title, completed }) => {
```
- `forEach`: Ejecuta una función para CADA elemento
- Desestructuración en el parámetro (extrae las propiedades)

**Equivalente sin desestructuración:**
```js
tasks.forEach((task) => {
  const id = task.id
  const title = task.title
  const completed = task.completed
  // ...
})
```

#### Crear elemento HTML
```js
const li = document.createElement('li')
```
- Crea un nuevo elemento `<li>` en memoria
- Aún no está en la página

#### Construir el contenido
```js
li.innerHTML = `
  <input type="checkbox" ${completed ? 'checked' : ''} onchange="toggleTask(${id})" />
  <span style="text-decoration: ${completed ? 'line-through' : 'none'}">${title}</span>
`
```

**Análisis del template literal:**

1. **Checkbox dinámico:**
   ```js
   ${completed ? 'checked' : ''}
   ```
   - Si `completed` es true → agrega atributo `checked`
   - Si es false → no agrega nada
   
   **Resultado HTML:**
   ```html
   <!-- Tarea completada -->
   <input type="checkbox" checked onchange="toggleTask(1699123456789)" />
   
   <!-- Tarea pendiente -->
   <input type="checkbox" onchange="toggleTask(1699123456789)" />
   ```

2. **Evento onchange:**
   ```js
   onchange="toggleTask(${id})"
   ```
   - Cuando el usuario marca/desmarca el checkbox
   - Llama a `toggleTask` con el ID de esa tarea

3. **Estilo condicional:**
   ```js
   style="text-decoration: ${completed ? 'line-through' : 'none'}"
   ```
   - Tarea completada → texto tachado
   - Tarea pendiente → texto normal

#### Agregar a la página
```js
list.appendChild(li)
```
- `appendChild`: Agrega el `<li>` al final de la lista
- Ahora SÍ aparece en la página

---

## 🔄 Flujo completo de la aplicación

### Ejemplo: Agregar una tarea

```
1. Usuario escribe "Comprar leche" y presiona Enter
                        ↓
2. Se dispara evento 'submit' del formulario
                        ↓
3. preventDefault() evita recarga de página
                        ↓
4. Se obtiene el texto: "Comprar leche"
                        ↓
5. addTask("Comprar leche") se ejecuta:
   - Crea objeto: { id: 1699123456789, title: "Comprar leche", completed: false }
   - Lo agrega al array tasks
   - Llama a renderTasks()
                        ↓
6. renderTasks() se ejecuta:
   - Limpia el <ul>
   - Para cada tarea en el array:
     - Crea un <li>
     - Le pone checkbox y texto
     - Lo agrega al <ul>
                        ↓
7. La tarea aparece en pantalla
```

### Ejemplo: Marcar tarea como completada

```
1. Usuario hace click en el checkbox de "Comprar leche"
                        ↓
2. Se ejecuta onchange="toggleTask(1699123456789)"
                        ↓
3. toggleTask busca la tarea con ese ID
                        ↓
4. Cambia completed de false a true
                        ↓
5. Llama a renderTasks()
                        ↓
6. renderTasks() redibuja toda la lista
   - Ahora el checkbox está marcado
   - El texto aparece tachado
```

---

## 🐛 Problemas comunes y soluciones

### 1. **"No funciona nada"**
- **Causa**: El script está en el `<head>`
- **Solución**: Moverlo al final del `<body>` o usar `defer`

### 2. **"Se recarga la página al agregar tarea"**
- **Causa**: Falta `e.preventDefault()`
- **Solución**: Agregarlo al inicio del evento submit

### 3. **"El checkbox no funciona"**
- **Causa**: Error en el nombre de la función o ID incorrecto
- **Solución**: Verificar que `toggleTask` esté definida globalmente

### 4. **"Se agregan tareas vacías"**
- **Causa**: No se valida el input
- **Solución**: Usar `.trim()` y verificar que no esté vacío

---

## 🎯 Conceptos clave aplicados

Este ejemplo utiliza TODOS los conceptos de JavaScript moderno que aprendimos:

1. **`const` y `let`**: Para declarar variables
2. **Arrow functions**: En eventos y callbacks
3. **Destructuring**: En el forEach de renderTasks
4. **Template literals**: Para construir HTML dinámico
5. **Métodos de arrays**: `find()`, `forEach()`, `push()`
6. **Operador ternario**: Para condicionales en línea
