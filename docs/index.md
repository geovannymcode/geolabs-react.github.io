# Fundamentos de JavaScript Moderno (ES6+)

## 🎯 Objetivo de esta guía

Comprender y aplicar las características principales de **JavaScript moderno (ES6 en adelante)**, las cuales son esenciales para desarrollar interfaces modernas. Esta base permitirá escribir código más limpio, legible y mantenible, además de facilitar la transición hacia el desarrollo con componentes funcionales y hooks en frameworks modernos.

---

## 1. `const` y `let`

### Definición técnica detallada
`const` y `let` son declaradores de variables introducidos en ECMAScript 2015 (ES6) que resuelven problemas fundamentales del declarador `var`:

- **Alcance de bloque (Block Scope)**: A diferencia de `var` que tiene alcance de función, `const` y `let` respetan el alcance delimitado por llaves `{}`. Esto significa que solo existen dentro del bloque donde fueron declaradas.

- **Hoisting modificado**: Aunque técnicamente `const` y `let` son "elevados" (hoisted), permanecen en la "Temporal Dead Zone" (TDZ) hasta su declaración real en el código, generando un error si intentas acceder a ellas antes.

- **`const`**: Define una **referencia inmutable** a un valor. Una vez asignada, la variable no puede ser reasignada (apuntar a otro valor), pero si el valor es un objeto o array, sus propiedades internas sí pueden modificarse porque `const` no hace inmutable el valor, solo la referencia.

- **`let`**: Define una variable que puede ser reasignada. Similar a `var` en flexibilidad, pero con alcance de bloque.

### Comparación detallada: `var` vs `let` y `const`

| Característica | `var` | `let` | `const` |
|----------------|-------|-------|---------|
| **Alcance** | Función | Bloque | Bloque |
| **Reasignación** | ✓ | ✓ | ✗ |
| **Redeclaración en el mismo ámbito** | ✓ | ✗ | ✗ |
| **Elevación (Hoisting)** | Completa, con `undefined` | Parcial, con TDZ | Parcial, con TDZ |
| **Propiedad global (window)** | ✓ | ✗ | ✗ |
| **En bucles for** | Una variable compartida | Nueva variable por iteración | No recomendado (requiere reasignación) |

### Ejemplos clave
```javascript
// 1. Alcance de bloque vs alcance de función
function ejemploAlcance() {
  var x = 10;
  let y = 20;
  const z = 30;
  
  if (true) {
    var x = 1;  // Sobreescribe la x anterior (misma variable)
    let y = 2;  // Nueva variable y, solo en este bloque
    const z = 3;  // Nueva variable z, solo en este bloque
    console.log(x, y, z);  // 1, 2, 3
  }
  
  console.log(x, y, z);  // 1, 20, 30
}

// 2. Temporal Dead Zone
function ejemploTDZ() {
  console.log(a);  // undefined (hoisting completo)
  // console.log(b);  // Error: Cannot access 'b' before initialization
  // console.log(c);  // Error: Cannot access 'c' before initialization
  
  var a = 1;
  let b = 2;
  const c = 3;
}

// 3. Mutabilidad vs Inmutabilidad
const persona = { nombre: "Ana" };
persona.nombre = "María";  // ✓ Válido (mutación)
persona.edad = 30;         // ✓ Válido (mutación)
// persona = { nombre: "Luis" };  // ✗ Error: reasignación prohibida

const numeros = [1, 2, 3];
numeros.push(4);           // ✓ Válido (mutación)
// numeros = [5, 6, 7];    // ✗ Error: reasignación prohibida
```

### Ejemplo práctico: Carrito de compras
```javascript
// Constantes que nunca cambiarán
const IMPUESTO = 0.19;
const ENVIO_GRATIS_DESDE = 100000;

// Variables que cambiarán
let totalCarrito = 0;
let cantidadProductos = 0;

function agregarProducto(precio, cantidad) {
  // Actualizar variables
  cantidadProductos += cantidad;
  totalCarrito += precio * cantidad;
  
  // Mostrar información actual
  console.log(`Productos en carrito: ${cantidadProductos}`);
  console.log(`Subtotal: $${totalCarrito}`);
  
  // Cálculos con constantes
  const impuestoCalculado = totalCarrito * IMPUESTO;
  const envioGratis = totalCarrito >= ENVIO_GRATIS_DESDE;
  
  console.log(`Impuesto: $${impuestoCalculado}`);
  console.log(`Envío gratis: ${envioGratis ? 'Sí' : 'No'}`);
}

// Probemos la función
agregarProducto(20000, 2);  // Agregar 2 productos de $20.000
```

### Errores comunes y cómo evitarlos

1. **Confundir inmutabilidad de referencia con inmutabilidad de valor**
   ```javascript
   // ERROR COMÚN
   const config = { api: "v1", timeout: 3000 };
   config.api = "v2";  // Muchos piensan que esto daría error, pero es válido
   
   // SOLUCIÓN: Si necesitas inmutabilidad real, usa Object.freeze()
   const configInmutable = Object.freeze({ api: "v1", timeout: 3000 });
   // configInmutable.api = "v2";  // Error en strict mode, silencioso en no-strict
   ```

2. **Redeclarar variables en el mismo ámbito**
   ```javascript
   // ERROR COMÚN
   let contador = 1;
   let contador = 2;  // SyntaxError: redeclaración de variable
   
   // CORRECTO: Usar diferentes nombres o ámbitos
   let contador = 1;
   if (true) {
     let contador = 2;  // Variable diferente en otro ámbito
   }
   ```

3. **Usar `const` para valores que necesitan cambiar**
   ```javascript
   // ERROR COMÚN
   const total = 0;
   for (const item of items) {
     // total += item.precio;  // Error: no se puede reasignar una constante
   }
   
   // CORRECTO: Usar let para acumuladores
   let total = 0;
   for (const item of items) {
     total += item.precio;  // Correcto
   }
   ```

### Limitaciones

1. **`const` no garantiza inmutabilidad completa**
      - Solo impide reasignación, pero no la mutación de objetos o arrays
      - Para inmutabilidad real, usa Object.freeze() (aunque solo en primer nivel)

2. **Polyfills y soporte en navegadores antiguos**
      - IE11 y anteriores no soportan `let`/`const` nativamente
      - Requiere transpiladores como Babel para compatibilidad

3. **Bucles `for-of` y `const`**
      - En bucles con colecciones, `const` funciona bien para cada elemento
      - Pero para contadores incrementales, debes usar `let`

### Mejores prácticas

1. Usa `const` por defecto para todo
2. Usa `let` solo cuando necesites reasignar valores
3. Evita completamente `var` en código moderno
4. Declara variables en el ámbito más pequeño posible
5. Prefiere crear nuevas constantes en lugar de reasignar variables

---

