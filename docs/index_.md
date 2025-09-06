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

#### 🎯 Ejemplos adicionales:

**Ejemplo 1: const con objetos y arrays**
```js
// const NO hace el objeto inmutable, solo evita la reasignación
const persona = { nombre: "Ana", edad: 25 };
persona.edad = 26; // ✅ Válido - modifica propiedad
persona = { nombre: "Luis" }; // ❌ Error - intenta reasignar

const numeros = [1, 2, 3];
numeros.push(4); // ✅ Válido - modifica array
numeros = [5, 6, 7]; // ❌ Error - intenta reasignar
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

#### 🎯 Ejemplos detallados:

**Ejemplo 1: Diferentes sintaxis**
```js
// Sin parámetros
const saludar = () => "¡Hola mundo!";

// Un parámetro (paréntesis opcionales)
const duplicar = num => num * 2;
const duplicar2 = (num) => num * 2; // también válido

// Múltiples parámetros (paréntesis obligatorios)
const sumar = (a, b) => a + b;

// Con cuerpo de función (necesita return)
const calcularDescuento = (precio, descuento) => {
    const montoDescuento = precio * (descuento / 100);
    const precioFinal = precio - montoDescuento;
    return precioFinal;
};
```

**Ejemplo 2: Diferencia con `this`**
```js
// Problema con función tradicional
const boton = {
    texto: "Click me",
    clicks: 0,
    configurar: function() {
        // this aquí es 'boton'
        document.addEventListener('click', function() {
            this.clicks++; // ❌ Error: this no es 'boton'
        });
    }
};

// Solución con arrow function
const boton2 = {
    texto: "Click me",
    clicks: 0,
    configurar: function() {
        // this aquí es 'boton'
        document.addEventListener('click', () => {
            this.clicks++; // ✅ Funciona: this sigue siendo 'boton'
        });
    }
};
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
  nombre: "Geovanny",
  edad: 28,
  ciudad: "Barranquilla"
};

const { nombre, edad } = persona;
console.log(nombre); // "Geovanny"
console.log(edad);   // 28
```

#### 🧪 Ejemplo con arrays:
```js
const colores = ["rojo", "verde", "azul"];
const [primero, segundo] = colores;

console.log(primero); // "rojo"
console.log(segundo); // "verde"
```

#### 🎯 Ejemplos avanzados:

**Ejemplo 1: Renombrar variables**
```js
const usuario = {
    nombreCompleto: "Ana García",
    correo: "ana@email.com"
};

// Renombrar al desestructurar
const { nombreCompleto: nombre, correo: email } = usuario;
console.log(nombre); // "Ana García"
console.log(email);  // "ana@email.com"
```

**Ejemplo 2: Valores por defecto**
```js
const config = {
    tema: "oscuro",
    // idioma no está definido
};

const { tema, idioma = "español" } = config;
console.log(tema);   // "oscuro"
console.log(idioma); // "español" (valor por defecto)
```

**Ejemplo 3: Desestructuración en parámetros**
```js
// Sin desestructuración
function mostrarUsuario(usuario) {
    console.log(`${usuario.nombre} tiene ${usuario.edad} años`);
}

// Con desestructuración
function mostrarUsuario({ nombre, edad }) {
    console.log(`${nombre} tiene ${edad} años`);
}

mostrarUsuario({ nombre: "Luis", edad: 30 });
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

#### 🎯 Ejemplos detallados:

**Ejemplo 1: Spread para copiar arrays**
```js
// Problema: copiar por referencia
const original = [1, 2, 3];
const copia1 = original; // ❌ No es una copia, es la misma referencia
copia1.push(4);
console.log(original); // [1, 2, 3, 4] - ¡Se modificó el original!

// Solución: usar spread
const original2 = [1, 2, 3];
const copia2 = [...original2]; // ✅ Copia real
copia2.push(4);
console.log(original2); // [1, 2, 3] - Original intacto
```

**Ejemplo 2: Combinar arrays**
```js
const frutas = ["manzana", "pera"];
const verduras = ["lechuga", "tomate"];

// Método antiguo
const alimentos1 = frutas.concat(verduras);

// Con spread (más legible)
const alimentos2 = [...frutas, ...verduras];
// También puedes agregar elementos extra
const alimentos3 = [...frutas, "naranja", ...verduras, "zanahoria"];
```

