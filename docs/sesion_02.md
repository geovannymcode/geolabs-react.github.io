# 🎮 Manejo de Estado con `useState` y Eventos

## 📋 Índice
1. [¿Qué es el estado y por qué lo necesitamos?](#qué-es-el-estado)
2. [Entendiendo useState paso a paso](#entendiendo-usestate)
3. [Eventos en React explicados](#eventos-en-react)
4. [Formularios controlados](#formularios-controlados)
5. [Comunicación entre componentes](#comunicación-entre-componentes)
6. [Ejemplos prácticos completos](#ejemplos-prácticos)
7. [Errores comunes y soluciones](#errores-comunes)
8. [Ejercicios guiados](#ejercicios-guiados)

---

## 🤔 ¿Qué es el Estado y Por Qué lo Necesitamos?

### El problema sin estado

Imagina este componente:
```jsx
function Contador() {
  let cuenta = 0;
  
  const incrementar = () => {
    cuenta = cuenta + 1;
    console.log(cuenta); // Muestra 1, 2, 3...
  };
  
  return (
    <div>
      <p>Cuenta: {cuenta}</p>  {/* Siempre muestra 0 😢 */}
      <button onClick={incrementar}>+1</button>
    </div>
  );
}
```

**¿Por qué no funciona?**
- La variable `cuenta` se reinicia en cada render
- React no sabe que debe actualizar la UI
- No hay conexión entre la variable y lo que se muestra

### La solución: Estado con useState

```jsx
import { useState } from 'react';

function Contador() {
  const [cuenta, setCuenta] = useState(0);
  
  const incrementar = () => {
    setCuenta(cuenta + 1); // ¡Ahora sí funciona! 🎉
  };
  
  return (
    <div>
      <p>Cuenta: {cuenta}</p>
      <button onClick={incrementar}>+1</button>
    </div>
  );
}
```

### ¿Qué es el estado?

El estado es:
- 📦 **Memoria del componente** entre renders
- 🔄 **Reactivo**: Cambios causan re-renderizado
- 🏠 **Local**: Cada componente tiene su propio estado
- 🎯 **La fuente de verdad** para datos dinámicos

**Analogía del mundo real:**
```
Estado = La memoria de tu teléfono
- Persiste entre reinicios de pantalla
- Cuando cambias algo, la pantalla se actualiza
- Cada app tiene su propia memoria
```

---

## 🔧 Entendiendo useState Paso a Paso

### Anatomía de useState

```jsx
const [valor, setValor] = useState(valorInicial);
//     ↑        ↑                      ↑
//  variable  función            valor por defecto
//   actual   setter
```

### Desglose completo

```jsx
import { useState } from 'react'; // 1. Importar el hook

function MiComponente() {
  // 2. Declarar estado
  const [nombre, setNombre] = useState("Juan");
  //      ↑         ↑                    ↑
  //   getter    setter            valor inicial
  
  // 3. Leer el estado
  console.log(nombre); // "Juan"
  
  // 4. Actualizar el estado
  const cambiarNombre = () => {
    setNombre("María"); // Trigger re-render
  };
  
  // 5. Usar en JSX
  return <h1>Hola {nombre}</h1>;
}
```

### Tipos de datos en useState

```jsx
// 1. String
const [texto, setTexto] = useState("");

// 2. Número
const [edad, setEdad] = useState(0);

// 3. Boolean
const [activo, setActivo] = useState(false);

// 4. Array
const [items, setItems] = useState([]);

// 5. Objeto
const [usuario, setUsuario] = useState({
  nombre: "",
  email: ""
});

// 6. Null/undefined
const [dato, setDato] = useState(null);
```

### El ciclo de vida del estado

```
1. Componente se monta → useState(valorInicial)
                ↓
2. Usuario interactúa → Evento dispara setter
                ↓
3. setter(nuevoValor) → React programa re-render
                ↓
4. Componente re-renderiza → UI actualizada
                ↓
5. Vuelve al paso 2
```

---

## 🎯 Eventos en React Explicados

### Eventos sintéticos vs nativos

React envuelve los eventos nativos del DOM en "Synthetic Events" para:
- ✅ Compatibilidad entre navegadores
- ✅ Mejor rendimiento
- ✅ API consistente

### Eventos más comunes

#### 1. **onClick - Eventos de clic**
```jsx
function BotonEjemplo() {
  // Formas de manejar onClick
  
  // Forma 1: Función inline
  return (
    <button onClick={() => console.log("Clic!")}>
      Clic inline
    </button>
  );
  
  // Forma 2: Función definida
  const manejarClic = () => {
    console.log("Clic!");
  };
  
  return (
    <button onClick={manejarClic}>
      Clic con función
    </button>
  );
  
  // Forma 3: Con parámetros
  const saludar = (nombre) => {
    alert(`Hola ${nombre}`);
  };
  
  return (
    <button onClick={() => saludar("Juan")}>
      Saludar
    </button>
  );
}
```

#### 2. **onChange - Cambios en inputs**
```jsx
function InputEjemplo() {
  const [valor, setValor] = useState("");
  
  const manejarCambio = (evento) => {
    // evento.target = el input
    // evento.target.value = texto actual
    setValor(evento.target.value);
  };
  
  return (
    <div>
      <input 
        type="text"
        value={valor}
        onChange={manejarCambio}
      />
      <p>Escribiste: {valor}</p>
    </div>
  );
}
```

#### 3. **onSubmit - Envío de formularios**
```jsx
function FormularioEjemplo() {
  const [email, setEmail] = useState("");
  
  const manejarEnvio = (evento) => {
    evento.preventDefault(); // IMPORTANTE!
    console.log("Enviando:", email);
  };
  
  return (
    <form onSubmit={manejarEnvio}>
      <input 
        type="email"
        value={email}
        onChange={(e) => setEmail(e.target.value)}
      />
      <button type="submit">Enviar</button>
    </form>
  );
}
```

### Tabla completa de eventos

| Evento | Uso | Ejemplo |
|--------|-----|---------|
| `onClick` | Clics en elementos | `<button onClick={fn}>` |
| `onChange` | Cambios en inputs | `<input onChange={fn}>` |
| `onSubmit` | Envío de forms | `<form onSubmit={fn}>` |
| `onFocus` | Elemento recibe foco | `<input onFocus={fn}>` |
| `onBlur` | Elemento pierde foco | `<input onBlur={fn}>` |
| `onMouseEnter` | Mouse entra | `<div onMouseEnter={fn}>` |
| `onMouseLeave` | Mouse sale | `<div onMouseLeave={fn}>` |
| `onKeyDown` | Tecla presionada | `<input onKeyDown={fn}>` |
| `onKeyUp` | Tecla soltada | `<input onKeyUp={fn}>` |

### El objeto evento

```jsx
function EventoDetallado() {
  const manejarEvento = (evento) => {
    console.log({
      tipo: evento.type,           // "click", "change", etc.
      objetivo: evento.target,     // Elemento que disparó
      valor: evento.target.value,  // Para inputs
      tecla: evento.key,          // Para teclado
      posX: evento.clientX,       // Para mouse
      posY: evento.clientY        // Para mouse
    });
  };
  
  return <input onChange={manejarEvento} />;
}
```

---

## 📝 Formularios Controlados

### ¿Qué es un formulario controlado?

Un formulario donde React controla el valor de los inputs:

```jsx
// ❌ No controlado (DOM controla)
<input type="text" />

// ✅ Controlado (React controla)
<input type="text" value={estado} onChange={actualizar} />
```

### Ejemplo completo: Formulario de registro

```jsx
function FormularioRegistro() {
  // Estados para cada campo
  const [formData, setFormData] = useState({
    nombre: "",
    email: "",
    password: "",
    pais: "",
    terminos: false
  });
  
  // Manejador genérico para todos los campos
  const manejarCambio = (evento) => {
    const { name, value, type, checked } = evento.target;
    
    setFormData(prevData => ({
      ...prevData,
      [name]: type === 'checkbox' ? checked : value
    }));
  };
  
  // Envío del formulario
  const manejarEnvio = (evento) => {
    evento.preventDefault();
    
    // Validación básica
    if (!formData.nombre || !formData.email) {
      alert("Por favor completa todos los campos");
      return;
    }
    
    console.log("Datos del formulario:", formData);
    // Aquí enviarías a tu API
  };
  
  return (
    <form onSubmit={manejarEnvio}>
      {/* Campo de texto */}
      <input
        type="text"
        name="nombre"
        placeholder="Tu nombre"
        value={formData.nombre}
        onChange={manejarCambio}
      />
      
      {/* Campo email */}
      <input
        type="email"
        name="email"
        placeholder="tu@email.com"
        value={formData.email}
        onChange={manejarCambio}
      />
      
      {/* Campo password */}
      <input
        type="password"
        name="password"
        placeholder="Contraseña"
        value={formData.password}
        onChange={manejarCambio}
      />
      
      {/* Select */}
      <select 
        name="pais" 
        value={formData.pais} 
        onChange={manejarCambio}
      >
        <option value="">Selecciona país</option>
        <option value="mx">México</option>
        <option value="es">España</option>
        <option value="ar">Argentina</option>
      </select>
      
      {/* Checkbox */}
      <label>
        <input
          type="checkbox"
          name="terminos"
          checked={formData.terminos}
          onChange={manejarCambio}
        />
        Acepto términos y condiciones
      </label>
      
      <button type="submit">Registrarse</button>
    </form>
  );
}
```

### Ventajas de formularios controlados

1. **Single source of truth**: El estado es la única fuente
2. **Validación en tiempo real**: Puedes validar mientras escriben
3. **Formato automático**: Puedes formatear entrada (ej: teléfonos)
4. **Deshabilitación condicional**: Botones según validación

---

## 🔄 Comunicación Entre Componentes

### Patrón: Props down, Events up

```
        Padre (tiene el estado)
         ↓ props        ↑ eventos
        Hijo            Hijo
```

### Ejemplo: Lista de tareas interactiva

```jsx
// Componente Padre
function ListaTareas() {
  const [tareas, setTareas] = useState([
    { id: 1, texto: "Aprender React", completada: false },
    { id: 2, texto: "Practicar useState", completada: false }
  ]);
  
  // Función para marcar tarea como completada
  const toggleTarea = (id) => {
    setTareas(tareas.map(tarea =>
      tarea.id === id 
        ? { ...tarea, completada: !tarea.completada }
        : tarea
    ));
  };
  
  // Función para agregar tarea
  const agregarTarea = (texto) => {
    const nuevaTarea = {
      id: Date.now(),
      texto,
      completada: false
    };
    setTareas([...tareas, nuevaTarea]);
  };
  
  return (
    <div>
      <h1>Mis Tareas</h1>
      <FormularioTarea onAgregar={agregarTarea} />
      <ul>
        {tareas.map(tarea => (
          <Tarea
            key={tarea.id}
            tarea={tarea}
            onToggle={toggleTarea}
          />
        ))}
      </ul>
    </div>
  );
}

// Componente Hijo 1: Formulario
function FormularioTarea({ onAgregar }) {
  const [texto, setTexto] = useState("");
  
  const manejarSubmit = (e) => {
    e.preventDefault();
    if (texto.trim()) {
      onAgregar(texto);
      setTexto("");
    }
  };
  
  return (
    <form onSubmit={manejarSubmit}>
      <input
        type="text"
        value={texto}
        onChange={(e) => setTexto(e.target.value)}
        placeholder="Nueva tarea..."
      />
      <button type="submit">Agregar</button>
    </form>
  );
}

// Componente Hijo 2: Tarea individual
function Tarea({ tarea, onToggle }) {
  return (
    <li>
      <label>
        <input
          type="checkbox"
          checked={tarea.completada}
          onChange={() => onToggle(tarea.id)}
        />
        <span style={{
          textDecoration: tarea.completada ? 'line-through' : 'none'
        }}>
          {tarea.texto}
        </span>
      </label>
    </li>
  );
}
```

### Lifting State Up - Paso a paso

**Situación**: Dos componentes necesitan compartir estado

```jsx
// ❌ MAL: Estado duplicado
function AppMal() {
  return (
    <>
      <Contador1 /> {/* tiene su propio estado */}
      <Contador2 /> {/* tiene su propio estado */}
    </>
  );
}

// ✅ BIEN: Estado elevado
function AppBien() {
  const [conteoCompartido, setConteoCompartido] = useState(0);
  
  return (
    <>
      <Mostrador cuenta={conteoCompartido} />
      <Botones 
        cuenta={conteoCompartido}
        setCuenta={setConteoCompartido} 
      />
    </>
  );
}
```

---

## 💡 Ejemplos Prácticos Completos

### Ejemplo 1: Toggle de visibilidad

```jsx
function MostrarOcultar() {
  const [visible, setVisible] = useState(true);
  
  return (
    <div>
      <button onClick={() => setVisible(!visible)}>
        {visible ? 'Ocultar' : 'Mostrar'}
      </button>
      
      {visible && (
        <div style={{
          padding: '20px',
          background: '#f0f0f0',
          marginTop: '10px'
        }}>
          ¡Este contenido se puede ocultar!
        </div>
      )}
    </div>
  );
}
```

### Ejemplo 2: Contador con límites

```jsx
function ContadorLimitado() {
  const [contador, setContador] = useState(0);
  const MIN = 0;
  const MAX = 10;
  
  const incrementar = () => {
    setContador(prev => Math.min(prev + 1, MAX));
  };
  
  const decrementar = () => {
    setContador(prev => Math.max(prev - 1, MIN));
  };
  
  return (
    <div>
      <h2>Contador: {contador}</h2>
      <button 
        onClick={decrementar} 
        disabled={contador === MIN}
      >
        -1
      </button>
      <button 
        onClick={incrementar}
        disabled={contador === MAX}
      >
        +1
      </button>
      {contador === MAX && <p>¡Límite alcanzado!</p>}
    </div>
  );
}
```

### Ejemplo 3: Búsqueda en tiempo real

```jsx
function BuscadorPeliculas() {
  const [busqueda, setBusqueda] = useState("");
  const [peliculas] = useState([
    "Star Wars",
    "Matrix",
    "Inception",
    "Interstellar",
    "The Dark Knight"
  ]);
  
  const peliculasFiltradas = peliculas.filter(pelicula =>
    pelicula.toLowerCase().includes(busqueda.toLowerCase())
  );
  
  return (
    <div>
      <input
        type="text"
        placeholder="Buscar película..."
        value={busqueda}
        onChange={(e) => setBusqueda(e.target.value)}
      />
      
      <ul>
        {peliculasFiltradas.length > 0 ? (
          peliculasFiltradas.map(pelicula => (
            <li key={pelicula}>{pelicula}</li>
          ))
        ) : (
          <li>No se encontraron películas</li>
        )}
      </ul>
    </div>
  );
}
```

---

## ❌ Errores Comunes y Soluciones

### Error 1: Mutar el estado directamente

```jsx
// ❌ MAL - Mutación directa
const [user, setUser] = useState({ name: "Juan", age: 25 });

const updateAge = () => {
  user.age = 26;        // NO actualiza la UI
  setUser(user);       // React no detecta cambios
};

// ✅ BIEN - Crear nuevo objeto
const updateAge = () => {
  setUser({
    ...user,           // Copia propiedades existentes
    age: 26           // Sobrescribe age
  });
};
```

### Error 2: Cerraduras obsoletas (stale closures)

```jsx
// ❌ MAL - Usa valor obsoleto
const [count, setCount] = useState(0);

const incrementarVarias = () => {
  setCount(count + 1);  // count = 0
  setCount(count + 1);  // count sigue siendo 0
  setCount(count + 1);  // count sigue siendo 0
  // Resultado: count = 1 (no 3)
};

// ✅ BIEN - Usa función updater
const incrementarVarias = () => {
  setCount(prev => prev + 1);  // prev = 0, return 1
  setCount(prev => prev + 1);  // prev = 1, return 2
  setCount(prev => prev + 1);  // prev = 2, return 3
  // Resultado: count = 3
};
```

### Error 3: useState en condicionales

```jsx
// ❌ MAL - Hook condicional
function Componente({ mostrar }) {
  if (mostrar) {
    const [valor, setValor] = useState(0); // ERROR!
  }
}

// ✅ BIEN - Hook siempre se ejecuta
function Componente({ mostrar }) {
  const [valor, setValor] = useState(0);
  
  if (!mostrar) return null;
  
  return <div>{valor}</div>;
}
```

### Error 4: Olvidar preventDefault

```jsx
// ❌ MAL - La página se recarga
<form onSubmit={handleSubmit}>

// ✅ BIEN - Prevenir comportamiento por defecto
const handleSubmit = (e) => {
  e.preventDefault();
  // Tu lógica aquí
};
```

---

## 🎯 Ejercicios Guiados

### Ejercicio 1: Contador simple

```jsx
// TODO: Completa este componente
function Contador() {
  // 1. Declara estado para contar
  const [cuenta, setCuenta] = useState(0);
  
  // 2. Función para incrementar
  const incrementar = () => {
    setCuenta(cuenta + 1);
  };
  
  // 3. Función para resetear
  const resetear = () => {
    setCuenta(0);
  };
  
  return (
    <div>
      <h2>Cuenta: {cuenta}</h2>
      <button onClick={incrementar}>+1</button>
      <button onClick={resetear}>Reset</button>
    </div>
  );
}
```

### Ejercicio 2: Input controlado

```jsx
// TODO: Crea un saludo personalizado
function Saludo() {
  // 1. Estado para el nombre
  const [nombre, setNombre] = useState("");
  
  return (
    <div>
      <input
        type="text"
        placeholder="Tu nombre"
        value={nombre}
        onChange={(e) => setNombre(e.target.value)}
      />
      
      {nombre && <h2>¡Hola, {nombre}!</h2>}
    </div>
  );
}
```

### Ejercicio 3: Lista dinámica

```jsx
// TODO: Lista de compras
function ListaCompras() {
  const [items, setItems] = useState([]);
  const [nuevoItem, setNuevoItem] = useState("");
  
  const agregarItem = (e) => {
    e.preventDefault();
    if (nuevoItem.trim()) {
      setItems([...items, nuevoItem]);
      setNuevoItem("");
    }
  };
  
  const eliminarItem = (index) => {
    setItems(items.filter((_, i) => i !== index));
  };
  
  return (
    <div>
      <form onSubmit={agregarItem}>
        <input
          value={nuevoItem}
          onChange={(e) => setNuevoItem(e.target.value)}
          placeholder="Agregar item..."
        />
        <button type="submit">Agregar</button>
      </form>
      
      <ul>
        {items.map((item, index) => (
          <li key={index}>
            {item}
            <button onClick={() => eliminarItem(index)}>❌</button>
          </li>
        ))}
      </ul>
      
      {items.length === 0 && <p>Lista vacía</p>}
    </div>
  );
}
```

### Ejercicio 4: Componente completo

```jsx
// TODO: Sistema de votación
function SistemaVotacion() {
  const [opciones] = useState([
    { id: 1, nombre: "React", votos: 0 },
    { id: 2, nombre: "Vue", votos: 0 },
    { id: 3, nombre: "Angular", votos: 0 }
  ]);
  
  const [votaciones, setVotaciones] = useState(opciones);
  
  const votar = (id) => {
    setVotaciones(votaciones.map(opcion =>
      opcion.id === id
        ? { ...opcion, votos: opcion.votos + 1 }
        : opcion
    ));
  };
  
  const totalVotos = votaciones.reduce((sum, op) => sum + op.votos, 0);
  
  return (
    <div>
      <h2>¿Cuál prefieres?</h2>
      
      {votaciones.map(opcion => (
        <div key={opcion.id}>
          <button onClick={() => votar(opcion.id)}>
            {opcion.nombre}: {opcion.votos} votos
          </button>
          {totalVotos > 0 && (
            <span> ({((opcion.votos / totalVotos) * 100).toFixed(1)}%)</span>
          )}
        </div>
      ))}
      
      <p>Total de votos: {totalVotos}</p>
    </div>
  );
}
```

---

## 📚 Resumen y Mejores Prácticas

### Lo que aprendiste:

1. **useState**: Hook para manejar estado local
2. **Eventos**: Sistema unificado de React
3. **Formularios controlados**: React controla los valores
4. **Comunicación**: Props down, events up
5. **Patrones comunes**: Toggle, contadores, listas

### Mejores prácticas:

✅ **DO's**:
- Usa el updater function para estado basado en anterior
- Mantén el estado lo más simple posible
- Eleva el estado cuando sea compartido
- Usa formularios controlados
- Nombra handlers como `handleX` o `onX`

❌ **DON'Ts**:
- No mutes el estado directamente
- No uses hooks condicionalmente
- No olvides `key` en listas
- No olvides `preventDefault` en forms
- No sobre-optimices prematuramente