## 2. Arrow Functions (Funciones Flecha)

### Definición

Las Arrow Functions (funciones flecha) son una sintaxis concisa para declarar funciones en JavaScript, introducida en ES6. Sus características técnicas fundamentales son:

- **Sintaxis reducida**: Eliminan la palabra clave `function`, y si solo hay un parámetro, los paréntesis son opcionales. Si el cuerpo es una expresión simple, las llaves y `return` son opcionales.

- **No tienen `this` propio**: A diferencia de las funciones tradicionales, las arrow functions heredan automáticamente el valor de `this` del contexto enclosing (léxico) donde fueron definidas, no del contexto de ejecución. Esto resuelve uno de los problemas más comunes en JavaScript.

- **Ausencia de otros objetos implícitos**: No tienen `arguments`, `super` ni `new.target`. No pueden acceder al objeto `arguments` como las funciones tradicionales.

- **No pueden ser constructores**: No se pueden usar con `new` porque no tienen un método `[[Construct]]` interno.

- **No tienen propiedad `prototype`**: Al no poder usarse como constructores, no tiene sentido una propiedad prototype.

- **No pueden usarse como generadores**: No pueden contener la palabra clave `yield` dentro de su cuerpo.

- **No son adecuadas para métodos de objetos** cuando necesitan referenciar el objeto contenedor mediante `this`.

### Comparación detallada: Funciones tradicionales vs Arrow Functions

| Característica | Función Tradicional | Arrow Function |
|----------------|---------------------|----------------|
| **Sintaxis** | `function(a, b) { return a + b; }` | `(a, b) => a + b` |
| **`this`** | Dinámico (depende de la invocación) | Léxico (heredado del contexto) |
| **`arguments`** | ✓ Disponible | ✗ No disponible |
| **Objeto `prototype`** | ✓ Tiene | ✗ No tiene |
| **Uso con `new`** | ✓ Puede usarse como constructor | ✗ No puede usarse |
| **`yield`** | ✓ Puede usarse como generador | ✗ No puede usarse |
| **`return` implícito** | ✗ Siempre explícito | ✓ Opcional con expresión única |

### Ejemplos clave
```javascript
// 1. Diferencias de sintaxis
// Función tradicional
function sumar(a, b) {
  return a + b;
}

// Arrow function - versión corta con return implícito
const sumar = (a, b) => a + b;

// Arrow function - versión con cuerpo
const sumar = (a, b) => {
  const resultado = a + b;
  return resultado;
};

// Arrow function con un parámetro (paréntesis opcionales)
const duplicar = x => x * 2;
const duplicar = (x) => x * 2;  // También válido

// Sin parámetros (paréntesis obligatorios)
const saludar = () => "Hola mundo";

// 2. Diferencias con this
const usuario = {
  nombre: "Carlos",
  amigos: ["Ana", "Luis", "María"],
  
  // Problema con función tradicional
  imprimirAmigosFuncionTradicional: function() {
    this.amigos.forEach(function(amigo) {
      // 'this' aquí no es 'usuario', es undefined o global
      console.log(this.nombre + " es amigo de " + amigo);  // ERROR
    });
  },
  
  // Solución tradicional: bind, that, o self
  imprimirAmigosSolucionTradicional: function() {
    const that = this;  // Guardar referencia a 'this'
    this.amigos.forEach(function(amigo) {
      console.log(that.nombre + " es amigo de " + amigo);  // OK
    });
  },
  
  // Solución moderna con arrow function
  imprimirAmigosArrow: function() {
    this.amigos.forEach(amigo => {
      // 'this' aquí sigue siendo 'usuario'
      console.log(this.nombre + " es amigo de " + amigo);  // OK
    });
  }
};
```

### Ejemplo práctico: Filtrar y transformar datos
```javascript
// Lista de productos
const productos = [
  { nombre: "Camisa", precio: 25000, disponible: true },
  { nombre: "Pantalón", precio: 35000, disponible: false },
  { nombre: "Zapatos", precio: 80000, disponible: true },
  { nombre: "Gorra", precio: 15000, disponible: true }
];

// Filtrar productos disponibles (con arrow function)
const disponibles = productos.filter(producto => producto.disponible);
console.log("Productos disponibles:", disponibles);

// Obtener solo los nombres (con arrow function)
const nombres = productos.map(producto => producto.nombre);
console.log("Nombres de productos:", nombres);

// Calcular precios con descuento (10%)
const preciosConDescuento = productos.map(producto => {
  const descuento = producto.precio * 0.1;
  return {
    nombre: producto.nombre,
    precioOriginal: producto.precio,
    precioConDescuento: producto.precio - descuento
  };
});
console.log("Productos con descuento:", preciosConDescuento);
```

### Errores comunes y cómo evitarlos

1. **Usar arrow functions como métodos de objetos**
   ```javascript
   // ERROR COMÚN
   const contador = {
     valor: 0,
     incrementar: () => {
       this.valor++;  // 'this' NO se refiere a contador
     }
   };
   
   // CORRECTO
   const contador = {
     valor: 0,
     incrementar() {  // Método conciso (ES6)
       this.valor++;  // 'this' se refiere a contador
     }
   };
   ```

2. **Intentar usar `arguments` dentro de arrow functions**
   ```javascript
   // ERROR COMÚN
   const sumarTodos = () => {
     return Array.from(arguments).reduce((a, b) => a + b, 0);  // Error: arguments no definido
   };
   
   // CORRECTO: Usar parámetro rest
   const sumarTodos = (...args) => {
     return args.reduce((a, b) => a + b, 0);
   };
   ```

3. **Intentar usar arrow functions como constructores**
   ```javascript
   // ERROR COMÚN
   const Persona = (nombre) => {
     this.nombre = nombre;  // 'this' no funciona como esperamos
   };
   const juan = new Persona("Juan");  // TypeError: Persona is not a constructor
   
   // CORRECTO: Usar función tradicional o clase
   function Persona(nombre) {
     this.nombre = nombre;
   }
   // O mejor aún, usar clases de ES6
   class Persona {
     constructor(nombre) {
       this.nombre = nombre;
     }
   }
   ```

4. **Omitir paréntesis cuando se necesitan**
   ```javascript
   // ERROR COMÚN
   const obtenerObjeto = () => { prop: "valor" };  // Interpreta { como inicio de función
   
   // CORRECTO: Envolver el objeto entre paréntesis
   const obtenerObjeto = () => ({ prop: "valor" });  // Devuelve el objeto
   ```

### Limitaciones

1. **No son adecuadas para todas las situaciones**
      - No sirven para métodos de objetos (cuando necesitan `this` del objeto)
      - No sirven para constructores de clases
      - No sirven para funciones generadoras

