# 📝 Lista de Tareas con React

## 📋 Índice
1. [¿Qué vamos a construir?](#qué-vamos-a-construir)
2. [Configuración inicial paso a paso](#configuración-inicial)
3. [Estructura del proyecto explicada](#estructura-del-proyecto)
4. [Construyendo cada archivo](#construyendo-cada-archivo)
5. [Conceptos clave de React aplicados](#conceptos-clave)
6. [Flujo de datos completo](#flujo-de-datos)
7. [Ejercicios prácticos](#ejercicios-prácticos)
8. [Errores comunes y soluciones](#errores-comunes)

---

## 🎯 ¿Qué vamos a construir?

### Vista previa del resultado final

```
📝 Lista de Tareas
─────────────────
• Estudiar React
• Hacer ejercicio (tachado)
• Leer documentación
```

### Características de esta versión

- ✅ **Versión estática**: Sin botones ni interacciones (por ahora)
- ✅ **Componentes modulares**: Código organizado y reutilizable
- ✅ **Props en acción**: Pasando datos entre componentes
- ✅ **Renderizado condicional**: Estilos diferentes según el estado

### Por qué empezar con una versión estática

1. **Enfoque en la estructura**: Primero diseñamos, luego agregamos funcionalidad
2. **Menos complejidad**: Sin estado ni eventos para confundirnos
3. **Base sólida**: Sobre esto agregaremos interactividad después

---

## 🚀 Configuración Inicial

### 1. Crear el proyecto con Vite

```bash
npm create vite@latest tarea-react -- --template react
```

**Desglose del comando:**
- `npm create vite@latest`: Usa la última versión de Vite
- `tarea-react`: Nombre de tu proyecto
- `--`: Separa el nombre de las opciones
- `--template react`: Usa la plantilla de React (no vanilla JS)

### 2. Entrar al proyecto e instalar

```bash
cd tarea-react
npm install
```

**¿Qué hace `npm install`?**
- Lee el archivo `package.json`
- Descarga React, ReactDOM y otras dependencias
- Las guarda en `node_modules/`
- Crea `package-lock.json` con versiones exactas

### 3. Iniciar el servidor de desarrollo

```bash
npm run dev
```

**Resultado esperado:**
```
  VITE v4.4.0  ready in 500 ms

  ➜  Local:   http://localhost:5173/
  ➜  Network: use --host to expose
```

### 4. Limpiar archivos innecesarios

Antes de empezar, limpiemos el proyecto de Vite:

```bash
# Eliminar archivos de ejemplo
rm src/App.css
rm src/index.css
rm src/assets/react.svg
rm public/vite.svg
```

---

## 📁 Estructura del Proyecto

### Vista general

```
tarea-react/
├── 📄 index.html          # Punto de entrada HTML
├── 📁 public/             # Archivos estáticos
├── 📁 src/                # Código fuente
│   ├── 📄 main.jsx       # Punto de entrada React
│   ├── 📄 App.jsx        # Componente principal
│   └── 📁 components/    # Componentes reutilizables
│       └── 📄 TaskItem.jsx
├── 📄 package.json        # Configuración del proyecto
└── 📄 vite.config.js      # Configuración de Vite
```

### ¿Por qué esta estructura?

1. **Separación de responsabilidades**: Cada archivo tiene un propósito
2. **Escalabilidad**: Fácil agregar más componentes
3. **Convención estándar**: Otros desarrolladores la reconocerán

---

## 🛠️ Construyendo Cada Archivo

### 📄 1. index.html - La base HTML

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

**Explicación línea por línea:**

1. **`<!DOCTYPE html>`**: Le dice al navegador que es HTML5
2. **`<html lang="es">`**: Idioma español para accesibilidad
3. **`<meta charset="UTF-8">`**: Soporta caracteres especiales (ñ, á, é, etc.)
4. **`<meta name="viewport"...`**: Diseño responsive para móviles
5. **`<title>`**: Lo que aparece en la pestaña del navegador
6. **`<div id="root">`**: Contenedor vacío donde React vivirá
7. **`<script type="module"...`**: Carga main.jsx como módulo ES6

### 📄 2. main.jsx - El punto de entrada

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

**Explicación detallada:**

```jsx
// 1. Importaciones necesarias
import React from 'react'           // La librería React
import ReactDOM from 'react-dom/client'  // Para interactuar con el DOM
import App from './App'             // Nuestro componente principal

// 2. Encontrar el div root
const rootElement = document.getElementById('root')

// 3. Crear una raíz React
const root = ReactDOM.createRoot(rootElement)

// 4. Renderizar la app
root.render(
  <React.StrictMode>    {/* Modo estricto para desarrollo */}
    <App />             {/* Nuestro componente principal */}
  </React.StrictMode>
)
```

**¿Qué es StrictMode?**
- Herramienta de desarrollo
- Detecta problemas potenciales
- No afecta la producción
- Puede causar doble renderizado en desarrollo (normal)

### 📄 3. App.jsx - El componente principal

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

**Desglose conceptual:**

#### 1. **Los datos (array de tareas)**
```jsx
const tareas = [
  { 
    id: 1,                          // Identificador único
    titulo: "Estudiar React",       // Texto de la tarea
    completado: false               // Estado: pendiente
  },
  // ... más tareas
]
```

#### 2. **El método map()**
```jsx
{tareas.map((tarea) => (
  <TaskItem ... />
))}
```

**¿Qué hace map?**
- Recorre cada elemento del array
- Transforma cada objeto en un componente
- Devuelve un nuevo array de componentes

**Visualización del proceso:**
```
Array de datos          →  map()  →     Array de componentes
[                                        [
  {id:1, titulo:"..."}      →            <TaskItem ... />
  {id:2, titulo:"..."}      →            <TaskItem ... />
  {id:3, titulo:"..."}      →            <TaskItem ... />
]                                        ]
```

#### 3. **La prop key**
```jsx
key={tarea.id}
```

**¿Por qué necesitamos key?**
- React la usa para identificar elementos únicos
- Optimiza el renderizado
- Evita bugs al reordenar listas
- DEBE ser única entre hermanos

**Error común sin key:**
```
Warning: Each child in a list should have a unique "key" prop.
```

### 📄 4. TaskItem.jsx - Componente de tarea

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

**Conceptos aplicados:**

#### 1. **Destructuring de props**
```jsx
// Recibimos props desestructuradas
function TaskItem({ titulo, completado }) {
  // En lugar de:
  // function TaskItem(props) {
  //   props.titulo
  //   props.completado
}
```

#### 2. **Estilo condicional**
```jsx
style={{ textDecoration: completado ? 'line-through' : 'none' }}
```

**Desglose del operador ternario:**
```jsx
completado ? 'line-through' : 'none'
//   ↑              ↑            ↑
// condición    si true      si false
```

**Casos:**
- Si `completado = true` → texto tachado
- Si `completado = false` → texto normal

#### 3. **Estilos inline en React**
```jsx
// HTML normal
<span style="text-decoration: line-through">

// React JSX (objeto JavaScript)
<span style={{ textDecoration: 'line-through' }}>
```

**Nota las diferencias:**
- Doble llave `{{ }}`
- camelCase: `text-decoration` → `textDecoration`
- Valores como strings: `'line-through'`

---

## 🔄 Flujo de Datos Completo

### Visualización del flujo

```
App.jsx (Padre)
    ↓
tiene array de tareas
    ↓
map() sobre cada tarea
    ↓
<TaskItem                    ← Crea componente hijo
  key={1}
  titulo="Estudiar React"    ← Pasa props
  completado={false}
/>
    ↓
TaskItem.jsx (Hijo)
    ↓
recibe props
    ↓
renderiza <li> con estilo condicional
```

### Diagrama de componentes

```
┌─────────────────────────────────┐
│           App                   │
│  ┌───────────────────────────┐  │
│  │  <h1>Lista de Tareas</h1> │  │
│  └───────────────────────────┘  │
│  ┌───────────────────────────┐  │
│  │         <ul>              │  │
│  │  ┌───────────────────┐    │  │
│  │  │   TaskItem #1     │    │  │
│  │  │ Estudiar React    │    │  │
│  │  └───────────────────┘    │  │
│  │  ┌───────────────────┐    │  │
│  │  │   TaskItem #2     │    │  │
│  │  │ Hacer ejercicio   │    │  │
│  │  └───────────────────┘    │  │
│  │  ┌───────────────────┐    │  │
│  │  │   TaskItem #3     │    │  │
│  │  │ Leer docs         │    │  │
│  │  └───────────────────┘    │  │
│  └───────────────────────────┘  │
└─────────────────────────────────┘
```

---

## 🎯 Conceptos Clave de React Aplicados

### 1. **Componentes Funcionales**

```jsx
function TaskItem(props) {
  return <elemento>
}
```

- Son funciones que retornan JSX
- Reciben props como parámetro
- No tienen estado (en esta versión)

### 2. **Props (Propiedades)**

```jsx
// Padre envía
<TaskItem titulo="Mi tarea" completado={true} />

// Hijo recibe
function TaskItem({ titulo, completado }) {
  // Usa titulo y completado
}
```

**Características de las props:**
- Son de solo lectura (inmutables)
- Fluyen de padre a hijo
- Pueden ser cualquier tipo de dato

### 3. **JSX y Expresiones**

```jsx
{/* Esto es un comentario en JSX */}

{/* Expresión JavaScript */}
<h1>Tengo {tareas.length} tareas</h1>

{/* Renderizado condicional */}
{completado && <span>✓</span>}

{/* Mapeo de arrays */}
{tareas.map(t => <TaskItem {...t} />)}
```

### 4. **Listas y Keys**

```jsx
{items.map(item => (
  <Component key={item.id} />
))}
```

**Reglas para keys:**
- Únicas entre hermanos
- Estables (no usar Math.random())
- No usar índice si el orden puede cambiar

---

## 💻 Ejercicios Prácticos

### Ejercicio 1: Crear archivo de datos

**Crear `src/data.js`:**
```jsx
export const tareas = [
  { id: 1, titulo: "Estudiar React", completado: false },
  { id: 2, titulo: "Hacer ejercicio", completado: true },
  { id: 3, titulo: "Leer documentación", completado: false },
  // Agrega más tareas aquí
  { id: 4, titulo: "Practicar código", completado: false },
  { id: 5, titulo: "Ver tutoriales", completado: true }
]
```

**Modificar `App.jsx`:**
```jsx
import TaskItem from './components/TaskItem'
import { tareas } from './data'  // Nueva importación

function App() {
  // Ya no necesitas const tareas = [...]
  
  return (
    // ... resto del código igual
  )
}
```

### Ejercicio 2: Agregar contador de tareas

```jsx
function App() {
  const tareasCompletadas = tareas.filter(t => t.completado).length
  const tareasPendientes = tareas.filter(t => !t.completado).length

  return (
    <div>
      <h1>📝 Lista de Tareas</h1>
      <p>
        Completadas: {tareasCompletadas} | 
        Pendientes: {tareasPendientes}
      </p>
      {/* ... resto del código */}
    </div>
  )
}
```

### Ejercicio 3: Agregar iconos de estado

```jsx
function TaskItem({ titulo, completado }) {
  return (
    <li>
      <span>{completado ? '✅' : '⭕'}</span>
      <span style={{ 
        textDecoration: completado ? 'line-through' : 'none',
        marginLeft: '8px'
      }}>
        {titulo}
      </span>
    </li>
  )
}
```

### Ejercicio 4: Añadir categorías

```jsx
// En data.js
export const tareas = [
  { 
    id: 1, 
    titulo: "Estudiar React", 
    completado: false,
    categoria: "estudio" 
  },
  // ... más tareas con categorías
]

// En TaskItem.jsx
function TaskItem({ titulo, completado, categoria }) {
  const coloresCategorias = {
    estudio: '#3498db',
    ejercicio: '#2ecc71',
    trabajo: '#e74c3c'
  }

  return (
    <li>
      <span style={{ 
        color: coloresCategorias[categoria] || '#000',
        marginRight: '8px'
      }}>
        ●
      </span>
      {/* ... resto del componente */}
    </li>
  )
}
```

---

## ❌ Errores Comunes y Soluciones

### Error 1: "Cannot read property 'map' of undefined"

**Causa**: `tareas` no está definido o es null

**Solución**:
```jsx
// Agregar valor por defecto
const tareas = datosExternos || []

// O verificar antes de mapear
{tareas && tareas.map(...)}
```

### Error 2: "Each child should have unique key"

**Causa**: Falta la prop `key` en elementos de lista

**Solución**:
```jsx
{tareas.map((tarea) => (
  <TaskItem
    key={tarea.id}  // ← Siempre agregar key única
    {...tarea}
  />
))}
```

### Error 3: "Module not found"

**Causa**: Ruta de importación incorrecta

**Solución**:
```jsx
// ❌ Mal
import TaskItem from 'TaskItem'
import TaskItem from './TaskItem'

// ✅ Bien
import TaskItem from './components/TaskItem'
```

### Error 4: Cambios no se reflejan

**Causa**: Servidor no está corriendo o necesita reinicio

**Solución**:
1. Verifica que el terminal muestre "ready"
2. Guarda todos los archivos (Ctrl+S)
3. Si persiste, reinicia con Ctrl+C y `npm run dev`

---

## 🚀 Próximos Pasos

Esta versión estática es la base perfecta para:

1. **Agregar interactividad** (useState)
   - Marcar tareas como completadas
   - Agregar nuevas tareas
   - Eliminar tareas

2. **Persistencia de datos** (localStorage)
   - Guardar tareas al cerrar
   - Cargar tareas al abrir

3. **Filtros y búsqueda**
   - Mostrar solo completadas/pendientes
   - Buscar por título

4. **Estilos mejorados**
   - CSS modules o styled-components
   - Animaciones con Framer Motion
   - Diseño responsive

---

## 🎯 Resumen

Has aprendido a:
- ✅ Estructurar una aplicación React
- ✅ Crear componentes reutilizables
- ✅ Pasar datos con props
- ✅ Renderizar listas con map()
- ✅ Aplicar estilos condicionales
- ✅ Organizar código de forma profesional