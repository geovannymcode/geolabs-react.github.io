# Manejo de Estado con `useState` y Gestión de Eventos en React

## 🎯 Objetivo de esta hora

Introducir el concepto de **estado** en React utilizando el hook `useState` y enseñar cómo capturar y gestionar eventos para crear interfaces verdaderamente interactivas. Esta hora está dedicada a consolidar la transición de una interfaz estática a una dinámica, donde los componentes reaccionan ante la entrada del usuario.

---

## 🧠 Conceptos clave

| Concepto          | Descripción                                                                                          |
|-------------------|------------------------------------------------------------------------------------------------------|
| `useState`        | Permite a los componentes funcionales mantener y actualizar datos que afectan la interfaz.           |
| Estado            | Representa la información dinámica de un componente, como inputs, listas o banderas de visibilidad. |
| Eventos           | Respuestas del navegador a acciones del usuario (`onClick`, `onChange`, `onSubmit`, etc.).          |
| Formularios       | Entrada de datos del usuario. En React se controlan con estado para sincronizar la vista y el modelo.|
| Lifting State Up  | Patrón donde el componente padre gestiona el estado que comparten varios hijos.                     |

---

## 📌 ¿Qué es el estado en React?

El estado es una estructura que permite a un componente "recordar" información entre renderizados.

```jsx
import { useState } from 'react';

function Ejemplo() {
  const [contador, setContador] = useState(0);

  return (
    <button onClick={() => setContador(contador + 1)}>
      Clics: {contador}
    </button>
  );
}
```

### 🔍 Explicación:
- `useState(0)`: Inicializa el estado con `0`.
- `contador`: Valor actual del estado.
- `setContador`: Función para actualizar el estado.
- Cuando se llama `setContador`, React vuelve a renderizar el componente con el nuevo valor.

---

## ✏️ Formularios controlados

Un formulario controlado en React vincula el valor del campo de entrada directamente al estado del componente.

```jsx
function Formulario() {
  const [nombre, setNombre] = useState("");

  const manejarEnvio = (e) => {
    e.preventDefault();
    alert(`Hola ${nombre}`);
  };

  return (
    <form onSubmit={manejarEnvio}>
      <input
        type="text"
        value={nombre}
        onChange={(e) => setNombre(e.target.value)}
      />
      <button type="submit">Enviar</button>
    </form>
  );
}
```

### 🔍 Detalles:
- El `input` tiene su valor controlado por `nombre`.
- Cada vez que el usuario escribe, se actualiza el estado.
- Al enviar el formulario, se evita el comportamiento por defecto (`preventDefault`) y se utiliza el valor almacenado.

---

## 🔁 Manejo de eventos en React

React encapsula los eventos del DOM en un sistema propio llamado **Synthetic Events**. Algunos eventos comunes:

| Evento        | Descripción                              |
|---------------|------------------------------------------|
| `onClick`     | Clic en un botón o elemento              |
| `onChange`    | Cambios en campos de texto o selectores  |
| `onSubmit`    | Envío de formularios                     |
| `onMouseEnter`| Cursor entra en un elemento              |

```jsx
<button onClick={() => alert("¡Clic!")}>Haz clic</button>
```

---

## 🔄 Comunicación entre componentes

Cuando se requiere pasar información o comportamiento de un componente a otro, se utilizan `props`.

```jsx
function Padre() {
  const saludar = (nombre) => console.log("Hola", nombre);
  return <Hijo onSaludar={saludar} />;
}

function Hijo({ onSaludar }) {
  return <button onClick={() => onSaludar("Estudiante")}>Saludar</button>;
}
```

### 📌 Lifting State Up
Cuando un componente hijo necesita actualizar información que pertenece al padre, se usa una función pasada por `props`.

---

## 🧠 Casos de uso típicos en apps reales

- Formularios de autenticación.
- Listas que crecen dinámicamente (como tareas o comentarios).
- Contadores y toggle buttons.
- Mostrar/ocultar elementos condicionalmente.

---

## 📋 Buenas prácticas

- **No mutar el estado directamente**: Siempre usar la función de actualización.
- **Mantener el estado lo más simple posible**: Usar estructuras planas y claras.
- **Agrupar lógica relacionada**: Por ejemplo, mantener lógica de formularios en su propio componente.

---

## 🧪 Ejercicio en clase

1. Crear un componente `Contador`.
2. Crear un input controlado y mostrar su valor.
3. Crear un botón que agregue valores a una lista visualizada.
4. Bonus: Mostrar un mensaje si la lista está vacía.

---

## ✅ Conclusión

React es declarativo: describe **qué** debe pasar cuando cambian los datos. Con `useState` y los eventos, podemos capturar interacciones del usuario y hacer que la interfaz responda de forma inmediata y eficiente.

---

> En la siguiente hora, extenderemos esta lógica incorporando más acciones: completar tareas, eliminarlas y aplicar filtros visuales, desarrollando así una experiencia de usuario más rica y profesional.