2. **No controlan el valor de `this`**
      - No se pueden usar con `.call()`, `.apply()` o `.bind()` para cambiar `this`
      - Siempre mantienen el `this` del contexto donde fueron creadas

3. **No tienen el objeto `arguments`**
      - Deben usar el operador rest (`...args`) en su lugar

4. **Readability en funciones complejas**
      - Muy útiles para funciones cortas, pero pueden reducir la legibilidad en funciones grandes

### Mejores prácticas

1. Usa arrow functions para callbacks cortos y funciones inline
2. Usa funciones tradicionales para métodos de objetos y clases
3. Aprovecha las arrow functions para evitar el problema de `this` en callbacks
4. Evita arrow functions de una línea demasiado complejas (prioriza legibilidad)

---

## 3. Destructuring (Desestructuración)

### Definición

La desestructuración es una expresión de JavaScript que permite extraer valores de arrays o propiedades de objetos y asignarlos a variables individuales de forma declarativa. Introducida en ES6, esta característica:

- **Crea enlaces (bindings)** entre propiedades de origen y variables destino
- Permite **extracción selectiva** sin necesidad de acceder a propiedades o índices repetidamente
- Soporta **patrones anidados** para estructuras de datos complejas
- Incluye **valores por defecto** para casos donde la propiedad o elemento no existe
- Permite **renombrar variables** durante la extracción
- Funciona con **patrones rest** para capturar elementos o propiedades restantes

La desestructuración opera en el lado **izquierdo** de una asignación y sigue un "patrón de asignación" que refleja la estructura de los datos que se están extrayendo.

### Tipos de desestructuración

#### 1. Desestructuración de objetos
Utiliza la sintaxis de llaves `{}` para extraer propiedades de un objeto basándose en los nombres de las propiedades.

#### 2. Desestructuración de arrays
Utiliza la sintaxis de corchetes `[]` para extraer elementos de un array basándose en su posición.

### Comparación detallada: Acceso tradicional vs Desestructuración

| Operación | Método Tradicional | Con Desestructuración |
|-----------|---------------------|------------------------|
| **Extraer múltiples propiedades** | `const name = user.name; const age = user.age;` | `const { name, age } = user;` |
| **Renombrar variables** | `const userName = user.name;` | `const { name: userName } = user;` |
| **Valores por defecto** | `const role = user.role || 'user';` | `const { role = 'user' } = user;` |
| **Extracción anidada** | `const city = user.address.city;` | `const { address: { city } } = user;` |
| **Extraer de arrays** | `const first = items[0]; const second = items[1];` | `const [first, second] = items;` |
| **Omitir elementos** | `const third = items[2];` | `const [, , third] = items;` |
| **Capturar el resto** | _Requiere slice o loops_ | `const [first, ...rest] = items;` |

### Ejemplos clave

```javascript
// 1. Desestructuración básica de objetos
const persona = { nombre: "Elena", edad: 28, ciudad: "Valencia" };

// Forma tradicional
const nombre = persona.nombre;
const edad = persona.edad;

// Con desestructuración
const { nombre, edad } = persona;
console.log(nombre, edad);  // "Elena", 28

// 2. Renombrar variables
const { nombre: nombreCompleto, ciudad: ubicacion } = persona;
console.log(nombreCompleto, ubicacion);  // "Elena", "Valencia"

// 3. Valores por defecto
const { nombre, profesion = "Desconocida" } = persona;
console.log(nombre, profesion);  // "Elena", "Desconocida"

// 4. Desestructuración anidada de objetos
const usuario = {
  id: 123,
  nombre: "Carlos",
  direccion: {
    calle: "Av. Principal 123",
    ciudad: "Madrid",
    pais: "España"
  }
};

const { nombre, direccion: { ciudad, pais } } = usuario;
console.log(nombre, ciudad, pais);  // "Carlos", "Madrid", "España"

// 5. Desestructuración básica de arrays
const colores = ["rojo", "verde", "azul", "amarillo"];

// Forma tradicional
const primero = colores[0];
const segundo = colores[1];

// Con desestructuración
const [primero, segundo] = colores;
console.log(primero, segundo);  // "rojo", "verde"

// 6. Omitir elementos
const [primero, , tercero] = colores;
console.log(primero, tercero);  // "rojo", "azul"

// 7. Operador rest en desestructuración
const [primero, segundo, ...resto] = colores;
console.log(resto);  // ["azul", "amarillo"]

const { nombre, ...otrosDatos } = persona;
console.log(otrosDatos);  // { edad: 28, ciudad: "Valencia" }

// 8. Desestructuración en parámetros de función
function mostrarInfo({ nombre, edad = 25 }) {
  console.log(`${nombre} tiene ${edad} años`);
}

mostrarInfo(persona);  // "Elena tiene 28 años"
mostrarInfo({ nombre: "Luis" });  // "Luis tiene 25 años"
```

### Ejemplo práctico: Datos de formulario
```javascript
// Datos recibidos de un formulario
const datosFormulario = {
  nombreCompleto: "Ana María Gómez",
  email: "ana@ejemplo.com",
  telefono: "3101234567",
  direccion: {
    calle: "Calle 123",
    ciudad: "Medellín",
    codigoPostal: "050001"
  }
};

// Extraer datos básicos
const { nombreCompleto, email, telefono } = datosFormulario;
console.log(`Nombre: ${nombreCompleto}`);
console.log(`Contacto: ${email}, ${telefono}`);

// Desestructuración anidada (para objetos dentro de objetos)
const { direccion: { ciudad, calle } } = datosFormulario;
console.log(`Ubicación: ${ciudad}, ${calle}`);

// Renombrar variables al desestructurar
const { nombreCompleto: nombre, email: correo } = datosFormulario;
console.log(`Usuario: ${nombre}`);
console.log(`Correo: ${correo}`);

// Valores por defecto (si la propiedad no existe)
const { pais = "Colombia" } = datosFormulario;
console.log(`País: ${pais}`);  // "Colombia" (aunque no existe en el objeto)
```

### Errores comunes y cómo evitarlos

1. **Desestructurar valores null o undefined**
   ```javascript
   // ERROR COMÚN
   const { propiedad } = null;  // TypeError: Cannot destructure property of null
   
   // CORRECTO: Verificar primero o usar valores por defecto a nivel de objeto
   const { propiedad } = objeto || {};  // Objeto vacío como fallback
   ```

2. **Confundir la sintaxis de objetos y arrays**
   ```javascript
   // ERROR COMÚN
   const { 0: primero } = ["a", "b", "c"];  // Intenta usar sintaxis de array con objetos
   
   // CORRECTO: Usar la sintaxis apropiada
   const [primero] = ["a", "b", "c"];  // Para arrays
   const { propiedad } = { propiedad: "valor" };  // Para objetos
   ```