**Ejemplo 3: Rest en desestructuración**
```js
const [primero, segundo, ...resto] = [1, 2, 3, 4, 5];
console.log(primero); // 1
console.log(segundo); // 2
console.log(resto);   // [3, 4, 5]

const { nombre, ...otrosDatos } = {
    nombre: "Ana",
    edad: 25,
    ciudad: "Madrid",
    trabajo: "Desarrolladora"
};
console.log(nombre);      // "Ana"
console.log(otrosDatos);  // { edad: 25, ciudad: "Madrid", trabajo: "Desarrolladora" }
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

const mensaje = `El producto ${producto} cuesta ${precio}`;
console.log(mensaje); // El producto Laptop cuesta $1200
```

#### 🎯 Ejemplos adicionales para principiantes:

**Ejemplo 1: Multilínea**
```js
// Método antiguo
const textoAntiguo = "Primera línea\n" +
                     "Segunda línea\n" +
                     "Tercera línea";

// Con template literals
const textoNuevo = `Primera línea
Segunda línea
Tercera línea`;
```

**Ejemplo 2: Expresiones complejas**
```js
const items = ["manzana", "pera", "uva"];
const mensaje = `Tienes ${items.length} item${items.length !== 1 ? 's' : ''} en tu carrito`;
console.log(mensaje); // "Tienes 3 items en tu carrito"

// Operaciones matemáticas
const ancho = 10;
const alto = 5;
console.log(`El área es ${ancho * alto} metros cuadrados`); // "El área es 50 metros cuadrados"
```

**Ejemplo 3: HTML dinámico**
```js
const usuario = {
    nombre: "Geovanny",
    edad: 28,
    foto: "geovanny.jpg"
};

const tarjetaHTML = `
    <div class="tarjeta">
        <img src="${usuario.foto}" alt="${usuario.nombre}">
        <h3>${usuario.nombre}</h3>
        <p>Edad: ${usuario.edad} años</p>
    </div>
`;
```

#### ✅ Cuándo usarlo:
- Al construir mensajes, HTML dinámico, o logs de consola.
- Mejora la legibilidad frente a `"Hola " + nombre + ", bienvenido"`.

---

## 📚 Métodos de Arrays

### 🎯 ¿Qué son los métodos de arrays?

Los métodos de arrays son **funciones integradas** en JavaScript que nos permiten trabajar con listas de elementos de forma más fácil y eficiente. En lugar de escribir bucles `for` complejos, podemos usar estos métodos que ya vienen preparados.

---

## 📚 Métodos de Transformación y Búsqueda

### 1. `map()` - Transformar cada elemento

#### 🤔 ¿Qué es?
`map()` es como una **máquina transformadora**: toma cada elemento de tu array y lo convierte en algo nuevo, creando un nuevo array con los resultados.

#### 📝 Sintaxis básica:
```javascript
const nuevoArray = arrayOriginal.map((elemento) => {
    // transformación
    return elementoTransformado;
});
```

#### 🎯 ¿Cuándo usarlo?
Usa `map()` cuando necesites:
- Transformar TODOS los elementos de un array
- Crear un nuevo array basado en otro
- Aplicar la misma operación a cada elemento

#### 💡 Ejemplos prácticos:

**Ejemplo 1: Convertir precios a otra moneda**
```javascript
const preciosUSD = [10, 25, 30, 45];
const preciosMXN = preciosUSD.map(precio => precio * 20);
console.log(preciosMXN); // [200, 500, 600, 900]
```

**Ejemplo 2: Extraer información específica**
```javascript
const usuarios = [
    { nombre: "Ana", edad: 25, email: "ana@mail.com" },
    { nombre: "Luis", edad: 30, email: "luis@mail.com" }
];

// Solo queremos los nombres
const nombres = usuarios.map(usuario => usuario.nombre);
console.log(nombres); // ["Ana", "Luis"]
```

**Ejemplo 3: Crear elementos HTML (útil en React)**
```javascript
const productos = ["Laptop", "Mouse", "Teclado"];
const listaHTML = productos.map(producto => `<li>${producto}</li>`);
// ["<li>Laptop</li>", "<li>Mouse</li>", "<li>Teclado</li>"]
```

---

### 2. `filter()` - Filtrar elementos

#### 🤔 ¿Qué es?
`filter()` es como un **colador o filtro**: solo deja pasar los elementos que cumplen una condición específica.

