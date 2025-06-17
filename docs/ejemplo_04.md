# 🎯 Filtrado Dinámico de Tareas

## 📋 Índice
1. [¿Qué vamos a construir?](#qué-vamos-a-construir)
2. [Conceptos clave del filtrado](#conceptos-clave)
3. [Construyendo TaskFilter.jsx](#construyendo-taskfilter)
4. [Actualizando App.jsx](#actualizando-app)
5. [Lógica de filtrado explicada](#lógica-de-filtrado)
6. [Flujo completo con diagramas](#flujo-completo)
7. [Extensiones y mejoras](#extensiones)
8. [Patrones avanzados de filtrado](#patrones-avanzados)
9. [Errores comunes](#errores-comunes)

---

## 🎯 ¿Qué vamos a construir?

### Evolución de nuestra app

```
Versión anterior:          →    Nueva versión:
├─ Agregar tareas         ├─ Agregar tareas
├─ Mostrar todas          ├─ Mostrar todas
                          └─ FILTRAR por estado ✨
```

### Vista previa del resultado

```
📝 Lista de Tareas
┌─────────────────────────────────┐
│ [Nueva tarea...] [Agregar]      │
└─────────────────────────────────┘
☐ Mostrar solo tareas pendientes

Mostrando: TODAS (3 tareas)
• Comprar leche
• Estudiar React ✓
• Hacer ejercicio

[Si activas el filtro]
☑ Mostrar solo tareas pendientes

Mostrando: PENDIENTES (2 tareas)
• Comprar leche
• Hacer ejercicio
```

### Nuevas funcionalidades

1. **Checkbox de filtro**: Controla qué tareas mostrar
2. **Estado del filtro**: Recordar la preferencia del usuario
3. **Filtrado dinámico**: Lista que cambia según el filtro
4. **Mensajes contextuales**: Feedback cuando no hay tareas

---

## 🧠 Conceptos Clave del Filtrado

### ¿Qué es filtrar?

Filtrar es **mostrar solo elementos que cumplen una condición**:

```javascript
// Array original
const numeros = [1, 2, 3, 4, 5]

// Filtrar solo pares
const pares = numeros.filter(n => n % 2 === 0)
// Resultado: [2, 4]

// En nuestro caso:
const tareas = [
  { titulo: "Tarea 1", completado: false },
  { titulo: "Tarea 2", completado: true },
  { titulo: "Tarea 3", completado: false }
]

// Filtrar solo pendientes
const pendientes = tareas.filter(t => !t.completado)
// Resultado: Tarea 1 y Tarea 3
```

### Estados posibles del filtro

```
┌─────────────────┐     ┌─────────────────┐
│ Filtro: INACTIVO│     │ Filtro: ACTIVO  │
│                 │     │                 │
│ Muestra:        │     │ Muestra:        │
│ - Pendientes    │     │ - Pendientes    │
│ - Completadas   │     │ - (No completadas)│
└─────────────────┘     └─────────────────┘
```

### Flujo de decisión

```
¿Filtro activo?
    │
    ├─ NO → Mostrar TODAS las tareas
    │
    └─ SÍ → Mostrar SOLO pendientes
            (completado === false)
```

---

## 🔧 Construyendo TaskFilter.jsx

### Componente completo comentado

```jsx
function TaskFilter({ soloPendientes, onToggle }) {
  // Props recibidas:
  // - soloPendientes: boolean (estado actual del filtro)
  // - onToggle: función para cambiar el estado
  
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

### Desglose del componente

#### 1. **Props del componente**
```jsx
function TaskFilter({ soloPendientes, onToggle }) {
  // soloPendientes: true/false - ¿está activo el filtro?
  // onToggle: función que recibe true/false
}
```

#### 2. **Checkbox controlado**
```jsx
<input
  type="checkbox"
  checked={soloPendientes}      // Estado actual
  onChange={(e) => onToggle(e.target.checked)}
/>
```

**¿Qué es `e.target.checked`?**
- Para checkboxes, no usamos `value`
- `checked` es true/false según si está marcado
- Se pasa directamente a la función padre

#### 3. **Estructura accesible**
```jsx
<label>
  <input type="checkbox" ... />
  Mostrar solo tareas pendientes
</label>
```

**Ventajas de envolver en `<label>`:**
- Click en el texto también activa el checkbox
- Mejor accesibilidad
- Mejor UX

### Versión mejorada con más información

```jsx
function TaskFilter({ soloPendientes, onToggle, totalTareas, tareasPendientes }) {
  return (
    <div style={{ 
      marginBottom: '1rem',
      padding: '0.5rem',
      backgroundColor: '#f0f0f0',
      borderRadius: '4px'
    }}>
      <label style={{ display: 'flex', alignItems: 'center', gap: '0.5rem' }}>
        <input
          type="checkbox"
          checked={soloPendientes}
          onChange={(e) => onToggle(e.target.checked)}
        />
        <span>
          Mostrar solo tareas pendientes 
          ({tareasPendientes} de {totalTareas})
        </span>
      </label>
    </div>
  )
}
```

---

## 📱 Actualizando App.jsx

### Cambios necesarios

```jsx
import { useState } from 'react'
import TaskItem from './components/TaskItem'
import TaskForm from './components/TaskForm'
import TaskFilter from './components/TaskFilter'  // NUEVO

function App() {
  // Estados existentes
  const [tareas, setTareas] = useState([])
  
  // NUEVO: Estado para el filtro
  const [soloPendientes, setSoloPendientes] = useState(false)

  // Función existente
  const agregarTarea = (titulo) => {
    const nuevaTarea = {
      id: Date.now(),
      titulo,
      completado: false
    }
    setTareas([...tareas, nuevaTarea])
  }

  // NUEVO: Función para cambiar el filtro
  const toggleFiltro = (activo) => {
    setSoloPendientes(activo)
  }

  // NUEVO: Lógica de filtrado
  const tareasFiltradas = soloPendientes
    ? tareas.filter(t => !t.completado)
    : tareas

  return (
    <div>
      <h1>📝 Lista de Tareas</h1>
      <TaskForm onAdd={agregarTarea} />
      
      {/* NUEVO: Componente de filtro */}
      <TaskFilter 
        soloPendientes={soloPendientes} 
        onToggle={toggleFiltro} 
      />
      
      {/* Cambio: usar tareasFiltradas en lugar de tareas */}
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
```

### Agregando funcionalidad de completar tareas

Para que el filtro tenga sentido, necesitamos poder marcar tareas:

```jsx
// En App.jsx - Agregar función toggle
const toggleTarea = (id) => {
  setTareas(tareas.map(tarea =>
    tarea.id === id
      ? { ...tarea, completado: !tarea.completado }
      : tarea
  ))
}

// Pasar la función a TaskItem
<TaskItem
  key={t.id}
  titulo={t.titulo}
  completado={t.completado}
  onToggle={() => toggleTarea(t.id)}  // NUEVO
/>
```

### TaskItem.jsx actualizado

```jsx
function TaskItem({ titulo, completado, onToggle }) {
  return (
    <li style={{ 
      display: 'flex', 
      alignItems: 'center',
      gap: '0.5rem',
      marginBottom: '0.5rem'
    }}>
      <input
        type="checkbox"
        checked={completado}
        onChange={onToggle}
      />
      <span style={{ 
        textDecoration: completado ? 'line-through' : 'none',
        color: completado ? '#888' : '#000'
      }}>
        {titulo}
      </span>
    </li>
  )
}
```

---

## 🔍 Lógica de Filtrado Explicada

### El operador ternario

```jsx
const tareasFiltradas = soloPendientes
  ? tareas.filter(t => !t.completado)
  : tareas
```

**Desglose paso a paso:**

```jsx
// Si soloPendientes es true:
const tareasFiltradas = tareas.filter(t => !t.completado)
// Solo tareas donde completado es false

// Si soloPendientes es false:
const tareasFiltradas = tareas
// Todas las tareas sin filtrar
```

### Visualización del filtrado

```
ESTADO INICIAL:
tareas = [
  { id: 1, titulo: "A", completado: false },
  { id: 2, titulo: "B", completado: true },
  { id: 3, titulo: "C", completado: false }
]

FILTRO DESACTIVADO (soloPendientes = false):
tareasFiltradas = tareas  // [A, B, C]

FILTRO ACTIVADO (soloPendientes = true):
tareasFiltradas = tareas.filter(t => !t.completado)
// [A, C] - Solo las que tienen completado: false
```

### ¿Por qué `!t.completado`?

```javascript
// t.completado puede ser true o false
// Queremos las NO completadas (pendientes)

!true  === false  // Completada → NO mostrar
!false === true   // Pendiente → SÍ mostrar
```

---

## 🔄 Flujo Completo con Diagramas

### Caso 1: Activar el filtro

```
1. Usuario hace clic en checkbox
           ↓
2. onChange dispara con e.target.checked = true
           ↓
3. TaskFilter llama onToggle(true)
           ↓
4. App ejecuta toggleFiltro(true)
           ↓
5. setSoloPendientes(true)
           ↓
6. React re-renderiza App
           ↓
7. tareasFiltradas = tareas.filter(...)
           ↓
8. Solo se muestran tareas pendientes
```

### Caso 2: Completar una tarea con filtro activo

```
Estado: Filtro activo, 3 tareas (2 pendientes, 1 completada)
Vista actual: [Tarea 1, Tarea 3]

1. Usuario marca "Tarea 1" como completada
           ↓
2. toggleTarea(1) actualiza el estado
           ↓
3. React re-renderiza
           ↓
4. tareasFiltradas recalcula
           ↓
5. Vista actualizada: [Tarea 3]
   (Tarea 1 desaparece de la vista filtrada)
```

### Diagrama de estados

```
┌─────────────────────────────────────┐
│          ESTADO GLOBAL              │
│                                     │
│  tareas: [                          │
│    { id: 1, completado: false },    │
│    { id: 2, completado: true },     │
│    { id: 3, completado: false }     │
│  ]                                  │
│                                     │
│  soloPendientes: false/true         │
└─────────────────────────────────────┘
                ↓
         ┌──────┴──────┐
         │   FILTRADO  │
         └──────┬──────┘
                ↓
    ┌───────────┴───────────┐
    │                       │
    ▼                       ▼
FILTRO OFF              FILTRO ON
Mostrar [1,2,3]         Mostrar [1,3]
```

---

## 🚀 Extensiones y Mejoras

### 1. Múltiples filtros

```jsx
// Estados para diferentes filtros
const [filtro, setFiltro] = useState('todas') // 'todas', 'pendientes', 'completadas'

// Componente de filtro mejorado
function TaskFilter({ filtroActual, onCambiar }) {
  return (
    <div>
      <label>
        <input
          type="radio"
          name="filtro"
          value="todas"
          checked={filtroActual === 'todas'}
          onChange={(e) => onCambiar(e.target.value)}
        />
        Todas
      </label>
      <label>
        <input
          type="radio"
          name="filtro"
          value="pendientes"
          checked={filtroActual === 'pendientes'}
          onChange={(e) => onCambiar(e.target.value)}
        />
        Pendientes
      </label>
      <label>
        <input
          type="radio"
          name="filtro"
          value="completadas"
          checked={filtroActual === 'completadas'}
          onChange={(e) => onCambiar(e.target.value)}
        />
        Completadas
      </label>
    </div>
  )
}

// Lógica de filtrado
const tareasFiltradas = tareas.filter(tarea => {
  if (filtro === 'todas') return true
  if (filtro === 'pendientes') return !tarea.completado
  if (filtro === 'completadas') return tarea.completado
})
```

### 2. Contador de tareas por categoría

```jsx
// En App.jsx
const contadores = {
  total: tareas.length,
  pendientes: tareas.filter(t => !t.completado).length,
  completadas: tareas.filter(t => t.completado).length
}

// En el render
<div style={{ marginBottom: '1rem' }}>
  <span>Total: {contadores.total} | </span>
  <span>Pendientes: {contadores.pendientes} | </span>
  <span>Completadas: {contadores.completadas}</span>
</div>
```

### 3. Búsqueda + Filtro combinados

```jsx
function App() {
  const [tareas, setTareas] = useState([])
  const [soloPendientes, setSoloPendientes] = useState(false)
  const [busqueda, setBusqueda] = useState("")

  // Aplicar filtros en cadena
  const tareasFiltradas = tareas
    .filter(t => soloPendientes ? !t.completado : true)
    .filter(t => t.titulo.toLowerCase().includes(busqueda.toLowerCase()))

  return (
    <div>
      <h1>📝 Lista de Tareas</h1>
      
      {/* Barra de búsqueda */}
      <input
        type="text"
        placeholder="Buscar tareas..."
        value={busqueda}
        onChange={(e) => setBusqueda(e.target.value)}
      />
      
      {/* Filtro de estado */}
      <TaskFilter 
        soloPendientes={soloPendientes} 
        onToggle={setSoloPendientes} 
      />
      
      {/* Lista filtrada */}
      {/* ... */}
    </div>
  )
}
```

### 4. Persistir preferencias del filtro

```jsx
// Cargar preferencia guardada
const [soloPendientes, setSoloPendientes] = useState(() => {
  const guardado = localStorage.getItem('filtroTareas')
  return guardado === 'true'
})

// Guardar cuando cambie
const toggleFiltro = (activo) => {
  setSoloPendientes(activo)
  localStorage.setItem('filtroTareas', activo)
}
```

### 5. Animaciones al filtrar

```jsx
// CSS para transiciones
const estiloLista = {
  transition: 'opacity 0.3s ease',
  opacity: tareasFiltradas.length > 0 ? 1 : 0.5
}

// Aplicar a la lista
<ul style={estiloLista}>
  {tareasFiltradas.map(...)}
</ul>
```

---

## 🎨 Patrones Avanzados de Filtrado

### 1. Filtros componibles

```jsx
// Definir filtros como funciones
const filtros = {
  todas: () => true,
  pendientes: (tarea) => !tarea.completado,
  completadas: (tarea) => tarea.completado,
  urgentes: (tarea) => tarea.prioridad === 'alta',
  hoy: (tarea) => {
    const hoy = new Date().toDateString()
    return new Date(tarea.fecha).toDateString() === hoy
  }
}

// Aplicar filtro seleccionado
const tareasFiltradas = tareas.filter(filtros[filtroActivo])
```

### 2. Filtros múltiples simultáneos

```jsx
function aplicarFiltros(tareas, filtros) {
  return tareas.filter(tarea => {
    // Todos los filtros deben cumplirse
    if (filtros.estado && tarea.completado !== filtros.estado) return false
    if (filtros.categoria && tarea.categoria !== filtros.categoria) return false
    if (filtros.busqueda && !tarea.titulo.includes(filtros.busqueda)) return false
    return true
  })
}

// Uso
const tareasFiltradas = aplicarFiltros(tareas, {
  estado: false,        // Solo pendientes
  categoria: 'trabajo', // Solo de trabajo
  busqueda: 'react'     // Que contengan "react"
})
```

### 3. Hook personalizado para filtros

```jsx
// useFilter.js
function useFilter(items, filterFn) {
  const [filtroActivo, setFiltroActivo] = useState(false)
  
  const itemsFiltrados = filtroActivo 
    ? items.filter(filterFn)
    : items
    
  return [itemsFiltrados, filtroActivo, setFiltroActivo]
}

// Uso en App.jsx
function App() {
  const [tareas, setTareas] = useState([])
  const [tareasFiltradas, filtroActivo, setFiltro] = useFilter(
    tareas,
    t => !t.completado
  )
  
  // Usar tareasFiltradas directamente
}
```

---

## ❌ Errores Comunes y Soluciones

### Error 1: Filtrar el estado original

```jsx
// ❌ MAL - Modifica el array original
const aplicarFiltro = () => {
  setTareas(tareas.filter(t => !t.completado))
  // ¡Perdiste las tareas completadas para siempre!
}

// ✅ BIEN - Filtrar solo para mostrar
const tareasFiltradas = soloPendientes
  ? tareas.filter(t => !t.completado)
  : tareas
// El array original permanece intacto
```

### Error 2: Filtro no se actualiza

```jsx
// ❌ MAL - Cerraduras obsoletas
const tareasFiltradas = soloPendientes
  ? tareas.filter(t => !t.completado)  // tareas puede estar desactualizado
  : tareas

// ✅ BIEN - Usar siempre el estado más reciente
// React garantiza que el estado en el render es el actual
```

### Error 3: Complejidad innecesaria

```jsx
// ❌ MAL - Demasiado complejo
let tareasFiltradas = []
if (soloPendientes === true) {
  for (let i = 0; i < tareas.length; i++) {
    if (tareas[i].completado === false) {
      tareasFiltradas.push(tareas[i])
    }
  }
} else {
  tareasFiltradas = tareas
}

// ✅ BIEN - Simple y declarativo
const tareasFiltradas = soloPendientes
  ? tareas.filter(t => !t.completado)
  : tareas
```

### Error 4: No manejar lista vacía

```jsx
// ❌ MAL - Sin feedback para lista vacía
{tareasFiltradas.map(t => <TaskItem ... />)}

// ✅ BIEN - Mensaje cuando no hay resultados
{tareasFiltradas.length === 0 ? (
  <p>
    {soloPendientes 
      ? "No hay tareas pendientes" 
      : "No hay tareas"}
  </p>
) : (
  tareasFiltradas.map(t => <TaskItem ... />)
)}
```

---

## 🎯 Resumen y Conclusiones

### Lo que construimos:

1. **TaskFilter.jsx**: Componente de control del filtro
2. **Lógica de filtrado**: En App.jsx con operador ternario
3. **Integración completa**: Filtro + Lista + Estado

### Conceptos aplicados:

- ✅ **Estado booleano** para el filtro
- ✅ **Checkbox controlado** con onChange
- ✅ **Array.filter()** para filtrado condicional
- ✅ **Operador ternario** para lógica
- ✅ **Composición** de componentes
- ✅ **Props bidireccionales** (estado down, eventos up)

### Habilidades desarrolladas:

1. **Pensar en estados**: ¿Qué información necesito recordar?
2. **Filtrado declarativo**: Describir QUÉ mostrar, no CÓMO
3. **Composición de UI**: Componentes pequeños y reutilizables
4. **Feedback al usuario**: Mensajes contextuales