3. **Olvidar los paréntesis en desestructuración de objetos sueltos**
   ```javascript
   // ERROR COMÚN
   // { a, b } = { a: 1, b: 2 };  // SyntaxError: las llaves se interpretan como bloque
   
   // CORRECTO: Envolver en paréntesis
   ({ a, b } = { a: 1, b: 2 });  // OK
   ```

4. **Intentar desestructurar propiedades profundamente anidadas que pueden no existir**
   ```javascript
   // ERROR COMÚN
   const { a: { b: { c } } } = obj;  // Error si 'a' o 'b' no existen
   
   // CORRECTO: Verificar cada nivel o usar desestructuración en varios pasos
   const { a } = obj;
   const { b } = a || {};
   const { c } = b || {};
   
   // O usar bibliotecas como lodash get: _.get(obj, 'a.b.c')
   ```

### Limitaciones

1. **Complejidad con estructuras muy anidadas**
      - La desestructuración de estructuras profundamente anidadas puede volverse difícil de leer
      - Recomendable limitar la profundidad a 2-3 niveles máximo

2. **Problemas con propiedades dinámicas**
      - No permite extraer propiedades con nombres calculados en tiempo de ejecución
      - Para propiedades dinámicas, primero debes calcular el nombre y luego usar notación de corchetes

3. **Desestructuración de propiedades con caracteres especiales**
      - Requiere usar notación especial para propiedades con nombres no estándar
      - Ejemplo: `const { 'special-name': specialName } = obj;`

4. **Compatibilidad con navegadores antiguos**
   - Requiere transpilación para navegadores que no soportan ES6

### Mejores prácticas

1. Usa desestructuración para extraer múltiples propiedades/elementos
2. Aprovecha los valores por defecto para manejar propiedades ausentes
3. Usa la desestructuración en parámetros de funciones para mayor claridad
4. Evita patrones excesivamente complejos que puedan reducir la legibilidad
5. Considera usar `try/catch` al desestructurar datos externos no confiables

---

## 4. Spread y Rest Operators (...)

### Definición

El operador `...` (tres puntos) fue introducido en ES6 y tiene dos usos principales que, aunque similares en sintaxis, cumplen funciones opuestas:

- **Operador Spread (expandir)**: Permite expandir un iterable (array, string) o un objeto en sus elementos o propiedades individuales. Se usa principalmente para:
  - Expandir elementos de un array en argumentos para funciones
  - Crear copias superficiales (shallow copy) de arrays u objetos
  - Combinar múltiples arrays u objetos

- **Operador Rest (agrupar)**: Permite recolectar múltiples elementos y empaquetarlos en un array o las propiedades restantes en un objeto. Se usa principalmente para:
  - Capturar múltiples argumentos en funciones
  - Recoger elementos sobrantes en desestructuración

Técnicamente, la diferencia entre ambos está en el **contexto de uso**:
- Rest aparece en el lado izquierdo de una asignación o en la definición de parámetros
- Spread aparece en el lado derecho de una asignación o en llamadas a funciones

### Contextos de uso principales

#### Spread (...)
1. En llamadas a funciones: `funcion(...array)`
2. En arrays literales: `[1, 2, ...otroArray]`
3. En objetos literales (ES2018): `{ ...objeto1, propiedad: valor }`

#### Rest (...)
1. En parámetros de función: `function miFunc(...args) {}`
2. En desestructuración de arrays: `const [primero, ...resto] = array`
3. En desestructuración de objetos: `const { a, ...resto } = objeto`

### Comparación detallada: Métodos tradicionales vs Spread/Rest

| Operación | Método Tradicional | Con Spread/Rest |
|-----------|---------------------|------------------|
| **Concatenar arrays** | `array1.concat(array2)` | `[...array1, ...array2]` |
| **Copiar array** | `array.slice()` | `[...array]` |
| **Convertir string a array** | `str.split('')` | `[...str]` |
| **Pasar array como args** | `func.apply(null, array)` | `func(...array)` |
| **Capturar argumentos variables** | `arguments` objeto | `function f(...args)` |
| **Recoger elementos restantes** | Loops, slices | `const [a, ...resto] = array` |
| **Combinar objetos** | `Object.assign({}, obj1, obj2)` | `{ ...obj1, ...obj2 }` |

### Ejemplos clave

```javascript
// 1. SPREAD OPERATOR

// 1.1. Expandir arrays en argumentos de función
const numeros = [5, 8, 3, 1, 9];

// Forma tradicional
console.log(Math.max.apply(null, numeros));  // 9

// Con spread
console.log(Math.max(...numeros));  // 9

// 1.2. Crear copias de arrays
// Forma tradicional
const copiaTradicional = numeros.slice();

// Con spread
const copiaConSpread = [...numeros];
copiaConSpread.push(10);  // No afecta al original

// 1.3. Combinar arrays
const array1 = [1, 2, 3];
const array2 = [4, 5, 6];

// Forma tradicional
const combinadoTradicional = array1.concat(array2);

// Con spread (más flexible)
const combinadoConSpread = [...array1, ...array2];
const combinadoConElementos = [...array1, 7, 8, ...array2];  // [1, 2, 3, 7, 8, 4, 5, 6]

// 1.4. Spread con strings
const letras = [..."Hola"];  // ["H", "o", "l", "a"]

// 1.5. Spread con objetos (ES2018)
const persona = { nombre: "David", edad: 31 };
const detalles = { profesion: "Desarrollador", ciudad: "Barcelona" };

// Forma tradicional
const personaCompletaTradicional = Object.assign({}, persona, detalles);

// Con spread
const personaCompletaSpread = { ...persona, ...detalles };
const personaConCambios = { ...persona, edad: 32, ...detalles };  // Sobrescribe edad

// 2. REST OPERATOR

// 2.1. Rest en parámetros de función
// Forma tradicional
function sumarTradicional() {
  let suma = 0;
  for (let i = 0; i < arguments.length; i++) {
    suma += arguments[i];
  }
  return suma;
}

// Con rest (más claro y funcional)
function sumar(...numeros) {
  return numeros.reduce((total, num) => total + num, 0);
}

console.log(sumar(1, 2, 3, 4, 5));  // 15

// 2.2. Rest con parámetros específicos
function procesar(primero, segundo, ...resto) {
  console.log(`Los dos primeros: ${primero}, ${segundo}`);
  console.log(`El resto: ${resto}`);
}

procesar("a", "b", "c", "d", "e");
// Los dos primeros: a, b
// El resto: c,d,e

// 2.3. Rest en desestructuración de arrays
const [ganador, segundo, ...otros] = ["Ana", "Luis", "Carlos", "María", "Pedro"];
console.log(ganador);  // "Ana"
console.log(segundo);  // "Luis"
console.log(otros);    // ["Carlos", "María", "Pedro"]

// 2.4. Rest en desestructuración de objetos
const { nombre, edad, ...otraInfo } = {
  nombre: "Elena",
  edad: 28,
  profesion: "Ingeniera",
  ciudad: "Madrid",
  hobby: "Pintura"
};

console.log(nombre);     // "Elena"
console.log(edad);       // 28
console.log(otraInfo);   // { profesion: "Ingeniera", ciudad: "Madrid", hobby: "Pintura" }
```

