# Fundamentos de JavaScript Moderno (ES6+)

## 🎯 Objetivo de esta hora

Comprender y aplicar las características principales de **JavaScript moderno (ES6 en adelante)**, las cuales son esenciales para desarrollar interfaces modernas con React. Esta base permitirá escribir código más limpio, legible y mantenible, además de facilitar la transición hacia el desarrollo con componentes funcionales y hooks.

---

## 🧠 Conceptos clave con ejemplos

---

### 1. `const` y `let`

#### 📘 Descripción:
- `const`: se usa para declarar **constantes**. No se puede cambiar su valor (reasignar).
- `let`: se usa para declarar **variables** que pueden cambiar a lo largo del tiempo.
- Ambas respetan el **alcance de bloque** (`{}`), a diferencia de `var` que tiene alcance de función.

#### 🧪 Ejemplo:
```js
const nombre = "Ana";
let edad = 30;

edad = 31; // válido
nombre = "Pedro"; // ❌ Error: no se puede reasignar una constante
```

#### ✅ Cuándo usarlo:
- Usa `const` por defecto.
- Usa `let` si sabes que la variable cambiará su valor (como un contador, input, etc.).

---

### 2. Arrow Functions (Funciones flecha)

#### 📘 Descripción:
Las **arrow functions** son una forma más concisa de declarar funciones. No tienen su propio `this`, lo cual es útil en callbacks y funciones anidadas.

#### 🧪 Ejemplo:
```js
// Función tradicional
function saludar(nombre) {
  return `Hola, ${nombre}`;
}

// Función flecha equivalente
const saludar = (nombre) => `Hola, ${nombre}`;
```

#### 🧩 Otro ejemplo:
```js
const numeros = [1, 2, 3];
const alCuadrado = numeros.map(num => num * num);
console.log(alCuadrado); // [1, 4, 9]
```

#### ✅ Cuándo usarlo:
- Para funciones pequeñas, especialmente dentro de `.map`, `.filter`, `.forEach`.
- Para mantener el contexto de `this` en componentes y objetos.

---

### 3. Destructuring (Desestructuración)

#### 📘 Descripción:
Permite extraer valores de arrays u objetos en variables individuales, haciendo el código más limpio.

#### 🧪 Ejemplo con objetos:
```js
const persona = {
  nombre: "Carlos",
  edad: 28,
  ciudad: "Bogotá"
};

const { nombre, edad } = persona;
console.log(nombre); // "Carlos"
console.log(edad);   // 28
```

#### 🧪 Ejemplo con arrays:
```js
const colores = ["rojo", "verde", "azul"];
const [primero, segundo] = colores;

console.log(primero); // "rojo"
console.log(segundo); // "verde"
```

#### ✅ Cuándo usarlo:

- Para extraer propiedades dentro de funciones, loops o directamente en argumentos.

---

### 4. Spread y Rest Operators (`...`)

#### 📘 Descripción:

El operador `...` se usa para dos cosas:

- **Spread**: Expande elementos de un array u objeto.
- **Rest**: Agrupa múltiples elementos en un array u objeto.

#### 🧪 Ejemplo - Spread:
```js
const numeros = [1, 2, 3];
const nuevosNumeros = [...numeros, 4]; // [1, 2, 3, 4]

const usuario = { nombre: "Ana", edad: 25 };
const usuarioActualizado = { ...usuario, ciudad: "Lima" };
// { nombre: "Ana", edad: 25, ciudad: "Lima" }
```

#### 🧪 Ejemplo - Rest:
```js
function sumar(...numeros) {
  return numeros.reduce((acc, n) => acc + n, 0);
}

console.log(sumar(1, 2, 3, 4)); // 10
```

#### ✅ Cuándo usarlo:
- Spread: Para copiar/modificar objetos y arrays sin mutarlos.
- Rest: Para recibir múltiples argumentos en una función.

---

### 5. Template Literals

#### 📘 Descripción:

Permiten construir cadenas de texto con variables embebidas usando **backticks** \` y `${variable}`.

#### 🧪 Ejemplo:
```js
const producto = "Laptop";
const precio = 1200;

const mensaje = `El producto ${producto} cuesta $${precio}`;
console.log(mensaje); // El producto Laptop cuesta $1200
```

#### ✅ Cuándo usarlo:

- Al construir mensajes, HTML dinámico, o logs de consola.
- Mejora la legibilidad frente a `"Hola " + nombre + ", bienvenido"`.

---

### 6. Funciones de Arrays: `map`, `filter`, `find`, `some`, `every`

#### 📘 Descripción:

Permiten trabajar de forma declarativa con colecciones. Son fundamentales en React para renderizar listas o filtrar datos.

#### 🧪 Ejemplo - `map`:
```js
const tareas = [
  { id: 1, titulo: "Estudiar", completado: false },
  { id: 2, titulo: "Hacer ejercicio", completado: true }
];

const titulos = tareas.map(t => t.titulo);
console.log(titulos); // ["Estudiar", "Hacer ejercicio"]
```

#### 🧪 Ejemplo - `filter`:
```js
const pendientes = tareas.filter(t => !t.completado);
console.log(pendientes); // Solo las tareas no completadas
```

#### 🧪 Ejemplo - `find`:
```js
const tarea = tareas.find(t => t.id === 2);
console.log(tarea); // { id: 2, titulo: "Hacer ejercicio", completado: true }
```

#### 🧪 Ejemplo - `some` / `every`:
```js
const hayPendientes = tareas.some(t => !t.completado);
const todasCompletadas = tareas.every(t => t.completado);

console.log(hayPendientes);     // true
console.log(todasCompletadas);  // false
```

#### ✅ Cuándo usarlo:

- `map`: transformar cada elemento de un array.
- `filter`: obtener subconjuntos que cumplan una condición.
- `find`: obtener el primer elemento que cumpla una condición.
- `some`: verificar si al menos uno cumple.
- `every`: verificar si todos cumplen.

---

## 🧩 ¿Y cómo se relaciona esto con React?

Todos estos conceptos se usan directamente cuando:

- Creamos componentes (`const`, `arrow functions`)
- Manipulamos `props` y `state` (`destructuring`, `spread`)
- Mostramos listas (`map`)
- Creamos elementos dinámicos (`template literals`)
- Filtramos datos antes de renderizar (`filter`, `find`)

---