#### 📝 Sintaxis básica:
```javascript
const arrayFiltrado = arrayOriginal.filter((elemento) => {
    // condición
    return true; // mantener elemento
    return false; // descartar elemento
});
```

#### 🎯 ¿Cuándo usarlo?
Usa `filter()` cuando necesites:
- Obtener solo algunos elementos que cumplan una condición
- Eliminar elementos no deseados
- Crear subconjuntos de datos

#### 💡 Ejemplos prácticos:

**Ejemplo 1: Filtrar números**
```javascript
const numeros = [1, 5, 10, 15, 20, 25];
const mayoresQue10 = numeros.filter(num => num > 10);
console.log(mayoresQue10); // [15, 20, 25]
```

**Ejemplo 2: Filtrar productos por precio**
```javascript
const productos = [
    { nombre: "Laptop", precio: 1200 },
    { nombre: "Mouse", precio: 25 },
    { nombre: "Monitor", precio: 300 },
    { nombre: "Teclado", precio: 80 }
];

// Solo productos baratos (menos de 100)
const productosBaratos = productos.filter(producto => producto.precio < 100);
console.log(productosBaratos); 
// [{ nombre: "Mouse", precio: 25 }, { nombre: "Teclado", precio: 80 }]
```

**Ejemplo 3: Filtrar tareas completadas**
```javascript
const tareas = [
    { id: 1, titulo: "Estudiar JS", completada: true },
    { id: 2, titulo: "Hacer ejercicio", completada: false },
    { id: 3, titulo: "Leer libro", completada: true }
];

const tareasPendientes = tareas.filter(tarea => !tarea.completada);
// Solo devuelve: [{ id: 2, titulo: "Hacer ejercicio", completada: false }]
```

---

### 3. `find()` - Encontrar UN elemento

#### 🤔 ¿Qué es?
`find()` es como un **detective**: busca en el array hasta encontrar EL PRIMER elemento que cumple una condición y lo devuelve.

#### 📝 Sintaxis básica:
```javascript
const elementoEncontrado = array.find((elemento) => {
    // condición de búsqueda
    return elemento.propiedad === valorBuscado;
});
```

#### 🎯 ¿Cuándo usarlo?
Usa `find()` cuando necesites:
- Buscar UN elemento específico
- Encontrar un objeto por su ID
- Obtener el primer elemento que cumple una condición

#### 💡 Ejemplos prácticos:

**Ejemplo 1: Buscar un usuario por ID**
```javascript
const usuarios = [
    { id: 1, nombre: "Ana" },
    { id: 2, nombre: "Luis" },
    { id: 3, nombre: "María" }
];

const usuario = usuarios.find(u => u.id === 2);
console.log(usuario); // { id: 2, nombre: "Luis" }
```

**Ejemplo 2: Encontrar un producto**
```javascript
const inventario = [
    { codigo: "A001", producto: "Laptop", stock: 5 },
    { codigo: "A002", producto: "Mouse", stock: 50 },
    { codigo: "A003", producto: "Teclado", stock: 0 }
];

const productoSinStock = inventario.find(item => item.stock === 0);
console.log(productoSinStock); // { codigo: "A003", producto: "Teclado", stock: 0 }
```

---

### 4. `findIndex()` - Encontrar la POSICIÓN

#### 🤔 ¿Qué es?
`findIndex()` es como `find()`, pero en lugar de devolver el elemento, devuelve **su posición (índice)** en el array.

#### 📝 Sintaxis básica:
```javascript
const posicion = array.findIndex((elemento) => {
    // condición de búsqueda
    return elemento.propiedad === valorBuscado;
});
```

#### 🎯 ¿Cuándo usarlo?
Usa `findIndex()` cuando necesites:
- Saber EN QUÉ POSICIÓN está un elemento
- Actualizar o eliminar un elemento específico
- Verificar si un elemento existe (devuelve -1 si no existe)

#### 💡 Ejemplos prácticos:

**Ejemplo 1: Encontrar posición para actualizar**
```javascript
const tareas = [
    { id: 1, titulo: "Estudiar" },
    { id: 2, titulo: "Ejercicio" },
    { id: 3, titulo: "Leer" }
];

// Encontrar posición de la tarea con id 2
const indice = tareas.findIndex(tarea => tarea.id === 2);
console.log(indice); // 1 (segunda posición)

// Actualizar esa tarea
if (indice !== -1) {
    tareas[indice].titulo = "Hacer ejercicio 30 min";
}
```