### Ejemplo práctico: Función con parámetros REST y formulario
```javascript
// Función que acepta un número indefinido de argumentos
function sumarTodos(...numeros) {
  return numeros.reduce((total, num) => total + num, 0);
}

console.log(sumarTodos(1, 2));            // 3
console.log(sumarTodos(1, 2, 3, 4, 5));   // 15
console.log(sumarTodos(10, 20, 30, 40));  // 100

// Actualizar datos de formulario (caso común)
const datosUsuario = {
  nombre: "Juliana",
  email: "juliana@ejemplo.com",
  preferencias: {
    tema: "claro",
    notificaciones: true
  }
};

// Actualizar solo algunas propiedades sin modificar el resto
function actualizarPerfil(usuario, cambios) {
  return {
    ...usuario,  // Mantener todas las propiedades existentes
    ...cambios,  // Sobrescribir con los cambios
    fechaActualizacion: new Date()  // Añadir propiedad nueva
  };
}

const usuarioActualizado = actualizarPerfil(datosUsuario, {
  email: "juliana.nueva@ejemplo.com",
  telefono: "3209876543"  // Propiedad nueva
});

console.log(usuarioActualizado);
// Resultado: el objeto original con email actualizado, teléfono añadido 
// y la nueva fecha de actualización
```

### Errores comunes y cómo evitarlos

1. **Confundir shallow copy con deep copy**
   ```javascript
   // ERROR COMÚN
   const original = { datos: { a: 1, b: 2 } };
   const copia = { ...original };
   copia.datos.a = 5;  // ¡También modifica original.datos.a!
   
   // CORRECTO para deep copy
   const copiaReal = JSON.parse(JSON.stringify(original));  // Para objetos simples
   // O usar bibliotecas como lodash _.cloneDeep()
   ```

2. **Usar rest en posición incorrecta**
   ```javascript
   // ERROR COMÚN
   function error(...resto, ultimo) {}  // SyntaxError
   
   // CORRECTO
   function correcto(primero, ...resto) {}  // Rest siempre al final
   ```

3. **Spread de null o undefined**
   ```javascript
   // ERROR COMÚN
   const obj = { ...null };  // TypeError en algunos entornos
   
   // CORRECTO
   const obj = { ...(data || {}) };  // Garantizar un objeto
   ```

4. **Orden de propiedades al combinar objetos**
   ```javascript
   // ERROR COMÚN: Propiedades sobrescritas sin darse cuenta
   const resultado = { ...defaults, ...config, debug: true };
   // Si config tiene su propio debug: false, sobrescribe el true
   
   // CORRECTO: Considerar cuidadosamente el orden
   const resultado = { ...defaults, debug: true, ...config };  // config tiene la última palabra
   ```

### Limitaciones

1. **Copia superficial (shallow copy)**
      - Los operadores spread solo crean copias superficiales
      - Los objetos anidados siguen siendo referencias al original

2. **Rendimiento con objetos grandes**
      - Spread en objetos grandes puede tener impacto en rendimiento
      - Para operaciones críticas en rendimiento, considerar métodos alternativos

3. **No funciona con objetos que no sean iterables**
      - Spread solo funciona con objetos iterables (arrays, strings, Maps, Sets)
      - No funciona con objetos normales a menos que sea en contexto de objeto literal

4. **Rest agrupa todo en un array**
      - Los parámetros rest siempre crean un array, incluso si no se pasan argumentos (array vacío)
      - Esto puede causar inconsistencias si esperas otro tipo de dato

### Mejores prácticas

1. Usa spread para crear copias no destructivas de arrays y objetos
2. Usa rest en funciones que necesitan manejar un número variable de argumentos
3. Coloca el parámetro rest siempre al final
4. Para copias profundas, complementa con soluciones específicas (estructuradamente o con bibliotecas)
5. Considera el rendimiento al trabajar con estructuras de datos muy grandes

---

## 5. Template Literals

### Definición

