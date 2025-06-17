# 📝 Lista de Tareas Dinámica

## 📋 Índice
1. [Vista general del proyecto](#vista-general)
2. [Arquitectura y flujo de datos](#arquitectura)
3. [Construyendo App.jsx paso a paso](#construyendo-app)
4. [Construyendo TaskForm.jsx](#construyendo-taskform)
5. [Construyendo TaskItem.jsx](#construyendo-taskitem)
6. [Flujo completo de la aplicación](#flujo-completo)
7. [Debugging y herramientas](#debugging)
8. [Extensiones y mejoras](#extensiones)
9. [Errores comunes](#errores-comunes)

---

## 🎯 Vista General del Proyecto

### ¿Qué vamos a construir?

Una aplicación de lista de tareas **completamente funcional** que:
- ✅ Permite agregar nuevas tareas
- ✅ Muestra todas las tareas en una lista
- ✅ Maneja estado con `useState`
- ✅ Usa formularios controlados
- ✅ Implementa comunicación entre componentes

### Vista previa del resultado

```
📝 Lista de Tareas
┌─────────────────────────────────┐
│ [Nueva tarea...] [Agregar]      │
└─────────────────────────────────┘

• Comprar leche
• Estudiar React
• Hacer ejercicio
```

### Diferencias con la versión estática

| Versión Estática (Hora 2) | Versión Dinámica (Hora 3) |
|---------------------------|---------------------------|
| Datos hardcodeados | Estado con `useState` |
| Sin interacción | Formulario funcional |
| Props de solo lectura | Props con funciones |
| Sin re-renderizado | Re-render automático |

---

## 🏗️ Arquitectura y Flujo de Datos

### Estructura de componentes

```
App (Componente padre)
│
├── Estado: tareas = []
├── Función: agregarTarea()
│
├── TaskForm (Componente hijo 1)
│   ├── Props: onAdd={agregarTarea}
│   ├── Estado local: titulo = ""
│   └── Emite: nueva tarea hacia arriba
│
└── TaskItem × N (Componente hijo 2)
    └── Props: titulo, completado
```

### Diagrama de flujo de datos

```
1. Usuario escribe en TaskForm
           ↓
2. TaskForm actualiza su estado local
           ↓
3. Usuario hace clic en "Agregar"
           ↓
4. TaskForm llama a onAdd (agregarTarea)
           ↓
5. App actualiza el array de tareas
           ↓
6. React re-renderiza App
           ↓
7. Se muestran nuevos TaskItems
```

---

## 🛠️ Construyendo App.jsx Paso a Paso

### Versión completa comentada

```jsx
import { useState } from 'react'
import TaskForm from './components/TaskForm'
import TaskItem from './components/TaskItem'

function App() {
  // 1. ESTADO: Array de tareas vacío al inicio
  const [tareas, setTareas] = useState([])
  // Estructura de cada tarea:
  // {
  //   id: 1234567890,        // timestamp único
  //   titulo: "Mi tarea",    // texto ingresado
  //   completado: false      // estado de la tarea
  // }

  // 2. FUNCIÓN: Agregar nueva tarea
  const agregarTarea = (titulo) => {
    // Crear objeto de nueva tarea
    const nuevaTarea = {
      id: Date.now(),      // ID único basado en timestamp
      titulo: titulo,      // o solo 'titulo' (shorthand)
      completado: false    // Siempre inicia como pendiente
    }
    
    // Actualizar estado de forma inmutable
    setTareas([...tareas, nuevaTarea])
    // Equivalente a:
    // const nuevaLista = tareas.slice() // copia
    // nuevaLista.push(nuevaTarea)      // agregar
    // setTareas(nuevaLista)             // actualizar
  }

  // 3. RENDERIZADO
  return (
    <div style={{ maxWidth: "600px", margin: "0 auto" }}>
      <h1>📝 Lista de Tareas</h1>
      
      {/* Formulario con función callback */}
      <TaskForm onAdd={agregarTarea} />
      
      {/* Lista de tareas o mensaje vacío */}
      <ul>
        {tareas.length === 0 ? (
          <p>No hay tareas todavía. ¡Agrega una!</p>
        ) : (
          tareas.map((t) => (
            <TaskItem 
              key={t.id}              // React necesita key única
              titulo={t.titulo}       // Prop: texto
              completado={t.completado} // Prop: estado
            />
          ))
        )}
      </ul>
    </div>
  )
}

export default App
```

### Desglose de conceptos clave

#### 1. **Estado inicial**
```jsx
const [tareas, setTareas] = useState([])
```

**¿Por qué array vacío?**
- Representa "sin tareas"
- Evita errores con `.map()`
- Permite usar `.length` para verificar

#### 2. **Función agregarTarea**
```jsx
const agregarTarea = (titulo) => {
  const nuevaTarea = {
    id: Date.now(),
    titulo,
    completado: false
  }
  setTareas([...tareas, nuevaTarea])
}
```

**Puntos importantes:**
- `Date.now()`: Genera ID único (timestamp en ms)
- `...tareas`: Spread operator para inmutabilidad
- `nuevaTarea` al final: Se agrega al final de la lista

#### 3. **Renderizado condicional**
```jsx
{tareas.length === 0 ? (
  <p>No hay tareas todavía. ¡Agrega una!</p>
) : (
  tareas.map((t) => <TaskItem ... />)
)}
```

**Mejora la UX:**
- Feedback cuando no hay tareas
- Evita mostrar lista vacía
- Guía al usuario

---

## 📝 Construyendo TaskForm.jsx

### Versión completa comentada

```jsx
import { useState } from 'react'

function TaskForm({ onAdd }) {
  // 1. ESTADO LOCAL: Solo para el input
  const [titulo, setTitulo] = useState("")

  // 2. MANEJADOR DE ENVÍO
  const handleSubmit = (e) => {
    // Prevenir recarga de página
    e.preventDefault()
    
    // Validación: eliminar espacios y verificar
    const valor = titulo.trim()
    if (valor === "") return  // No agregar tareas vacías
    
    // Comunicar al padre
    onAdd(valor)
    
    // Limpiar formulario
    setTitulo("")
  }

  // 3. RENDERIZADO
  return (
    <form onSubmit={handleSubmit} style={{ marginBottom: "1rem" }}>
      <input
        type="text"
        value={titulo}                    // Controlado por React
        onChange={(e) => setTitulo(e.target.value)}
        placeholder="Nueva tarea"
        style={{ padding: "0.5rem", width: "80%" }}
      />
      <button 
        type="submit" 
        style={{ padding: "0.5rem" }}
      >
        Agregar
      </button>
    </form>
  )
}

export default TaskForm
```

### Conceptos clave del formulario

#### 1. **Props desestructuradas**
```jsx
function TaskForm({ onAdd }) {
  // Equivalente a:
  // function TaskForm(props) {
  //   const onAdd = props.onAdd
```

#### 2. **Formulario controlado**
```jsx
<input
  value={titulo}                          // Estado → Input
  onChange={(e) => setTitulo(e.target.value)} // Input → Estado
/>
```

**Flujo de datos:**
```
Estado (titulo) → value → Lo que se muestra
Usuario escribe → onChange → Actualiza estado
```

#### 3. **Validación básica**
```jsx
const valor = titulo.trim()  // "  hola  " → "hola"
if (valor === "") return     // Evita tareas vacías
```

**¿Por qué trim()?**
- Elimina espacios al inicio/final
- Evita tareas de solo espacios
- Mejora la calidad de datos

#### 4. **Patrón de comunicación**
```jsx
onAdd(valor)     // Llama función del padre
setTitulo("")    // Limpia input local
```

**Importante:** El componente NO sabe qué hace `onAdd`, solo la llama

---

## 🎨 Construyendo TaskItem.jsx

### Versión actual (presentacional)

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

### Preparado para expansión

```jsx
// Versión futura con más funcionalidad
function TaskItem({ titulo, completado, onToggle, onDelete }) {
  return (
    <li style={{ marginBottom: "0.5rem", display: "flex", gap: "1rem" }}>
      <input 
        type="checkbox" 
        checked={completado}
        onChange={onToggle}
      />
      <span style={{
        textDecoration: completado ? "line-through" : "none",
        fontSize: "1.1rem",
        flex: 1
      }}>
        {titulo}
      </span>
      <button onClick={onDelete}>🗑️</button>
    </li>
  )
}
```

---

## 🔄 Flujo Completo de la Aplicación

### Paso a paso: Agregar una tarea

```
1. INICIO
   App: tareas = []
   TaskForm: titulo = ""

2. USUARIO ESCRIBE "Comprar leche"
   App: tareas = [] (sin cambios)
   TaskForm: titulo = "Comprar leche"

3. USUARIO HACE CLIC EN "Agregar"
   TaskForm: handleSubmit se ejecuta
   └─> e.preventDefault()
   └─> valor = "Comprar leche"
   └─> onAdd("Comprar leche")

4. APP RECIBE LA LLAMADA
   App: agregarTarea("Comprar leche")
   └─> nuevaTarea = {
         id: 1699123456789,
         titulo: "Comprar leche",
         completado: false
       }
   └─> setTareas([nuevaTarea])

5. REACT RE-RENDERIZA
   App: tareas = [{...}] (1 elemento)
   └─> TaskItem se renderiza
   TaskForm: titulo = "" (limpiado)

6. RESULTADO FINAL
   UI muestra:
   • Comprar leche
```

### Diagrama visual del estado

```
Estado inicial:
┌─────────────┐
│ tareas: []  │
└─────────────┘

Después de agregar primera tarea:
┌─────────────────────────────┐
│ tareas: [{                  │
│   id: 1699123456789,        │
│   titulo: "Comprar leche",  │
│   completado: false         │
│ }]                          │
└─────────────────────────────┘

Después de agregar segunda tarea:
┌─────────────────────────────┐
│ tareas: [                   │
│   { id: 1699123456789, ...},│
│   { id: 1699123467890, ...} │
│ ]                           │
└─────────────────────────────┘
```

---

## 🔍 Debugging y Herramientas

### 1. Console.log estratégico

```jsx
const agregarTarea = (titulo) => {
  console.log("Antes:", tareas)
  
  const nuevaTarea = {
    id: Date.now(),
    titulo,
    completado: false
  }
  console.log("Nueva tarea:", nuevaTarea)
  
  setTareas([...tareas, nuevaTarea])
  console.log("Después:", [...tareas, nuevaTarea])
}
```

### 2. React Developer Tools

**Instalación:**
1. Chrome/Firefox: Buscar "React Developer Tools"
2. Instalar extensión oficial

**Uso:**
1. Abrir DevTools (F12)
2. Ir a pestaña "Components"
3. Ver árbol de componentes
4. Inspeccionar props y estado

**Lo que puedes ver:**
```
App
├─ state: {tareas: Array(2)}
├─ TaskForm
│  └─ props: {onAdd: ƒ}
│  └─ state: {titulo: ""}
└─ TaskItem
   └─ props: {titulo: "Comprar leche", completado: false}
```

### 3. Verificación de re-renders

```jsx
function App() {
  console.log("App renderizado")
  // ... resto del código
}

function TaskForm({ onAdd }) {
  console.log("TaskForm renderizado")
  // ... resto del código
}
```

---

## 🚀 Extensiones y Mejoras

### 1. Agregar función de completar

```jsx
// En App.jsx
const toggleTarea = (id) => {
  setTareas(tareas.map(tarea =>
    tarea.id === id
      ? { ...tarea, completado: !tarea.completado }
      : tarea
  ))
}

// Pasar a TaskItem
<TaskItem
  key={t.id}
  titulo={t.titulo}
  completado={t.completado}
  onToggle={() => toggleTarea(t.id)}
/>
```

### 2. Agregar función de eliminar

```jsx
// En App.jsx
const eliminarTarea = (id) => {
  setTareas(tareas.filter(tarea => tarea.id !== id))
}

// Confirmar antes de eliminar
const eliminarTarea = (id) => {
  if (window.confirm("¿Estás seguro?")) {
    setTareas(tareas.filter(tarea => tarea.id !== id))
  }
}
```

### 3. Persistencia con localStorage

```jsx
// Cargar tareas al inicio
const [tareas, setTareas] = useState(() => {
  const guardadas = localStorage.getItem('tareas')
  return guardadas ? JSON.parse(guardadas) : []
})

// Guardar cuando cambien
useEffect(() => {
  localStorage.setItem('tareas', JSON.stringify(tareas))
}, [tareas])
```

### 4. Contador de tareas

```jsx
// En App.jsx
const tareasCompletadas = tareas.filter(t => t.completado).length
const tareasPendientes = tareas.filter(t => !t.completado).length

return (
  <div>
    <h1>📝 Lista de Tareas</h1>
    <p>
      Completadas: {tareasCompletadas} | 
      Pendientes: {tareasPendientes}
    </p>
    {/* resto del componente */}
  </div>
)
```

### 5. Validación mejorada

```jsx
// En TaskForm.jsx
const handleSubmit = (e) => {
  e.preventDefault()
  
  const valor = titulo.trim()
  
  // Validaciones múltiples
  if (valor === "") {
    alert("La tarea no puede estar vacía")
    return
  }
  
  if (valor.length < 3) {
    alert("La tarea debe tener al menos 3 caracteres")
    return
  }
  
  if (valor.length > 100) {
    alert("La tarea es demasiado larga")
    return
  }
  
  onAdd(valor)
  setTitulo("")
}
```

---

## ❌ Errores Comunes y Soluciones

### Error 1: Mutar el estado directamente

```jsx
// ❌ MAL - No actualiza la UI
const agregarTarea = (titulo) => {
  tareas.push({ id: Date.now(), titulo, completado: false })
  setTareas(tareas) // React no detecta cambios
}

// ✅ BIEN - Crear nuevo array
const agregarTarea = (titulo) => {
  setTareas([...tareas, { id: Date.now(), titulo, completado: false }])
}
```

### Error 2: Key no única en map

```jsx
// ❌ MAL - Usa índice como key
{tareas.map((t, index) => (
  <TaskItem key={index} ... />
))}

// ✅ BIEN - Usa ID único
{tareas.map((t) => (
  <TaskItem key={t.id} ... />
))}
```

### Error 3: Olvidar preventDefault

```jsx
// ❌ MAL - La página se recarga
const handleSubmit = (e) => {
  // Falta e.preventDefault()
  onAdd(titulo)
}

// ✅ BIEN
const handleSubmit = (e) => {
  e.preventDefault()
  onAdd(titulo)
}
```

### Error 4: Estado asíncrono

```jsx
// ❌ MAL - Estado no se actualiza inmediatamente
const agregarTarea = (titulo) => {
  setTareas([...tareas, nuevaTarea])
  console.log(tareas) // Muestra el array ANTERIOR
}

// ✅ BIEN - Usar el nuevo valor
const agregarTarea = (titulo) => {
  const nuevasTareas = [...tareas, nuevaTarea]
  setTareas(nuevasTareas)
  console.log(nuevasTareas) // Muestra el array actualizado
}
```

---

## 🎯 Resumen y Conclusiones

### Lo que construimos:

1. **App.jsx**: Componente principal con estado global
2. **TaskForm.jsx**: Formulario controlado con validación
3. **TaskItem.jsx**: Componente presentacional (por ahora)

### Conceptos aplicados:

- ✅ **useState** para manejar lista de tareas
- ✅ **Formularios controlados** con value y onChange
- ✅ **Eventos** con preventDefault
- ✅ **Props** para comunicación padre-hijo
- ✅ **Lifting state up** pattern
- ✅ **Renderizado condicional** para lista vacía
- ✅ **Keys únicas** en listas