**Ejemplo 2: Verificar si existe**
```javascript
const emails = ["ana@mail.com", "luis@mail.com", "maria@mail.com"];

const existe = emails.findIndex(email => email === "pedro@mail.com");
console.log(existe); // -1 (no existe)

if (existe === -1) {
    console.log("Email no registrado");
}
```

---

## 🔍 Métodos de Validación (Devuelven true/false)

### 5. `some()` - ¿AL MENOS UNO cumple?

#### 🤔 ¿Qué es?
`some()` verifica si **AL MENOS UN elemento** del array cumple una condición. Es como preguntar "¿Hay alguno que...?"

#### 📝 Sintaxis básica:
```javascript
const hayAlguno = array.some((elemento) => {
    // condición
    return condicion;
});
```

#### 🎯 ¿Cuándo usarlo?
Usa `some()` cuando necesites saber si:
- Existe al menos un elemento que cumple algo
- Hay algún error en una lista
- Al menos un usuario está activo
- Hay algún producto en stock

#### 💡 Ejemplos prácticos:

**Ejemplo 1: Verificar si hay productos caros**
```javascript
const productos = [
    { nombre: "Pan", precio: 2 },
    { nombre: "Leche", precio: 3 },
    { nombre: "Laptop", precio: 1200 }
];

const hayProductosCars = productos.some(producto => producto.precio > 1000);
console.log(hayProductosCars); // true (por la laptop)
```

**Ejemplo 2: Verificar errores en formulario**
```javascript
const camposFormulario = [
    { campo: "nombre", valor: "Ana", error: false },
    { campo: "email", valor: "", error: true },
    { campo: "telefono", valor: "123456", error: false }
];

const hayErrores = camposFormulario.some(campo => campo.error === true);
if (hayErrores) {
    console.log("Por favor corrige los errores"); // Se ejecuta
}
```

**Ejemplo 3: Verificar permisos**
```javascript
const permisos = ["leer", "escribir"];
const requiereAdmin = permisos.some(permiso => permiso === "admin");
console.log(requiereAdmin); // false
```

---

### 6. `every()` - ¿TODOS cumplen?

#### 🤔 ¿Qué es?
`every()` verifica si **TODOS los elementos** del array cumplen una condición. Es como preguntar "¿Todos son...?"

#### 📝 Sintaxis básica:
```javascript
const todosCumplen = array.every((elemento) => {
    // condición
    return condicion;
});
```

#### 🎯 ¿Cuándo usarlo?
Usa `every()` cuando necesites verificar si:
- Todos los campos están completos
- Todos los usuarios están activos
- Todas las calificaciones son aprobatorias
- Todos los productos tienen stock

#### 💡 Ejemplos prácticos:

**Ejemplo 1: Validar formulario completo**
```javascript
const formulario = [
    { campo: "nombre", valor: "Ana" },
    { campo: "email", valor: "ana@mail.com" },
    { campo: "telefono", valor: "123456789" }
];

const formularioCompleto = formulario.every(campo => campo.valor !== "");
console.log(formularioCompleto); // true (todos tienen valor)
```

**Ejemplo 2: Verificar edades**
```javascript
const edades = [22, 25, 30, 28, 19];
const todosSonMayores = edades.every(edad => edad >= 18);
console.log(todosSonMayores); // true

const todosMayoresDe21 = edades.every(edad => edad > 21);
console.log(todosMayoresDe21); // false (hay uno de 19)
```

**Ejemplo 3: Verificar stock**
```javascript
const carrito = [
    { producto: "Laptop", cantidad: 1, hayStock: true },
    { producto: "Mouse", cantidad: 2, hayStock: true },
    { producto: "Teclado", cantidad: 1, hayStock: false }
];

const todosDisponibles = carrito.every(item => item.hayStock);
if (!todosDisponibles) {
    console.log("Algunos productos no están disponibles"); // Se ejecuta
}
```

---

## 📊 Comparación Visual

| Método | ¿Qué devuelve? | ¿Cuándo usarlo? |
|--------|----------------|-----------------|
| `map()` | Nuevo array transformado | Transformar TODOS los elementos |
| `filter()` | Nuevo array con elementos filtrados | Obtener ALGUNOS elementos |
| `find()` | El PRIMER elemento encontrado | Buscar UN elemento específico |
| `findIndex()` | La POSICIÓN del elemento | Saber DÓNDE está un elemento |
| `some()` | `true` o `false` | Verificar si AL MENOS UNO cumple |
| `every()` | `true` o `false` | Verificar si TODOS cumplen |