Los template literals (literales de plantilla) son una nueva forma de trabajar con strings en JavaScript introducida en ES6 que permite expresiones incrustadas y strings multilínea. Se definen usando backticks (`` ` ``) en lugar de comillas simples o dobles. Sus características principales son:

- **Interpolación de expresiones**: Permite incrustar expresiones JavaScript directamente en el string usando la sintaxis `${expresión}`. La expresión se evalúa y su resultado se convierte a string.

- **Strings multilínea**: Permiten escribir texto en varias líneas sin necesidad de caracteres de escape especiales (`\n`) o concatenación.

- **Tagged Templates**: Una característica avanzada que permite procesar el template literal con una función (el "tag") para manipulación personalizada. Es el fundamento de bibliotecas como styled-components y graphql-tag.

- **Raw Strings**: Acceso a la representación "cruda" del string sin procesar escapes usando `String.raw`.

A nivel del motor de JavaScript, los template literals son convertidos a strings regulares después de evaluar las expresiones incrustadas. El proceso implica:

1. Identificar todas las expresiones dentro de `${ }`
2. Evaluar cada expresión en el contexto actual
3. Convertir los resultados a string (usando toString() implícitamente)
4. Concatenar las partes literales con los resultados

### Comparación detallada: Strings tradicionales vs Template Literals

| Característica | Strings tradicionales | Template Literals |
|----------------|------------------------|-------------------|
| **Delimitador** | `''` o `""` | `` ` ` `` (backticks) |
| **Multilínea** | Requiere `\n` | Soporte nativo |
| **Interpolación** | Concatenación con `+` | Integrada con `${}` |
| **Expresiones** | Fuera del string | Dentro del string |
| **Caracteres especiales** | Requiere escapes | Menos escapes necesarios |
| **Procesamiento personalizado** | No soportado | Soportado (tagged templates) |

### Ejemplos clave

```javascript
// 1. Interpolación básica de variables
const nombre = "María";
const edad = 28;

// Forma tradicional
const mensajeTradicional = "Hola, mi nombre es " + nombre + " y tengo " + edad + " años.";

// Con template literals
const mensajeModerno = `Hola, mi nombre es ${nombre} y tengo ${edad} años.`;

// 2. Expresiones dentro de la interpolación
const precio = 19.99;
const cantidad = 3;

const total = `El total es $${(precio * cantidad).toFixed(2)}`;
console.log(total);  // "El total es $59.97"

// 3. Strings multilínea
// Forma tradicional
const htmlTradicional = "<div>\n" +
                        "  <h1>Título</h1>\n" +
                        "  <p>Párrafo</p>\n" +
                        "</div>";

// Con template literals
const htmlModerno = `<div>
  <h1>Título</h1>
  <p>Párrafo</p>
</div>`;

// 4. Plantillas anidadas
const autores = ["Ana", "Luis", "Carlos"];
const categoria = "Tecnología";

const listaHTML = `
<h2>Artículos de ${categoria}</h2>
<ul>
  ${autores.map(autor => `<li>${autor}</li>`).join('')}
</ul>
`;

// 5. Tagged templates (avanzado)
function resaltar(strings, ...valores) {
  return strings.reduce((resultado, str, i) => {
    const valor = valores[i] || '';
    return resultado + str + (valor ? `<strong>${valor}</strong>` : '');
  }, '');
}

const producto = "Laptop";
const precio = 1299.99;

const mensaje = resaltar`El producto ${producto} cuesta $${precio}`;
console.log(mensaje);  // "El producto <strong>Laptop</strong> cuesta $<strong>1299.99</strong>"

// 6. String.raw (caracteres de escape sin procesar)
console.log(`Salto de línea:\n`);  // Muestra un salto de línea real
console.log(String.raw`Salto de línea:\n`);  // Muestra "\n" como texto
```

### Ejemplo práctico: Generación de HTML
```javascript
// Datos de un producto
const producto = {
  nombre: "Smartwatch XYZ",
  precio: 250000,
  caracteristicas: ["Resistente al agua", "Monitor cardíaco", "GPS"],
  imagen: "watch.jpg",
  disponible: true
};

// Generar HTML para mostrar el producto (común en desarrollo web)
const plantillaProducto = `
  <div class="producto ${producto.disponible ? 'en-stock' : 'agotado'}">
    <img src="img/${producto.imagen}" alt="${producto.nombre}">
    <h2>${producto.nombre}</h2>
    <p class="precio">$${producto.precio.toLocaleString('es-CO')}</p>
    
    <h3>Características:</h3>
    <ul>
      ${producto.caracteristicas.map(c => `<li>${c}</li>`).join('')}
    </ul>
    
    <button ${!producto.disponible ? 'disabled' : ''}>
      ${producto.disponible ? 'Comprar ahora' : 'Agotado'}
    </button>
  </div>
`;

console.log(plantillaProducto);
// HTML formateado con todos los datos del producto
```

### Errores comunes y cómo evitarlos

1. **Usar interpolación en strings normales**
   ```javascript
   // ERROR COMÚN
   const nombre = "Ana";
   const mensaje = "Hola ${nombre}";  // Resultado literal: "Hola ${nombre}"
   
   // CORRECTO
   const mensaje = `Hola ${nombre}`;  // "Hola Ana"
   ```

2. **Olvidar convertir valores no string**
   ```javascript
   // ERROR COMÚN (aunque no siempre visible)
   const obj = { clave: "valor" };
   console.log(`Objeto: ${obj}`);  // "Objeto: [object Object]" (no muy útil)
   
   // CORRECTO
   console.log(`Objeto: ${JSON.stringify(obj)}`);  // "Objeto: {"clave":"valor"}"
   ```

3. **Espacios inesperados en multilínea**
   ```javascript
   // ERROR COMÚN: espacios/indentación incluidos en el resultado
   const sql = `SELECT *
                FROM usuarios
                WHERE id = 1`;  // Los espacios antes de "FROM" y "WHERE" están en el string
   
   // CORRECTO: usar trim() o cuidar la indentación
   const sql = `
     SELECT *
     FROM usuarios
     WHERE id = 1
   `.trim().replace(/^\s+/gm, '');
   ```

4. **Confusión con backticks anidados**
   ```javascript
   // ERROR COMÚN
   const html = `<button onclick="alert(`Hola`)">Click</button>`;  // Error de sintaxis
   
   // CORRECTO
   const html = `<button onclick="alert('Hola')">Click</button>`;  // Usar comillas diferentes
   const html = `<button onclick="alert(\`Hola\`)">Click</button>`;  // Escapar backticks
   ```

### Limitaciones

1. **No existe interpolación "cruda"**
    - No hay sintaxis nativa para insertar texto sin procesar/escapar
    - Las expresiones siempre se convierten a string

2. **Tagged templates no estándar entre frameworks**
    - Cada biblioteca implementa sus propios "tags" de manera diferente
    - No hay un estándar para pasar metadatos o configuraciones

3. **Rendimiento con muchas interpolaciones**
    - Un exceso de expresiones `${}` puede afectar rendimiento
    - El uso excesivo puede generar garbage collection

4. **Posibles problemas de seguridad**
    - Interpolación directa de datos de usuario puede provocar XSS si se usa en HTML

### Mejores prácticas

1. Usa template literals para cualquier string que contenga valores dinámicos
2. Aprovecha la capacidad multilínea para código HTML, SQL o texto largo
3. Considera el uso de tagged templates para casos especiales (HTML sanitization, SQL escaping)
4. Usa `JSON.stringify()` para interpolar objetos de forma legible
5. Mantén la legibilidad cuidando la indentación, especialmente en strings largos

---

## 6. Métodos Modernos de Arrays

### Definición

Los métodos modernos de arrays son un conjunto de métodos de alto nivel que permiten manipular arrays de manera más declarativa, con código más conciso y funcional. Estos métodos son funciones integradas que operan en cada elemento del array, devolviendo un nuevo resultado basado en una función callback proporcionada.

Características fundamentales:

- **Higher-order functions**: Reciben funciones como argumentos (callbacks)
- **Funciones puras** (la mayoría): No modifican el array original (excepto `sort()` y `splice()`)
- **Declarativas**: Expresan "qué" se quiere hacer en lugar de "cómo" hacerlo paso a paso
- **Encadenables**: Permiten crear "pipelines" de transformación de datos
- **Reducen la mutabilidad**: Ayudan a mantener un estilo de programación más inmutable
- **Evitan loops explícitos**: Menos propensos a errores de índices y condiciones de salida

El callback recibe típicamente tres argumentos:

1. `elemento`: El elemento actual que se está procesando
2. `índice` (opcional): La posición del elemento en el array
3. `array` (opcional): El array completo sobre el que se llamó al método

### Comparación detallada de los métodos principales

#### Métodos de iteración y transformación

| Método | Propósito | Devuelve | Modifica el original |
|--------|-----------|----------|----------------------|
| **`map()`** | Transformar cada elemento | Nuevo array transformado | No |
| **`filter()`** | Seleccionar elementos que cumplan condición | Nuevo array filtrado | No |
| **`find()`** | Encontrar el primer elemento que cumpla condición | Elemento o undefined | No |
| **`findIndex()`** | Encontrar posición del primer elemento | Índice o -1 | No |
| **`forEach()`** | Ejecutar código para cada elemento | undefined | No (pero el callback puede modificar) |
| **`some()`** | Verificar si algún elemento cumple condición | Boolean | No |
| **`every()`** | Verificar si todos los elementos cumplen condición | Boolean | No |
| **`reduce()`** | Acumular valores de array en un solo resultado | Valor acumulado | No |
| **`includes()`** | Verificar si array contiene un valor | Boolean | No |
| **`indexOf()`** | Encontrar primera posición de un valor | Índice o -1 | No |

### Ejemplos detallados de cada método clave

```javascript
const productos = [
  { id: 1, nombre: "Laptop", precio: 1200, stock: 5, categorias: ["electrónica", "computación"] },
  { id: 2, nombre: "Celular", precio: 800, stock: 10, categorias: ["electrónica", "móviles"] },
  { id: 3, nombre: "Auriculares", precio: 100, stock: 20, categorias: ["electrónica", "audio"] },
  { id: 4, nombre: "Monitor", precio: 300, stock: 3, categorias: ["electrónica", "computación"] },
  { id: 5, nombre: "Teclado", precio: 80, stock: 0, categorias: ["electrónica", "computación", "periféricos"] }
];

// 1. map() - Transformar cada elemento
// Obtener solo nombres y precios
const catalogoSimplificado = productos.map(producto => ({
  nombre: producto.nombre,
  precio: producto.precio
}));
console.log(catalogoSimplificado);
// [{nombre: "Laptop", precio: 1200}, {nombre: "Celular", precio: 800}, ...]

// Aplicar formato de precios
const preciosFormateados = productos.map(producto => 
  `${producto.nombre}: $${producto.precio.toLocaleString('es-CO')}`
);
console.log(preciosFormateados);
// ["Laptop: $1.200", "Celular: $800", ...]

// 2. filter() - Seleccionar elementos según condición
// Productos en stock
const productosDisponibles = productos.filter(producto => producto.stock > 0);
console.log(productosDisponibles.length);  // 4

// Productos de una categoría específica
const productosComputacion = productos.filter(producto => 
  producto.categorias.includes("computación")
);
console.log(productosComputacion.length);  // 3

// Productos en oferta (precio < 100)
const productosBaratos = productos.filter(producto => producto.precio < 100);
console.log(productosBaratos.length);  // 1

// 3. find() - Encontrar un elemento específico
// Buscar por ID
const productoID3 = productos.find(producto => producto.id === 3);
console.log(productoID3.nombre);  // "Auriculares"

// Buscar primer producto agotado
const primerAgotado = productos.find(producto => producto.stock === 0);
console.log(primerAgotado?.nombre);  // "Teclado"

// Si no existe
const productoInexistente = productos.find(producto => producto.precio > 2000);
console.log(productoInexistente);  // undefined

// 4. findIndex() - Encontrar posición
// Encontrar índice para actualización
const indiceTeclado = productos.findIndex(producto => producto.nombre === "Teclado");
console.log(indiceTeclado);  // 4

// Producto no encontrado
const indiceInexistente = productos.findIndex(producto => producto.nombre === "Mouse");
console.log(indiceInexistente);  // -1

// Actualizar elemento encontrado
if (indiceTeclado !== -1) {
  const productosActualizados = [...productos];  // Copia para inmutabilidad
  productosActualizados[indiceTeclado] = {
    ...productos[indiceTeclado],
    stock: 5
  };
  // productosActualizados tendría el teclado con stock actualizado
}

// 5. some() - ¿Alguno cumple la condición?
// Verificar si hay productos agotados
const hayAgotados = productos.some(producto => producto.stock === 0);
console.log(hayAgotados);  // true

// Verificar si hay productos muy caros
const hayProductosCaros = productos.some(producto => producto.precio > 1500);
console.log(hayProductosCaros);  // false

// Verificar si hay productos de categoría específica
const hayAudio = productos.some(producto => 
  producto.categorias.includes("audio")
);
console.log(hayAudio);  // true

// 6. every() - ¿Todos cumplen la condición?
// Verificar si todos tienen precio
const todosTienenPrecio = productos.every(producto => producto.precio > 0);
console.log(todosTienenPrecio);  // true

// Verificar si todos están en stock
const todosDisponibles = productos.every(producto => producto.stock > 0);
console.log(todosDisponibles);  // false

// Verificar si todos pertenecen a electrónica
const todosElectronica = productos.every(producto => 
  producto.categorias.includes("electrónica")
);
console.log(todosElectronica);  // true

// 7. reduce() - Acumular resultados
// Calcular valor total del inventario
const valorTotal = productos.reduce((total, producto) => 
  total + (producto.precio * producto.stock), 0
);
console.log(valorTotal);  // 1200*5 + 800*10 + 100*20 + 300*3 + 80*0 = 14900

// Agrupar por categoría (más avanzado)
const porCategoria = productos.reduce((agrupado, producto) => {
  producto.categorias.forEach(categoria => {
    if (!agrupado[categoria]) {
      agrupado[categoria] = [];
    }
    agrupado[categoria].push(producto);
  });
  return agrupado;
}, {});
console.log(Object.keys(porCategoria));  // ["electrónica", "computación", "móviles", "audio", "periféricos"]

// 8. forEach() - Iterar sin transformar
// Mostrar inventario
productos.forEach(producto => {
  console.log(`${producto.nombre}: ${producto.stock} unidades disponibles`);
});
```

### Ejemplo práctico: Lista de tareas
```javascript
// Lista de tareas pendientes
const tareas = [
  { id: 1, descripcion: "Hacer compras", completada: false, prioridad: "alta" },
  { id: 2, descripcion: "Estudiar JavaScript", completada: false, prioridad: "alta" },
  { id: 3, descripcion: "Hacer ejercicio", completada: true, prioridad: "media" },
  { id: 4, descripcion: "Llamar al médico", completada: false, prioridad: "baja" },
  { id: 5, descripcion: "Limpiar casa", completada: true, prioridad: "media" }
];

// 1. Filtrar tareas pendientes (no completadas)
const tareasPendientes = tareas.filter(tarea => !tarea.completada);
console.log("Tareas pendientes:", tareasPendientes);

// 2. Filtrar tareas completadas
const tareasCompletadas = tareas.filter(tarea => tarea.completada);
console.log("Tareas completadas:", tareasCompletadas);

// 3. Encontrar una tarea específica
const tareaEstudiar = tareas.find(tarea => 
  tarea.descripcion.includes("Estudiar")
);
console.log("Tarea de estudio:", tareaEstudiar);

// 4. Verificar si hay tareas de alta prioridad pendientes
const hayPrioritarias = tareas.some(tarea => 
  tarea.prioridad === "alta" && !tarea.completada
);
console.log("¿Hay tareas prioritarias pendientes?", hayPrioritarias);

// 5. Verificar si todas las tareas están completadas
const todasCompletadas = tareas.every(tarea => tarea.completada);
console.log("¿Todas las tareas están completadas?", todasCompletadas);

// 6. Crear array con solo las descripciones
const descripciones = tareas.map(tarea => tarea.descripcion);
console.log("Lista de descripciones:", descripciones);

// 7. Encontrar la posición de una tarea para actualizarla
const posicionLlamada = tareas.findIndex(tarea => 
  tarea.descripcion.includes("Llamar")
);

if (posicionLlamada !== -1) {
  // Copia del array para no modificar el original
  const tareasActualizadas = [...tareas];
  // Actualizar la tarea encontrada
  tareasActualizadas[posicionLlamada].completada = true;
  console.log("Tareas después de actualizar:", tareasActualizadas);
}
```

### Casos de uso combinados

```javascript
// Ejemplo de e-commerce: filtrar, transformar y calcular
// 1. Filtrar productos en stock y en oferta
// 2. Calcular precio con impuesto
// 3. Obtener total

const productosEnOferta = productos
  .filter(p => p.stock > 0 && p.precio < 500)  // Solo disponibles y en oferta
  .map(p => ({                                 // Añadir cálculos
    ...p,
    impuesto: p.precio * 0.19,
    precioFinal: p.precio * 1.19
  }))
  .sort((a, b) => a.precioFinal - b.precioFinal);  // Ordenar por precio

const totalImpuestos = productosEnOferta.reduce((sum, p) => sum + p.impuesto, 0);
const totalInventarioOferta = productosEnOferta.reduce(
  (sum, p) => sum + (p.precioFinal * p.stock), 0
);
```

### Errores comunes y cómo evitarlos

1. **Intentar modificar el array original con métodos inmutables**
   ```javascript
   // ERROR COMÚN
   const numeros = [1, 2, 3];
   numeros.map(n => n * 2);  // No guarda el resultado
   console.log(numeros);  // Sigue siendo [1, 2, 3]
   
   // CORRECTO
   const numeros = [1, 2, 3];
   const duplicados = numeros.map(n => n * 2);
   console.log(duplicados);  // [2, 4, 6]
   ```

2. **Olvidar el return en funciones con cuerpo**
   ```javascript
   // ERROR COMÚN
   const filtrados = numeros.filter(n => {
     n > 5;  // Sin return, siempre devuelve undefined (evaluado como false)
   });
   
   // CORRECTO
   const filtrados = numeros.filter(n => {
     return n > 5;  // Con return explícito
   });
   // O mejor aún
   const filtrados = numeros.filter(n => n > 5);  // Implícito sin llaves
   ```

3. **Uso incorrecto de `forEach` para crear un nuevo array**
   ```javascript
   // ERROR COMÚN
   let transformados = [];
   numeros.forEach(n => {
     transformados.push(n * 2);  // Mutación, efectos secundarios
   });
   
   // CORRECTO: Usar map que ya devuelve un array
   const transformados = numeros.map(n => n * 2);
   ```

4. **Encadenar métodos sin considerar rendimiento**
   ```javascript
   // ERROR COMÚN: Múltiples iteraciones innecesarias
   const resultado = datos
     .filter(d => d.activo)
     .map(d => d.valor)
     .filter(v => v > 0)
     .map(v => v.toFixed(2));
   
   // MEJOR: Consolidar operaciones
   const resultado = datos
     .reduce((acc, d) => {
       if (d.activo && d.valor > 0) {
         acc.push(d.valor.toFixed(2));
       }
       return acc;
     }, []);
   ```

5. **Confundir `find` con `filter`**
   ```javascript
   // ERROR COMÚN: Usar find y esperar array
   const usuarios = [{id: 1, nombre: "Ana"}, {id: 2, nombre: "Luis"}];
   const ana = usuarios.find(u => u.nombre.includes("A"));
   ana.forEach(u => console.log(u));  // Error: ana no es un array
   
   // CORRECTO
   // Usar find para UN elemento
   const ana = usuarios.find(u => u.nombre.includes("A"));
   // Usar filter para múltiples
   const usuariosConA = usuarios.filter(u => u.nombre.includes("A"));
   ```

### Limitaciones

1. **Rendimiento con arrays grandes**
      - Para operaciones complejas con arrays muy grandes, los métodos funcionales pueden ser más lentos que los bucles tradicionales
      - `reduce` puede ser especialmente problemático si se usa incorrectamente

2. **Debugging más complejo**
      - Las cadenas de métodos pueden ser difíciles de depurar
      - Difícil identificar en qué paso ocurrió un error

3. **No modifican el array original**
      - Aunque generalmente es una ventaja, puede requerir asignaciones adicionales cuando se necesita modificación in-place

4. **Interrupción temprana limitada**
      - A diferencia de los bucles `for` con `break`, la mayoría de métodos (excepto `some`, `find`, etc.) siempre procesan todo el array

### Mejores prácticas

1. Usar `map` para transformaciones, `filter` para filtrado, `find` para búsquedas
2. Preferir métodos de array sobre bucles for/while para código más declarativo
3. Mantener funciones callback pequeñas y legibles
4. Para operaciones complejas, considerar `reduce` o separar en pasos
5. Evaluar el rendimiento con arrays muy grandes (>10,000 elementos)
6. Encadenar métodos en líneas separadas para mejor legibilidad

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
// 5. ¿Hay algún estudiante reprobado (calificación < 3)?
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

## 🚀 Consejos finales para JavaScript moderno

1. **Preferir declaraciones const/let sobre var**
      - Usar const por defecto, let solo cuando sea necesario, evitar var

2. **Adoptar un estilo más funcional y declarativo**
      - Preferir métodos de array como map/filter/reduce sobre loops imperativos

3. **Usar destructuring para extraer datos**
      - Especialmente útil en parámetros de función y resultados de APIs

4. **Aprovechar arrow functions para callbacks y funciones pequeñas**
      - Mejoran la legibilidad y solucionan problemas con this

5. **Mantener la inmutabilidad cuando sea posible**
      - Usar spread para crear copias en lugar de modificar objetos o arrays

6. **Utilizar template literals para strings dinámicos**
      - Especialmente útiles para generar HTML o mensajes con variables

7. **Organizar el código en módulos ES6**
      - Usar import/export para mejor organización y mantenibilidad

8. **Aprovechar valores por defecto en parámetros**
      - Mejora la robustez sin necesidad de verificaciones adicionales

9. **Considerar async/await para código asíncrono**
      - Más fácil de leer y mantener que cadenas de promesas

10. **Pensar en términos de transformaciones de datos**
    - El flujo de datos moderno se basa en transformar, no en modificar
