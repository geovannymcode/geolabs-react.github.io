# 🎯 Renderizado Condicional, Completar y Eliminar Tareas

## 📋 Índice
1. [¿Qué vamos a construir?](#qué-vamos-a-construir)
2. [Conceptos fundamentales](#conceptos-fundamentales)
3. [Marcar tareas como completadas](#marcar-tareas-completadas)
4. [Eliminar tareas](#eliminar-tareas)
5. [Renderizado condicional](#renderizado-condicional)
6. [Implementación completa paso a paso](#implementación-completa)
7. [Patrones y mejores prácticas](#patrones-mejores-prácticas)
8. [Casos de uso avanzados](#casos-uso-avanzados)
9. [Debugging y errores comunes](#debugging-errores)

---

## 🎯 ¿Qué vamos a construir?

### Evolución de nuestra aplicación

```
Versión anterior:            Nueva versión:
├─ Ver tareas               ├─ Ver tareas
├─ Agregar tareas           ├─ Agregar tareas
├─ Filtrar tareas           ├─ Filtrar tareas
                            ├─ ✅ Marcar como completadas
                            ├─ 🗑️ Eliminar tareas
                            └─ 📝 Mensajes condicionales
```

### Vista previa del resultado final

```
📝 Lista de Tareas

[Nueva tarea...] [Agregar]

□ Comprar leche                    🗑️
☑ Estudiar React                   🗑️
□ Hacer ejercicio                  🗑️

[Si eliminas todas las tareas:]
"🎉 ¡No hay tareas pendientes! Disfruta tu día libre."
```

### Nuevas interacciones

1. **Checkbox interactivo**: Click para completar/descompletar
2. **Botón eliminar**: Remueve permanentemente la tarea
3. **Feedback visual**: Tachado para completadas
4. **Mensajes contextuales**: Cuando no hay tareas

---

## 🧠 Conceptos Fundamentales

### Estado Inmutable en React

#### ¿Qué es la inmutabilidad?

**Inmutable** = No se puede cambiar directamente

```javascript
// ❌ MUTABLE - Modificación directa
const numeros = [1, 2, 3]
numeros.push(4)  // Modifica el array original

// ✅ INMUTABLE - Crear nueva versión
const numeros = [1, 2, 3]
const nuevosNumeros = [...numeros, 4]  // Array nuevo
```

#### ¿Por qué es importante en React?

```javascript
// ❌ React NO detecta este cambio
const [tareas, setTareas] = useState([...])
tareas[0].completado = true  // Mutación directa
setTareas(tareas)  // React: "Es el mismo array, no re-renderizo"

// ✅ React SÍ detecta este cambio
setTareas(tareas.map(t => 
  t.id === id ? { ...t, completado: true } : t
))  // Array nuevo, React re-renderiza
```

### Métodos de Array para Estado Inmutable

#### 1. **map() - Transformar elementos**
```javascript
// Actualizar un elemento específico
const actualizado = items.map(item =>
  item.id === targetId 
    ? { ...item, propiedad: nuevoValor }  // Elemento modificado
    : item                                 // Elemento sin cambios
)
```

#### 2. **filter() - Remover elementos**
```javascript
// Eliminar por condición
const sinElemento = items.filter(item => item.id !== targetId)
```

#### 3. **Spread operator - Agregar elementos**
```javascript
// Agregar al final
const conNuevo = [...items, nuevoItem]

// Agregar al principio
const conNuevo = [nuevoItem, ...items]
```

### Renderizado Condicional

#### Formas de renderizado condicional:

```jsx
// 1. Operador ternario
{condicion ? <ComponenteA /> : <ComponenteB />}

// 2. Operador AND (&&)
{condicion && <Componente />}

// 3. Condicional en variable
const elemento = condicion ? <A /> : <B />
return <div>{elemento}</div>

// 4. Retorno temprano
if (!datos) return <Cargando />
return <Lista datos={datos} />
```

---

## ✅ Marcar Tareas como Completadas

### Implementación paso a paso

#### 1. **Función toggleTarea en App.jsx**

```jsx
const toggleTarea = (id) => {
  setTareas(tareas.map(tarea =>
    tarea.id === id 
      ? { ...tarea, completado: !tarea.completado }
      : tarea
  ))
}
```

**Desglose línea por línea:**

```jsx
const toggleTarea = (id) => {
  // 1. Usamos map para crear un nuevo array
  setTareas(tareas.map(tarea => {
    // 2. Para cada tarea, verificamos si es la seleccionada
    if (tarea.id === id) {
      // 3. Si es la tarea objetivo, creamos una copia
      return {
        ...tarea,                    // Copiamos todas las propiedades
        completado: !tarea.completado // Invertimos el estado
      }
    } else {
      // 4. Si no es la tarea objetivo, la devolvemos sin cambios
      return tarea
    }
  }))
}
```

#### 2. **Versión corta con ternario**

```jsx
const toggleTarea = (id) => {
  setTareas(tareas.map(t =>
    t.id === id ? { ...t, completado: !t.completado } : t
  ))
}
```

### Visualización del proceso

```
ANTES de toggleTarea(2):
[
  { id: 1, titulo: "A", completado: false },
  { id: 2, titulo: "B", completado: false }, ← Target
  { id: 3, titulo: "C", completado: true }
]

DESPUÉS de toggleTarea(2):
[
  { id: 1, titulo: "A", completado: false },
  { id: 2, titulo: "B", completado: true },  ← Cambiado
  { id: 3, titulo: "C", completado: true }
]
```

### Componente TaskItem actualizado

```jsx
function TaskItem({ id, titulo, completado, onToggle, onDelete }) {
  return (
    <li style={{ 
      display: 'flex', 
      alignItems: 'center',
      gap: '0.5rem',
      padding: '0.5rem',
      borderBottom: '1px solid #eee'
    }}>
      {/* Checkbox para completar */}
      <input
        type="checkbox"
        checked={completado}
        onChange={() => onToggle(id)}
        style={{ cursor: 'pointer' }}
      />
      
      {/* Título con estilo condicional */}
      <span style={{ 
        flex: 1,
        textDecoration: completado ? 'line-through' : 'none',
        color: completado ? '#888' : '#000',
        transition: 'all 0.3s ease'
      }}>
        {titulo}
      </span>
      
      {/* Botón eliminar */}
      <button 
        onClick={() => onDelete(id)}
        style={{
          background: 'none',
          border: 'none',
          fontSize: '1.2rem',
          cursor: 'pointer',
          opacity: 0.7,
          transition: 'opacity 0.2s'
        }}
        onMouseEnter={(e) => e.target.style.opacity = 1}
        onMouseLeave={(e) => e.target.style.opacity = 0.7}
      >
        🗑️
      </button>
    </li>
  )
}
```

---

## 🗑️ Eliminar Tareas

### Implementación de eliminarTarea

```jsx
const eliminarTarea = (id) => {
  setTareas(tareas.filter(tarea => tarea.id !== id))
}
```

**Desglose del proceso:**

```jsx
// Versión expandida para claridad
const eliminarTarea = (id) => {
  // filter crea un nuevo array con elementos que cumplen la condición
  const tareasRestantes = tareas.filter(tarea => {
    // Mantener solo si el ID NO coincide
    return tarea.id !== id
  })
  
  setTareas(tareasRestantes)
}
```

### Visualización del filtrado

```
ANTES de eliminarTarea(2):
[
  { id: 1, titulo: "A" },
  { id: 2, titulo: "B" }, ← A eliminar
  { id: 3, titulo: "C" }
]

filter(t => t.id !== 2):
- { id: 1 } !== 2 ? ✅ Mantener
- { id: 2 } !== 2 ? ❌ Eliminar
- { id: 3 } !== 2 ? ✅ Mantener

DESPUÉS:
[
  { id: 1, titulo: "A" },
  { id: 3, titulo: "C" }
]
```

### Agregar confirmación

```jsx
const eliminarTarea = (id) => {
  // Encontrar la tarea para mostrar su título
  const tarea = tareas.find(t => t.id === id)
  
  if (window.confirm(`¿Eliminar "${tarea.titulo}"?`)) {
    setTareas(tareas.filter(t => t.id !== id))
  }
}
```

### Versión con animación de salida

```jsx
const eliminarTarea = (id) => {
  // Marcar como "eliminándose"
  setTareas(tareas.map(t =>
    t.id === id ? { ...t, eliminando: true } : t
  ))
  
  // Eliminar después de la animación
  setTimeout(() => {
    setTareas(prev => prev.filter(t => t.id !== id))
  }, 300)
}

// En TaskItem
<li style={{
  opacity: tarea.eliminando ? 0 : 1,
  transform: tarea.eliminando ? 'translateX(-100%)' : 'none',
  transition: 'all 0.3s ease'
}}>
```

---

## 🎨 Renderizado Condicional

### Casos de uso en nuestra app

#### 1. **Lista vacía vs con tareas**

```jsx
function App() {
  // ... estado y funciones
  
  return (
    <div>
      <h1>📝 Lista de Tareas</h1>
      <TaskForm onAdd={agregarTarea} />
      
      {tareas.length === 0 ? (
        <div style={{
          textAlign: 'center',
          padding: '2rem',
          color: '#666'
        }}>
          <p style={{ fontSize: '1.5rem' }}>🎉</p>
          <p>¡No hay tareas pendientes!</p>
          <p>Disfruta tu día libre o agrega una nueva tarea.</p>
        </div>
      ) : (
        <ul>
          {tareas.map(t => (
            <TaskItem
              key={t.id}
              {...t}
              onToggle={toggleTarea}
              onDelete={eliminarTarea}
            />
          ))}
        </ul>
      )}
    </div>
  )
}
```

#### 2. **Mensaje según el filtro activo**

```jsx
// Diferentes mensajes según contexto
const mensajeVacio = () => {
  if (busqueda) {
    return `No se encontraron tareas con "${busqueda}"`
  }
  if (filtroActivo === 'completadas') {
    return "No hay tareas completadas aún"
  }
  if (filtroActivo === 'pendientes') {
    return "¡Felicidades! No tienes tareas pendientes"
  }
  return "No hay tareas. ¡Agrega una!"
}

// Uso
{tareasFiltradas.length === 0 && (
  <p>{mensajeVacio()}</p>
)}
```

#### 3. **Mostrar/ocultar acciones**

```jsx
function TaskItem({ id, titulo, completado, onToggle, onDelete }) {
  const [mostrarAcciones, setMostrarAcciones] = useState(false)
  
  return (
    <li 
      onMouseEnter={() => setMostrarAcciones(true)}
      onMouseLeave={() => setMostrarAcciones(false)}
    >
      <input type="checkbox" checked={completado} onChange={() => onToggle(id)} />
      <span>{titulo}</span>
      
      {/* Botón eliminar solo al hover */}
      {mostrarAcciones && (
        <button onClick={() => onDelete(id)}>🗑️</button>
      )}
    </li>
  )
}
```

### Patrones de renderizado condicional

#### 1. **Con operador ternario**
```jsx
{condicion ? <ComponenteA /> : <ComponenteB />}
```

#### 2. **Con AND lógico**
```jsx
{condicion && <Componente />}
```

#### 3. **Con switch/case**
```jsx
const renderizarEstado = (estado) => {
  switch(estado) {
    case 'cargando':
      return <Spinner />
    case 'error':
      return <Error />
    case 'exito':
      return <Lista />
    default:
      return null
  }
}
```

#### 4. **Con objeto de componentes**
```jsx
const componentes = {
  cargando: <Spinner />,
  error: <Error />,
  exito: <Lista />
}

return componentes[estado] || null
```

---

## 🛠️ Implementación Completa Paso a Paso

### App.jsx completo

```jsx
import { useState } from 'react'
import TaskForm from './components/TaskForm'
import TaskItem from './components/TaskItem'
import TaskFilter from './components/TaskFilter'

function App() {
  // Estados
  const [tareas, setTareas] = useState([
    { id: 1, titulo: "Aprender React", completado: false },
    { id: 2, titulo: "Crear una app", completado: false }
  ])
  const [filtro, setFiltro] = useState('todas') // 'todas', 'pendientes', 'completadas'

  // Agregar tarea
  const agregarTarea = (titulo) => {
    const nuevaTarea = {
      id: Date.now(),
      titulo,
      completado: false
    }
    setTareas([...tareas, nuevaTarea])
  }

  // Toggle completado
  const toggleTarea = (id) => {
    setTareas(tareas.map(tarea =>
      tarea.id === id 
        ? { ...tarea, completado: !tarea.completado }
        : tarea
    ))
  }

  // Eliminar tarea
  const eliminarTarea = (id) => {
    const tarea = tareas.find(t => t.id === id)
    if (window.confirm(`¿Eliminar "${tarea.titulo}"?`)) {
      setTareas(tareas.filter(t => t.id !== id))
    }
  }

  // Filtrar tareas
  const tareasFiltradas = tareas.filter(tarea => {
    if (filtro === 'todas') return true
    if (filtro === 'pendientes') return !tarea.completado
    if (filtro === 'completadas') return tarea.completado
    return true
  })

  // Contadores
  const stats = {
    total: tareas.length,
    pendientes: tareas.filter(t => !t.completado).length,
    completadas: tareas.filter(t => t.completado).length
  }

  return (
    <div style={{ maxWidth: '600px', margin: '0 auto', padding: '20px' }}>
      <h1>📝 Lista de Tareas</h1>
      
      {/* Estadísticas */}
      <div style={{ 
        background: '#f0f0f0', 
        padding: '10px', 
        borderRadius: '5px',
        marginBottom: '20px'
      }}>
        Total: {stats.total} | 
        Pendientes: {stats.pendientes} | 
        Completadas: {stats.completadas}
      </div>

      {/* Formulario */}
      <TaskForm onAdd={agregarTarea} />
      
      {/* Filtros */}
      <TaskFilter filtroActual={filtro} onCambiar={setFiltro} />
      
      {/* Lista o mensaje vacío */}
      {tareasFiltradas.length === 0 ? (
        <div style={{
          textAlign: 'center',
          padding: '40px',
          color: '#666'
        }}>
          {filtro === 'todas' && stats.total === 0 && (
            <>
              <p style={{ fontSize: '3rem', margin: 0 }}>📋</p>
              <p>No hay tareas aún.</p>
              <p>¡Agrega tu primera tarea!</p>
            </>
          )}
          {filtro === 'pendientes' && stats.pendientes === 0 && (
            <>
              <p style={{ fontSize: '3rem', margin: 0 }}>🎉</p>
              <p>¡No hay tareas pendientes!</p>
              <p>Disfruta tu tiempo libre.</p>
            </>
          )}
          {filtro === 'completadas' && stats.completadas === 0 && (
            <>
              <p style={{ fontSize: '3rem', margin: 0 }}>💪</p>
              <p>No hay tareas completadas aún.</p>
              <p>¡Ponte en marcha!</p>
            </>
          )}
        </div>
      ) : (
        <ul style={{ listStyle: 'none', padding: 0 }}>
          {tareasFiltradas.map(tarea => (
            <TaskItem
              key={tarea.id}
              {...tarea}
              onToggle={toggleTarea}
              onDelete={eliminarTarea}
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

## 🎨 Patrones y Mejores Prácticas

### 1. **Funciones puras para actualizaciones**

```jsx
// ✅ BIEN - Función pura, predecible
const toggleTarea = (tareas, id) => {
  return tareas.map(t => 
    t.id === id ? { ...t, completado: !t.completado } : t
  )
}

// Uso
setTareas(prevTareas => toggleTarea(prevTareas, id))
```

### 2. **Composición de actualizaciones**

```jsx
// Múltiples actualizaciones en una
const completarTodas = () => {
  setTareas(tareas.map(t => ({ ...t, completado: true })))
}

const eliminarCompletadas = () => {
  setTareas(tareas.filter(t => !t.completado))
}

const invertirTodas = () => {
  setTareas(tareas.map(t => ({ ...t, completado: !t.completado })))
}
```

### 3. **Optimización con useCallback**

```jsx
import { useState, useCallback } from 'react'

function App() {
  const [tareas, setTareas] = useState([])
  
  // Memorizar funciones para evitar re-creaciones
  const toggleTarea = useCallback((id) => {
    setTareas(prev => prev.map(t =>
      t.id === id ? { ...t, completado: !t.completado } : t
    ))
  }, [])
  
  const eliminarTarea = useCallback((id) => {
    setTareas(prev => prev.filter(t => t.id !== id))
  }, [])
  
  // ...
}
```

### 4. **Separación de lógica**

```jsx
// utils/tareas.js
export const toggleTarea = (tareas, id) => {
  return tareas.map(t => 
    t.id === id ? { ...t, completado: !t.completado } : t
  )
}

export const eliminarTarea = (tareas, id) => {
  return tareas.filter(t => t.id !== id)
}

export const filtrarTareas = (tareas, filtro) => {
  switch(filtro) {
    case 'pendientes':
      return tareas.filter(t => !t.completado)
    case 'completadas':
      return tareas.filter(t => t.completado)
    default:
      return tareas
  }
}

// App.jsx
import { toggleTarea, eliminarTarea, filtrarTareas } from './utils/tareas'

function App() {
  const handleToggle = (id) => {
    setTareas(prev => toggleTarea(prev, id))
  }
  // ...
}
```

---

## 🚀 Casos de Uso Avanzados

### 1. **Deshacer última acción**

```jsx
function useUndoableTareas(inicial) {
  const [tareas, setTareas] = useState(inicial)
  const [historia, setHistoria] = useState([inicial])
  const [indice, setIndice] = useState(0)
  
  const actualizarTareas = (nuevasTareas) => {
    const nuevaHistoria = historia.slice(0, indice + 1)
    nuevaHistoria.push(nuevasTareas)
    
    setHistoria(nuevaHistoria)
    setIndice(nuevaHistoria.length - 1)
    setTareas(nuevasTareas)
  }
  
  const deshacer = () => {
    if (indice > 0) {
      setIndice(indice - 1)
      setTareas(historia[indice - 1])
    }
  }
  
  const rehacer = () => {
    if (indice < historia.length - 1) {
      setIndice(indice + 1)
      setTareas(historia[indice + 1])
    }
  }
  
  return {
    tareas,
    actualizarTareas,
    deshacer,
    rehacer,
    puedeDeshacer: indice > 0,
    puedeRehacer: indice < historia.length - 1
  }
}
```

### 2. **Arrastrar para reordenar**

```jsx
function TaskItem({ tarea, index, moverTarea }) {
  const [arrastrando, setArrastrando] = useState(false)
  
  const handleDragStart = (e) => {
    e.dataTransfer.effectAllowed = 'move'
    e.dataTransfer.setData('taskIndex', index)
    setArrastrando(true)
  }
  
  const handleDragEnd = () => {
    setArrastrando(false)
  }
  
  const handleDragOver = (e) => {
    e.preventDefault()
  }
  
  const handleDrop = (e) => {
    e.preventDefault()
    const dragIndex = parseInt(e.dataTransfer.getData('taskIndex'))
    if (dragIndex !== index) {
      moverTarea(dragIndex, index)
    }
  }
  
  return (
    <li
      draggable
      onDragStart={handleDragStart}
      onDragEnd={handleDragEnd}
      onDragOver={handleDragOver}
      onDrop={handleDrop}
      style={{
        opacity: arrastrando ? 0.5 : 1,
        cursor: 'move'
      }}
    >
      {/* contenido */}
    </li>
  )
}
```

### 3. **Edición inline**

```jsx
function TaskItem({ tarea, onUpdate }) {
  const [editando, setEditando] = useState(false)
  const [nuevoTitulo, setNuevoTitulo] = useState(tarea.titulo)
  
  const guardar = () => {
    if (nuevoTitulo.trim()) {
      onUpdate(tarea.id, { titulo: nuevoTitulo })
      setEditando(false)
    }
  }
  
  const cancelar = () => {
    setNuevoTitulo(tarea.titulo)
    setEditando(false)
  }
  
  if (editando) {
    return (
      <li>
        <input
          value={nuevoTitulo}
          onChange={(e) => setNuevoTitulo(e.target.value)}
          onKeyDown={(e) => {
            if (e.key === 'Enter') guardar()
            if (e.key === 'Escape') cancelar()
          }}
          autoFocus
        />
        <button onClick={guardar}>✓</button>
        <button onClick={cancelar}>✗</button>
      </li>
    )
  }
  
  return (
    <li onDoubleClick={() => setEditando(true)}>
      {/* contenido normal */}
    </li>
  )
}
```

---

## 🐛 Debugging y Errores Comunes

### Error 1: Mutación del estado

```jsx
// ❌ MAL - No actualiza la UI
const toggleTarea = (id) => {
  const tarea = tareas.find(t => t.id === id)
  tarea.completado = !tarea.completado  // Mutación!
  setTareas(tareas)  // Mismo array, no re-renderiza
}

// ✅ BIEN - Crea nuevo array
const toggleTarea = (id) => {
  setTareas(tareas.map(t =>
    t.id === id ? { ...t, completado: !t.completado } : t
  ))
}
```

### Error 2: Pérdida de referencia en closures

```jsx
// ❌ MAL - tareas puede estar desactualizado
const eliminarMultiples = (ids) => {
  ids.forEach(id => {
    setTareas(tareas.filter(t => t.id !== id))  // tareas viejo!
  })
}

// ✅ BIEN - Usar función updater
const eliminarMultiples = (ids) => {
  setTareas(prevTareas => 
    prevTareas.filter(t => !ids.includes(t.id))
  )
}
```

### Error 3: Keys incorrectas en listas

```jsx
// ❌ MAL - Índice como key
{tareas.map((tarea, index) => (
  <TaskItem key={index} ... />  // Problemas al reordenar!
))}

// ✅ BIEN - ID único como key
{tareas.map(tarea => (
  <TaskItem key={tarea.id} ... />
))}
```

### Error 4: Renderizado condicional incorrecto

```jsx
// ❌ MAL - Muestra "0" cuando no hay tareas
{tareas.length && <Lista tareas={tareas} />}

// ✅ BIEN - Convierte a boolean
{tareas.length > 0 && <Lista tareas={tareas} />}

// ✅ MEJOR - Usa ternario para casos completos
{tareas.length > 0 ? (
  <Lista tareas={tareas} />
) : (
  <MensajeVacio />
)}
```

---

## 🎯 Resumen y Conclusiones

### Lo que construimos:

1. **Toggle de completado**: Cambio de estado inmutable
2. **Eliminación de tareas**: Con confirmación opcional
3. **Renderizado condicional**: Mensajes contextuales
4. **UI interactiva completa**: CRUD funcional

### Conceptos dominados:

- ✅ **Inmutabilidad**: map, filter, spread operator
- ✅ **Eventos complejos**: onChange, onClick
- ✅ **Estado derivado**: Contadores, filtros
- ✅ **Renderizado condicional**: Ternarios, AND lógico
- ✅ **Composición**: Componentes pequeños y enfocados
- ✅ **UX mejorada**: Feedback visual, confirmaciones

### Habilidades desarrolladas:

1. **Pensar inmutablemente**: Nuevas versiones, no mutaciones
2. **Manejar interacciones**: Click, hover, drag
3. **Dar feedback**: Visual y textual
4. **Escribir código mantenible**: Funciones puras, separación