---

## 🎮 Ejemplos Combinados

### Sistema de Tareas
```javascript
const tareas = [
    { id: 1, titulo: "Comprar pan", completada: true, prioridad: "baja" },
    { id: 2, titulo: "Estudiar JS", completada: false, prioridad: "alta" },
    { id: 3, titulo: "Hacer ejercicio", completada: false, prioridad: "media" },
    { id: 4, titulo: "Leer emails", completada: true, prioridad: "alta" }
];

// 1. Obtener solo títulos de tareas (map)
const titulos = tareas.map(t => t.titulo);

// 2. Filtrar tareas pendientes (filter)
const pendientes = tareas.filter(t => !t.completada);

// 3. Buscar una tarea específica (find)
const tareaEstudiar = tareas.find(t => t.titulo.includes("Estudiar"));

// 4. Encontrar posición para actualizar (findIndex)
const posicion = tareas.findIndex(t => t.id === 3);

// 5. ¿Hay alguna tarea de alta prioridad pendiente? (some)
const urgente = tareas.some(t => t.prioridad === "alta" && !t.completada);

// 6. ¿Todas las tareas de alta prioridad están completadas? (every)
const altasCompletadas = tareas
    .filter(t => t.prioridad === "alta")
    .every(t => t.completada);
```

### Carrito de Compras
```javascript
const carrito = [
    { id: 1, producto: "Laptop", precio: 1200, cantidad: 1 },
    { id: 2, producto: "Mouse", precio: 25, cantidad: 2 },
    { id: 3, producto: "Teclado", precio: 80, cantidad: 1 }
];

// Calcular precio total con map y reduce
const total = carrito
    .map(item => item.precio * item.cantidad) // [1200, 50, 80]
    .reduce((suma, precio) => suma + precio, 0); // 1330

// Verificar si hay productos caros
const hayProductosCostosos = carrito.some(item => item.precio > 1000);

// Buscar un producto específico
const laptop = carrito.find(item => item.producto === "Laptop");

// Todos los productos cuestan menos de 2000?
const preciosRazonables = carrito.every(item => item.precio < 2000);
```

---

## 🚀 Consejos

1. **Empieza con `map()` y `filter()`** - Son los más comunes
2. **Recuerda el valor de retorno**:
   - `map()` y `filter()` → nuevo array
   - `find()` → un elemento o `undefined`
   - `findIndex()` → número o `-1`
   - `some()` y `every()` → `true` o `false`
3. **No modifiques el array original** - Estos métodos crean copias
4. **Usa el método correcto**:
   - ¿Necesitas transformar? → `map()`
   - ¿Necesitas filtrar? → `filter()`
   - ¿Buscas uno? → `find()`
   - ¿Necesitas verificar? → `some()` o `every()`

---

## 📝 Ejercicios de Práctica

### Ejercicio 1: Lista de Estudiantes
```javascript
const estudiantes = [
    { nombre: "Ana", calificacion: 4, asistencia: 90 },
    { nombre: "Luis", calificacion: 3, asistencia: 85 },
    { nombre: "María", calificacion: 5, asistencia: 95 },
    { nombre: "Carlos", calificacion: 2, asistencia: 80 }
];

// TODO: 
// 1. Obtén solo los nombres de los estudiantes
// 2. Filtra estudiantes con calificación mayor a 3
// 3. Encuentra al estudiante "María"
// 4. ¿Todos tienen asistencia mayor a 75%?
// 5. ¿Hay algún estudiante reprobado (calificación < 30)?
```

### Ejercicio 2: Inventario de Tienda
```javascript
const inventario = [
    { codigo: "A001", nombre: "Camisa", precio: 25, stock: 50 },
    { codigo: "A002", nombre: "Pantalón", precio: 40, stock: 0 },
    { codigo: "A003", nombre: "Zapatos", precio: 60, stock: 20 },
    { codigo: "A004", nombre: "Gorra", precio: 15, stock: 0 }
];

// TODO:
// 1. Crea un array con los precios con 10% de descuento
// 2. Filtra productos sin stock
// 3. Encuentra la posición del producto "Zapatos"
// 4. ¿Todos los productos cuestan menos de $100?
// 5. ¿Hay algún producto sin stock?
```