# Manejo de Estado con `useState` y Gestión de Eventos en React

## 🎯 Objetivo de esta hora

Aprender a construir componentes interactivos en React utilizando el hook `useState` para manejar datos dinámicos, y eventos del navegador como `onClick`, `onChange` y `onSubmit` para capturar acciones del usuario. Esta base es esencial para cualquier aplicación React moderna.

---

## 🧠 Conceptos clave

| Concepto          | Descripción                                                                                          |
|-------------------|------------------------------------------------------------------------------------------------------|
| `useState`        | Hook que permite a los componentes funcionales tener y modificar su propio estado.                  |
| Estado            | Datos dinámicos que afectan lo que se muestra en pantalla.                                           |
| Eventos           | Acciones del usuario que disparan funciones (`onClick`, `onChange`, `onSubmit`).                    |
| Formularios       | Entrada de datos del usuario controlada por estado.                                                  |
| Comunicación      | Paso de funciones entre componentes usando `props`.                                                  |

---

## 📘 1. Declarar y actualizar estado con `useState`

`useState` es un hook que se importa desde React y permite a un componente recordar valores entre renderizados.

```jsx
import { useState } from 'react'

function Contador() {
  const [contador, setContador] = useState(0)

  return (
    <button onClick={() => setContador(contador + 1)}>
      Has hecho clic {contador} veces
    </button>
  )
}
```

---

## 📘 2. Formularios controlados

```jsx
function Formulario() {
  const [nombre, setNombre] = useState("")

  const handleSubmit = (e) => {
    e.preventDefault()
    alert(`Hola, ${nombre}`)
  }

  return (
    <form onSubmit={handleSubmit}>
      <input
        type="text"
        value={nombre}
        onChange={(e) => setNombre(e.target.value)}
        placeholder="Escribe tu nombre"
      />
      <button type="submit">Enviar</button>
    </form>
  )
}
```

---

## 📘 3. Comunicación entre componentes

```jsx
function App() {
  const [mensaje, setMensaje] = useState("")

  const actualizarMensaje = (nuevo) => setMensaje(nuevo)

  return (
    <div>
      <h1>{mensaje}</h1>
      <Entrada onTextoCambio={actualizarMensaje} />
    </div>
  )
}

function Entrada({ onTextoCambio }) {
  return (
    <input
      type="text"
      onChange={(e) => onTextoCambio(e.target.value)}
      placeholder="Escribe algo"
    />
  )
}
```

---

## 📘 4. Patrón: "Lifting State Up"

Cuando varios componentes necesitan compartir datos, se eleva el estado al ancestro común. Es un patrón común cuando un hijo envía datos al padre para que este actualice su estado.

---

## 🧪 Ejercicio propuesto (para desarrollar en vivo)

Construyamos juntos un mini componente práctico que aplique lo aprendido:

### Requisitos del ejercicio:

1. Crear un campo de texto y un botón para agregar elementos.
2. Mostrar una lista con los elementos ingresados.
3. Mostrar un mensaje como “No hay elementos aún” si la lista está vacía.

### Código guiado:

```jsx
import { useState } from 'react'

function ListaDinamica() {
  const [texto, setTexto] = useState("")
  const [elementos, setElementos] = useState([])

  const agregarElemento = () => {
    const valor = texto.trim()
    if (valor !== "") {
      setElementos([...elementos, valor])
      setTexto("")
    }
  }

  return (
    <div>
      <h2>Mi Lista</h2>
      <input
        type="text"
        value={texto}
        onChange={(e) => setTexto(e.target.value)}
        placeholder="Agregar elemento"
      />
      <button onClick={agregarElemento}>Agregar</button>

      {elementos.length === 0 ? (
        <p>No hay elementos aún.</p>
      ) : (
        <ul>
          {elementos.map((el, index) => (
            <li key={index}>{el}</li>
          ))}
        </ul>
      )}
    </div>
  )
}
```

---

## ✅ Cierre

- `useState` permite que React se comporte como una interfaz viva, que cambia con las acciones del usuario.
- Los eventos capturan estas acciones y permiten manipular el flujo de datos.
- El siguiente paso es implementar esto en una aplicación completa de lista de tareas, con interacción real.

---