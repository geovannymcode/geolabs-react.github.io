
# Renderizado condicional, marcado de tareas completadas y eliminación

## 🎯 Objetivo de esta hora

Dominar técnicas fundamentales de interacción en React, como el renderizado condicional, actualización de estado inmutable y acciones de usuario para completar o eliminar elementos. Esta sesión fortalece la lógica de control en interfaces dinámicas mediante el uso correcto de `useState`, operadores lógicos y estructuras declarativas.

---

## 🧠 Conceptos clave

| Concepto                | Explicación técnica                                                                                      |
|-------------------------|----------------------------------------------------------------------------------------------------------|
| Renderizado condicional | Permite decidir qué elementos se muestran según ciertas condiciones del estado o props.                |
| Estado inmutable        | React recomienda no modificar directamente el estado; se debe generar una nueva versión del mismo.     |
| Manejo de eventos       | Se asocian funciones a eventos del DOM, como `onClick` o `onChange`, para ejecutar lógica personalizada. |
| Uso de `.map()`         | Recorre listas para renderizar componentes o transformar estructuras.                                   |
| Uso de `.filter()`      | Crea nuevas listas excluyendo ciertos elementos, ideal para eliminar ítems.                            |
| Ternario en JSX         | Operador corto que permite devolver elementos JSX dependiendo de una condición.                         |

---

## 📘 Explicación

### 1. Marcar tareas como completadas

Usamos `.map()` para recorrer la lista y actualizar solo la tarea cuyo `id` coincida:

```jsx
const toggleTarea = (id) => {
  setTareas(tareas.map(t =>
    t.id === id ? { ...t, completado: !t.completado } : t
  ))
}
```

> 🔍 Estamos retornando una **nueva lista** con los mismos objetos excepto el que se modificó. Así mantenemos la inmutabilidad del estado.

---

### 2. Eliminar una tarea

Con `.filter()` podemos excluir un elemento específico:

```jsx
const eliminarTarea = (id) => {
  setTareas(tareas.filter(t => t.id !== id))
}
```

> 🧼 Esto limpia la lista manteniendo solo las tareas cuyo `id` no sea el seleccionado.

---

### 3. Renderizado condicional

Permite mostrar distintos componentes en función del estado. Ejemplo:

```jsx
{tareas.length === 0 ? (
  <p>No hay tareas pendientes</p>
) : (
  <ul>
    {tareas.map(t => (
      <TaskItem ... />
    ))}
  </ul>
)}
```

> 🎯 Esto evita que la lista se muestre vacía y da feedback visual al usuario.

---

### 4. Estilos condicionales

Se aplica un estilo diferente según el valor de una variable:

```jsx
<span style={{ textDecoration: completado ? 'line-through' : 'none' }}>
  {titulo}
</span>
```

> Esto permite representar visualmente el estado “completado”.

---

## 🧪 Ejercicio propuesto (para desarrollar en vivo)

Implementaremos una funcionalidad avanzada en nuestra lista de tareas que abarque:

1. Marcar una tarea como completada al hacer clic en un checkbox.
2. Eliminar una tarea mediante un botón de acción.
3. Mostrar un mensaje dinámico si no existen tareas pendientes.

### 💡 Solución sugerida (resuelta en clase):

```jsx
function TaskItem({ id, titulo, completado, onToggle, onDelete }) {
  return (
    <li>
      <input
        type="checkbox"
        checked={completado}
        onChange={() => onToggle(id)}
      />
      <span style={{ textDecoration: completado ? 'line-through' : 'none' }}>
        {titulo}
      </span>
      <button onClick={() => onDelete(id)}>🗑️</button>
    </li>
  )
}
```

```jsx
{tareas.length === 0 ? (
  <p>No hay tareas pendientes</p>
) : (
  <ul>
    {tareas.map(t => (
      <TaskItem
        key={t.id}
        id={t.id}
        titulo={t.titulo}
        completado={t.completado}
        onToggle={toggleTarea}
        onDelete={eliminarTarea}
      />
    ))}
  </ul>
)}
```

---

## ✅ Resultado esperado

- Las tareas pueden marcarse como hechas usando un checkbox.
- Las tareas pueden eliminarse de forma individual con un botón.
- Si no hay tareas, se muestra un mensaje claro como feedback visual.

> Esta implementación ofrece una experiencia interactiva fluida, aplicando buenas prácticas de desarrollo con React.
