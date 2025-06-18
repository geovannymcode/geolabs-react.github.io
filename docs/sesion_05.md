# 🎯 Props en React

## 📋 Índice
1. [¿Qué son las Props y por qué las necesitamos?](#qué-son-las-props)
2. [Anatomía de las Props](#anatomía-props)
3. [Flujo de datos unidireccional](#flujo-datos)
4. [Tipos de datos en Props](#tipos-datos)
5. [Desestructuración de Props](#desestructuración)
6. [Props por defecto](#props-defecto)
7. [Validación con PropTypes](#validación)
8. [Patrones comunes con Props](#patrones-comunes)
9. [Props vs Estado](#props-vs-estado)
10. [Errores comunes y debugging](#errores-comunes)
11. [Ejercicios prácticos](#ejercicios-prácticos)

---

## 🤔 ¿Qué son las Props y por qué las necesitamos?

### El problema sin Props

Imagina que quieres mostrar información de usuarios:

```jsx
// ❌ SIN PROPS - Código duplicado y no reutilizable
function App() {
  return (
    <div>
      {/* Usuario 1 */}
      <div>
        <h2>Juan Pérez</h2>
        <p>juan@email.com</p>
        <p>28 años</p>
      </div>
      
      {/* Usuario 2 */}
      <div>
        <h2>María García</h2>
        <p>maria@email.com</p>
        <p>32 años</p>
      </div>
      
      {/* Usuario 3... copiar y pegar otra vez 😢 */}
    </div>
  )
}
```

### La solución con Props

```jsx
// ✅ CON PROPS - Componente reutilizable
function UsuarioCard({ nombre, email, edad }) {
  return (
    <div>
      <h2>{nombre}</h2>
      <p>{email}</p>
      <p>{edad} años</p>
    </div>
  )
}

function App() {
  return (
    <div>
      <UsuarioCard nombre="Juan Pérez" email="juan@email.com" edad={28} />
      <UsuarioCard nombre="María García" email="maria@email.com" edad={32} />
      <UsuarioCard nombre="Carlos López" email="carlos@email.com" edad={25} />
    </div>
  )
}
```

### ¿Qué son las Props?

**Props** = **Prop**ertie**s** (Propiedades)

Las props son:
- 📨 **Mensajes** del componente padre al hijo
- 🎁 **Paquetes de datos** que viajan hacia abajo
- 🔒 **Inmutables** - El hijo no puede modificarlas
- 🎯 **La forma** de hacer componentes configurables

### Analogía del mundo real

```
Props = Parámetros de una función

function saludar(nombre) {        // nombre es como una prop
  return `Hola ${nombre}`
}

saludar("Juan")                   // "Juan" es el valor de la prop
saludar("María")                  // Reutilizable con diferentes valores
```

---

## 🔧 Anatomía de las Props

### Estructura básica

```jsx
// COMPONENTE HIJO - Recibe props
function Saludo(props) {
  return <h1>Hola {props.nombre}</h1>
}

// COMPONENTE PADRE - Envía props
function App() {
  return <Saludo nombre="Juan" />
}
```

### Desglose del proceso

```
1. Padre define el valor:    nombre="Juan"
                                ↓
2. React empaqueta:         props = { nombre: "Juan" }
                                ↓
3. Hijo recibe:            function Saludo(props)
                                ↓
4. Hijo usa:               props.nombre → "Juan"
```

### Props como objeto

Las props SIEMPRE son un objeto:

```jsx
// Lo que escribes
<Saludo nombre="Juan" edad={25} activo={true} />

// Lo que React crea
props = {
  nombre: "Juan",
  edad: 25,
  activo: true
}

// Lo que el componente recibe
function Saludo(props) {
  console.log(props)
  // { nombre: "Juan", edad: 25, activo: true }
}
```

---

## 🔄 Flujo de Datos Unidireccional

### Dirección del flujo

```
        PADRE
          ↓ props
        HIJO
          ↓ props
      NIETO
```

**IMPORTANTE**: Los datos solo fluyen hacia abajo, nunca hacia arriba directamente.

### Ejemplo visual del flujo

```jsx
function Abuelo() {
  const apellido = "García"
  
  return <Padre apellidoFamilia={apellido} />
}

function Padre({ apellidoFamilia }) {
  const nombre = "Juan"
  
  return <Hijo nombre={nombre} apellido={apellidoFamilia} />
}

function Hijo({ nombre, apellido }) {
  return <p>{nombre} {apellido}</p>  // "Juan García"
}
```

### Diagrama del flujo

```
┌─────────────────────────────┐
│         ABUELO              │
│   apellido = "García"       │
└──────────┬──────────────────┘
           │ apellidoFamilia="García"
           ↓
┌─────────────────────────────┐
│         PADRE               │
│   nombre = "Juan"           │
└──────────┬──────────────────┘
           │ nombre="Juan"
           │ apellido="García"
           ↓
┌─────────────────────────────┐
│         HIJO                │
│   Muestra: Juan García      │
└─────────────────────────────┘
```

---

## 📦 Tipos de Datos en Props

### Todos los tipos permitidos

```jsx
function ComponenteEjemplo() {
  return (
    <MiComponente
      // Strings
      texto="Hola mundo"
      plantilla={`Hola ${nombre}`}
      
      // Números
      edad={25}
      precio={19.99}
      
      // Booleanos
      activo={true}
      visible={false}
      mostrar  // Sin valor = true
      
      // Arrays
      numeros={[1, 2, 3, 4, 5]}
      nombres={["Ana", "Luis", "Carlos"]}
      
      // Objetos
      usuario={{ nombre: "Juan", edad: 30 }}
      config={{ tema: "oscuro", idioma: "es" }}
      
      // Funciones
      onClick={() => console.log("Click!")}
      onChange={manejarCambio}
      
      // Componentes
      icono={<IconoEstrella />}
      contenido={<div>Contenido personalizado</div>}
      
      // Null, undefined
      dato={null}
      opcional={undefined}
    />
  )
}
```

### Ejemplos prácticos por tipo

#### 1. **Props de texto**
```jsx
function Tarjeta({ titulo, descripcion }) {
  return (
    <div>
      <h2>{titulo}</h2>
      <p>{descripcion}</p>
    </div>
  )
}

// Uso
<Tarjeta 
  titulo="React Props" 
  descripcion="Las props permiten pasar datos entre componentes"
/>
```

#### 2. **Props numéricas**
```jsx
function Producto({ nombre, precio, descuento }) {
  const precioFinal = precio - (precio * descuento / 100)
  
  return (
    <div>
      <h3>{nombre}</h3>
      <p>Precio: ${precio}</p>
      <p>Descuento: {descuento}%</p>
      <p>Precio final: ${precioFinal}</p>
    </div>
  )
}

// Uso
<Producto nombre="Laptop" precio={1000} descuento={15} />
```

#### 3. **Props booleanas**
```jsx
function Alerta({ mensaje, tipo, visible, puedeOcultar }) {
  if (!visible) return null
  
  return (
    <div className={`alerta alerta-${tipo}`}>
      {mensaje}
      {puedeOcultar && <button>×</button>}
    </div>
  )
}

// Uso
<Alerta 
  mensaje="Operación exitosa" 
  tipo="exito"
  visible={true}
  puedeOcultar
/>
```

#### 4. **Props con arrays**
```jsx
function ListaCompras({ items, titulo }) {
  return (
    <div>
      <h3>{titulo}</h3>
      <ul>
        {items.map((item, index) => (
          <li key={index}>{item}</li>
        ))}
      </ul>
    </div>
  )
}

// Uso
<ListaCompras 
  titulo="Lista del súper"
  items={["Leche", "Pan", "Huevos", "Frutas"]}
/>
```

#### 5. **Props con objetos**
```jsx
function PerfilUsuario({ usuario }) {
  return (
    <div>
      <h2>{usuario.nombre}</h2>
      <p>Email: {usuario.email}</p>
      <p>Edad: {usuario.edad}</p>
      <p>Ciudad: {usuario.direccion.ciudad}</p>
    </div>
  )
}

// Uso
const datosUsuario = {
  nombre: "Ana López",
  email: "ana@email.com",
  edad: 28,
  direccion: {
    ciudad: "Madrid",
    pais: "España"
  }
}

<PerfilUsuario usuario={datosUsuario} />
```

#### 6. **Props con funciones**
```jsx
function Boton({ texto, onClick, tipo = "primary" }) {
  return (
    <button 
      className={`btn btn-${tipo}`}
      onClick={onClick}
    >
      {texto}
    </button>
  )
}

// Uso
function App() {
  const manejarClick = () => {
    alert("¡Botón presionado!")
  }
  
  return (
    <Boton 
      texto="Hacer clic aquí" 
      onClick={manejarClick}
      tipo="success"
    />
  )
}
```

---

## 🎯 Desestructuración de Props

### Sin desestructuración

```jsx
function Producto(props) {
  return (
    <div>
      <h3>{props.nombre}</h3>
      <p>${props.precio}</p>
      <p>{props.descripcion}</p>
    </div>
  )
}
```

### Con desestructuración

```jsx
function Producto({ nombre, precio, descripcion }) {
  return (
    <div>
      <h3>{nombre}</h3>
      <p>${precio}</p>
      <p>{descripcion}</p>
    </div>
  )
}
```

### Ventajas de la desestructuración

1. **Código más limpio**: No repetir `props.`
2. **Más legible**: Ver qué props espera el componente
3. **Autocompletado**: Mejor soporte del editor

### Técnicas avanzadas de desestructuración

#### 1. **Valores por defecto en desestructuración**
```jsx
function Saludo({ nombre = "Invitado", saludo = "Hola" }) {
  return <h1>{saludo}, {nombre}!</h1>
}

// Uso
<Saludo />                    // "Hola, Invitado!"
<Saludo nombre="Juan" />      // "Hola, Juan!"
<Saludo nombre="Ana" saludo="Buenos días" />  // "Buenos días, Ana!"
```

#### 2. **Rest operator (...)**
```jsx
function Boton({ texto, tipo, ...otrasProps }) {
  return (
    <button 
      className={`btn btn-${tipo}`}
      {...otrasProps}  // onClick, disabled, etc.
    >
      {texto}
    </button>
  )
}

// Uso
<Boton 
  texto="Guardar"
  tipo="primary"
  onClick={guardar}
  disabled={guardando}
  data-testid="boton-guardar"
/>
```

#### 3. **Renombrar props**
```jsx
function Usuario({ nombre: nombreCompleto, email: correo }) {
  return (
    <div>
      <h3>{nombreCompleto}</h3>
      <p>{correo}</p>
    </div>
  )
}
```

#### 4. **Props anidadas**
```jsx
function Direccion({ usuario: { nombre, direccion: { ciudad, pais } } }) {
  return (
    <p>{nombre} vive en {ciudad}, {pais}</p>
  )
}

// Más legible: desestructurar en el cuerpo
function Direccion({ usuario }) {
  const { nombre, direccion } = usuario
  const { ciudad, pais } = direccion
  
  return (
    <p>{nombre} vive en {ciudad}, {pais}</p>
  )
}
```

---

## 🛡️ Props por Defecto

### Método 1: defaultProps (Clásico)

```jsx
function Tarjeta({ titulo, contenido, color }) {
  return (
    <div style={{ backgroundColor: color }}>
      <h3>{titulo}</h3>
      <p>{contenido}</p>
    </div>
  )
}

Tarjeta.defaultProps = {
  titulo: "Sin título",
  contenido: "Sin contenido",
  color: "#f0f0f0"
}
```

### Método 2: Valores por defecto ES6 (Moderno)

```jsx
function Tarjeta({ 
  titulo = "Sin título", 
  contenido = "Sin contenido", 
  color = "#f0f0f0" 
}) {
  return (
    <div style={{ backgroundColor: color }}>
      <h3>{titulo}</h3>
      <p>{contenido}</p>
    </div>
  )
}
```

### Método 3: Valores por defecto complejos

```jsx
function TablaUsuarios({ 
  usuarios = [], 
  columnas = ["nombre", "email", "edad"],
  ordenPor = "nombre",
  limite = 10 
}) {
  // Lógica del componente
}
```

### Cuándo usar valores por defecto

```jsx
// ✅ BIEN - Props opcionales con valores sensatos
function Paginacion({ 
  paginaActual = 1, 
  itemsPorPagina = 10,
  mostrarNumeros = true 
}) {
  // ...
}

// ❌ EVITAR - Props requeridas con defaults sin sentido
function Usuario({ 
  nombre = "SIN NOMBRE",  // Mejor hacerla requerida
  id = -1                 // Mejor validar que exista
}) {
  // ...
}
```

---

## ✅ Validación con PropTypes

### Instalación

```bash
npm install prop-types
```

### Uso básico

```jsx
import PropTypes from 'prop-types'

function Usuario({ nombre, edad, email, activo }) {
  return (
    <div>
      <h3>{nombre}</h3>
      <p>{email}</p>
      <p>{edad} años</p>
      <p>Estado: {activo ? "Activo" : "Inactivo"}</p>
    </div>
  )
}

Usuario.propTypes = {
  nombre: PropTypes.string.isRequired,
  edad: PropTypes.number.isRequired,
  email: PropTypes.string.isRequired,
  activo: PropTypes.bool
}

Usuario.defaultProps = {
  activo: true
}
```

### Tipos de validación disponibles

```jsx
ComponenteEjemplo.propTypes = {
  // Tipos básicos
  texto: PropTypes.string,
  numero: PropTypes.number,
  booleano: PropTypes.bool,
  funcion: PropTypes.func,
  objeto: PropTypes.object,
  arreglo: PropTypes.array,
  simbolo: PropTypes.symbol,
  
  // Cualquier cosa renderizable
  nodo: PropTypes.node,
  
  // Elemento React
  elemento: PropTypes.element,
  
  // Instancia de clase
  fecha: PropTypes.instanceOf(Date),
  
  // Valores específicos
  tipo: PropTypes.oneOf(['primario', 'secundario', 'peligro']),
  
  // Tipos múltiples
  id: PropTypes.oneOfType([
    PropTypes.string,
    PropTypes.number
  ]),
  
  // Array de tipo específico
  numeros: PropTypes.arrayOf(PropTypes.number),
  
  // Objeto con forma específica
  usuario: PropTypes.shape({
    id: PropTypes.number.isRequired,
    nombre: PropTypes.string.isRequired,
    email: PropTypes.string
  }),
  
  // Validación personalizada
  edad: function(props, propName, componentName) {
    if (props[propName] < 18) {
      return new Error(`${propName} debe ser mayor a 18`)
    }
  }
}
```

### Ejemplo completo con validaciones

```jsx
import PropTypes from 'prop-types'

function ProductoCard({ producto, onComprar, descuento }) {
  const precioFinal = producto.precio * (1 - descuento / 100)
  
  return (
    <div className="producto-card">
      <img src={producto.imagen} alt={producto.nombre} />
      <h3>{producto.nombre}</h3>
      <p>{producto.descripcion}</p>
      <p className="precio">
        ${producto.precio}
        {descuento > 0 && (
          <span className="descuento"> → ${precioFinal.toFixed(2)}</span>
        )}
      </p>
      <button onClick={() => onComprar(producto.id)}>
        Comprar
      </button>
    </div>
  )
}

ProductoCard.propTypes = {
  producto: PropTypes.shape({
    id: PropTypes.string.isRequired,
    nombre: PropTypes.string.isRequired,
    descripcion: PropTypes.string,
    precio: PropTypes.number.isRequired,
    imagen: PropTypes.string.isRequired
  }).isRequired,
  onComprar: PropTypes.func.isRequired,
  descuento: PropTypes.number
}

ProductoCard.defaultProps = {
  descuento: 0
}
```

---

## 🎨 Patrones Comunes con Props

### 1. **Props de renderizado (Render Props)**

```jsx
function ContenedorDatos({ render }) {
  const [datos, setDatos] = useState(null)
  const [cargando, setCargando] = useState(true)
  
  useEffect(() => {
    fetchDatos().then(data => {
      setDatos(data)
      setCargando(false)
    })
  }, [])
  
  return render({ datos, cargando })
}

// Uso
<ContenedorDatos 
  render={({ datos, cargando }) => (
    cargando ? <Spinner /> : <Lista items={datos} />
  )}
/>
```

### 2. **Props de composición (children)**

```jsx
function Tarjeta({ titulo, children, pie }) {
  return (
    <div className="tarjeta">
      <div className="tarjeta-header">
        <h3>{titulo}</h3>
      </div>
      <div className="tarjeta-body">
        {children}
      </div>
      {pie && (
        <div className="tarjeta-footer">
          {pie}
        </div>
      )}
    </div>
  )
}

// Uso
<Tarjeta titulo="Mi Tarjeta" pie={<button>Acción</button>}>
  <p>Este es el contenido de la tarjeta</p>
  <p>Puede ser cualquier cosa</p>
</Tarjeta>
```

### 3. **Props de configuración**

```jsx
function Tabla({ datos, columnas, ordenable, paginacion }) {
  return (
    <table>
      <thead>
        <tr>
          {columnas.map(col => (
            <th key={col.campo}>
              {col.titulo}
              {ordenable && <BotonOrdenar campo={col.campo} />}
            </th>
          ))}
        </tr>
      </thead>
      <tbody>
        {/* Renderizar filas */}
      </tbody>
      {paginacion && <Paginacion {...paginacion} />}
    </table>
  )
}

// Uso
<Tabla
  datos={usuarios}
  columnas={[
    { campo: 'nombre', titulo: 'Nombre' },
    { campo: 'email', titulo: 'Correo' }
  ]}
  ordenable
  paginacion={{ pagina: 1, total: 100 }}
/>
```

### 4. **Props de estado y acciones**

```jsx
function FormularioControlado({ valor, onChange, onSubmit, error }) {
  return (
    <form onSubmit={onSubmit}>
      <input 
        value={valor}
        onChange={e => onChange(e.target.value)}
        className={error ? 'error' : ''}
      />
      {error && <span className="mensaje-error">{error}</span>}
      <button type="submit">Enviar</button>
    </form>
  )
}
```

### 5. **Props spread pattern**

```jsx
function Input({ label, error, ...inputProps }) {
  return (
    <div className="campo-formulario">
      <label>{label}</label>
      <input {...inputProps} />
      {error && <span className="error">{error}</span>}
    </div>
  )
}

// Uso
<Input 
  label="Email"
  type="email"
  name="email"
  value={email}
  onChange={handleChange}
  required
  error={errores.email}
/>
```

---

## 🆚 Props vs Estado

### Diferencias clave

| Característica | Props | Estado |
|----------------|-------|---------|
| **Origen** | Viene del padre | Interno del componente |
| **Mutabilidad** | Inmutable (solo lectura) | Mutable (con setState) |
| **Actualización** | Solo el padre puede cambiar | El componente mismo |
| **Uso** | Configuración y datos | Datos que cambian |
| **Dirección** | Unidireccional ↓ | Local |

### Ejemplo comparativo

```jsx
// PROPS - Datos del padre
function ContadorControlado({ valor, onCambiar }) {
  return (
    <div>
      <p>Contador: {valor}</p>
      <button onClick={() => onCambiar(valor + 1)}>+1</button>
    </div>
  )
}

// ESTADO - Datos propios
function ContadorLocal() {
  const [valor, setValor] = useState(0)
  
  return (
    <div>
      <p>Contador: {valor}</p>
      <button onClick={() => setValor(valor + 1)}>+1</button>
    </div>
  )
}
```

### Cuándo usar cada uno

```jsx
// ✅ USA PROPS cuando:
// - El padre necesita controlar el valor
// - Varios componentes comparten el dato
// - Es configuración del componente

// ✅ USA ESTADO cuando:
// - El dato es privado del componente
// - Solo este componente lo modifica
// - Es un detalle de implementación
```

---

## 🐛 Errores Comunes y Debugging

### Error 1: Mutar props

```jsx
// ❌ MAL - Nunca modifiques props
function Componente({ usuario }) {
  usuario.nombre = "Nuevo nombre"  // ¡ERROR!
  return <div>{usuario.nombre}</div>
}

// ✅ BIEN - Crea una copia si necesitas modificar
function Componente({ usuario }) {
  const usuarioLocal = { ...usuario, nombre: "Nuevo nombre" }
  return <div>{usuarioLocal.nombre}</div>
}
```

### Error 2: Props no definidas

```jsx
// ❌ MAL - Puede causar error
function Perfil({ usuario }) {
  return <h1>{usuario.nombre}</h1>  // Error si usuario es undefined
}

// ✅ BIEN - Verificar existencia
function Perfil({ usuario }) {
  if (!usuario) return <p>Cargando...</p>
  return <h1>{usuario.nombre}</h1>
}

// ✅ MEJOR - Opcional chaining
function Perfil({ usuario }) {
  return <h1>{usuario?.nombre || "Sin nombre"}</h1>
}
```

### Error 3: Pasar objetos como prop key

```jsx
// ❌ MAL - Los objetos no funcionan como keys
{items.map(item => (
  <Item key={item} data={item} />  // item es objeto
))}

// ✅ BIEN - Usar propiedad única
{items.map(item => (
  <Item key={item.id} data={item} />
))}
```

### Error 4: Props con el mismo nombre que variables

```jsx
// ❌ CONFUSO
function Componente({ nombre }) {
  const nombre = "Otro nombre"  // Conflicto de nombres
  return <div>{nombre}</div>
}

// ✅ CLARO
function Componente({ nombreProp }) {
  const nombreLocal = "Otro nombre"
  return <div>{nombreProp} - {nombreLocal}</div>
}
```

### Herramientas de debugging

#### 1. **Console.log de props**
```jsx
function MiComponente(props) {
  console.log("Props recibidas:", props)
  // ...
}
```

#### 2. **React Developer Tools**
- Instalar extensión del navegador
- Inspeccionar componentes
- Ver props en tiempo real
- Buscar componentes por nombre

#### 3. **Validación con PropTypes**
```jsx
// Te avisa en consola si hay problemas
MiComponente.propTypes = {
  nombre: PropTypes.string.isRequired
}
```

---

## 💪 Ejercicios Prácticos

### Ejercicio 1: Card de Usuario

```jsx
// TODO: Crear un componente UserCard que reciba:
// - nombre (string, requerido)
// - avatar (string, opcional con default)
// - bio (string, opcional)
// - seguidores (number, default 0)

function UserCard({ 
  nombre, 
  avatar = "/default-avatar.png",
  bio,
  seguidores = 0 
}) {
  return (
    <div className="user-card">
      <img src={avatar} alt={nombre} />
      <h3>{nombre}</h3>
      {bio && <p>{bio}</p>}
      <span>{seguidores} seguidores</span>
    </div>
  )
}

// Agregar PropTypes
UserCard.propTypes = {
  nombre: PropTypes.string.isRequired,
  avatar: PropTypes.string,
  bio: PropTypes.string,
  seguidores: PropTypes.number
}
```

### Ejercicio 2: Lista de Productos

```jsx
// TODO: Crear componentes para mostrar productos
// ProductList recibe array de productos
// ProductItem recibe datos de un producto

function ProductItem({ nombre, precio, imagen, enStock }) {
  return (
    <div className={`product ${!enStock ? 'agotado' : ''}`}>
      <img src={imagen} alt={nombre} />
      <h4>{nombre}</h4>
      <p>${precio}</p>
      {!enStock && <span>Agotado</span>}
    </div>
  )
}

function ProductList({ productos, titulo = "Nuestros Productos" }) {
  return (
    <section>
      <h2>{titulo}</h2>
      <div className="product-grid">
        {productos.map(producto => (
          <ProductItem key={producto.id} {...producto} />
        ))}
      </div>
    </section>
  )
}

// Uso
const productos = [
  { id: 1, nombre: "Laptop", precio: 999, imagen: "...", enStock: true },
  { id: 2, nombre: "Mouse", precio: 29, imagen: "...", enStock: false }
]

<ProductList productos={productos} />
```

### Ejercicio 3: Formulario Configurable

```jsx
// TODO: Crear un Input reutilizable con props

function Input({ 
  label, 
  tipo = "text", 
  nombre, 
  valor, 
  onChange, 
  error,
  requerido = false,
  placeholder 
}) {
  const inputId = `input-${nombre}`
  
  return (
    <div className="form-field">
      <label htmlFor={inputId}>
        {label}
        {requerido && <span className="required">*</span>}
      </label>
      <input
        id={inputId}
        type={tipo}
        name={nombre}
        value={valor}
        onChange={onChange}
        placeholder={placeholder}
        required={requerido}
        className={error ? 'error' : ''}
      />
      {error && <span className="error-message">{error}</span>}
    </div>
  )
}

// Uso
function Formulario() {
  const [valores, setValores] = useState({ email: '', password: '' })
  const [errores, setErrores] = useState({})
  
  const handleChange = (e) => {
    setValores({
      ...valores,
      [e.target.name]: e.target.value
    })
  }
  
  return (
    <form>
      <Input
        label="Email"
        tipo="email"
        nombre="email"
        valor={valores.email}
        onChange={handleChange}
        error={errores.email}
        requerido
        placeholder="tu@email.com"
      />
      <Input
        label="Contraseña"
        tipo="password"
        nombre="password"
        valor={valores.password}
        onChange={handleChange}
        error={errores.password}
        requerido
      />
    </form>
  )
}
```

### Ejercicio 4: Componente de Notificación

```jsx
// TODO: Crear un sistema de notificaciones flexible

function Notificacion({ 
  tipo = "info", // "info", "exito", "advertencia", "error"
  titulo,
  mensaje,
  onCerrar,
  duracion = 5000, // ms antes de auto-cerrar
  icono = true,
  accion
}) {
  useEffect(() => {
    if (duracion && duracion > 0) {
      const timer = setTimeout(() => {
        onCerrar && onCerrar()
      }, duracion)
      
      return () => clearTimeout(timer)
    }
  }, [duracion, onCerrar])
  
  const iconos = {
    info: "ℹ️",
    exito: "✅",
    advertencia: "⚠️",
    error: "❌"
  }
  
  return (
    <div className={`notificacion notificacion-${tipo}`}>
      <div className="notificacion-contenido">
        {icono && <span className="notificacion-icono">{iconos[tipo]}</span>}
        <div>
          {titulo && <h4>{titulo}</h4>}
          <p>{mensaje}</p>
        </div>
      </div>
      <div className="notificacion-acciones">
        {accion && (
          <button onClick={accion.onClick}>
            {accion.texto}
          </button>
        )}
        {onCerrar && (
          <button onClick={onCerrar} className="cerrar">×</button>
        )}
      </div>
    </div>
  )
}

// Uso
function App() {
  const [notificaciones, setNotificaciones] = useState([])
  
  const agregarNotificacion = (config) => {
    const id = Date.now()
    setNotificaciones(prev => [...prev, { id, ...config }])
  }
  
  const quitarNotificacion = (id) => {
    setNotificaciones(prev => prev.filter(n => n.id !== id))
  }
  
  return (
    <div>
      <button onClick={() => agregarNotificacion({
        tipo: "exito",
        titulo: "¡Guardado!",
        mensaje: "Los cambios se guardaron correctamente"
      })}>
        Mostrar notificación
      </button>
      
      <div className="notificaciones-container">
        {notificaciones.map(notif => (
          <Notificacion
            key={notif.id}
            {...notif}
            onCerrar={() => quitarNotificacion(notif.id)}
          />
        ))}
      </div>
    </div>
  )
}
```

---

## 🎯 Resumen y Mejores Prácticas

### Checklist de Props

✅ **DO's - Hacer**:
1. Usar nombres descriptivos para props
2. Desestructurar props para código más limpio
3. Proporcionar valores por defecto para props opcionales
4. Validar props con PropTypes o TypeScript
5. Documentar props complejas
6. Mantener props inmutables

❌ **DON'Ts - No hacer**:
1. Modificar props directamente
2. Pasar demasiadas props (más de 7-8)
3. Usar nombres genéricos (data, info, config)
4. Pasar el estado completo si solo necesitas parte
5. Olvidar manejar props undefined

### Patrones recomendados

```jsx
// 1. Props bien nombradas y tipadas
function BotonAccion({ 
  texto,           // Qué muestra
  onClick,         // Qué hace
  variante = "primario",  // Cómo se ve
  deshabilitado = false,  // Estado
  cargando = false        // Estado de carga
}) {
  // ...
}

// 2. Agrupación lógica de props
function TarjetaProducto({ producto, acciones, configuracion }) {
  // Mejor que pasar 15 props individuales
}

// 3. Composición sobre configuración
// En lugar de:
<Modal 
  titulo="..."
  contenido="..."
  piePagina="..."
/>

// Mejor:
<Modal>
  <ModalTitulo>...</ModalTitulo>
  <ModalContenido>...</ModalContenido>
  <ModalPie>...</ModalPie>
</Modal>
```

### Evolución del uso de props

```jsx
// Nivel 1: Props básicas
<Boton texto="Click" />

// Nivel 2: Props con funciones
<Boton texto="Click" onClick={handleClick} />

// Nivel 3: Props con objetos y arrays
<Lista items={datos} onSeleccionar={item => ...} />

// Nivel 4: Composición y render props
<DataProvider render={data => <Vista datos={data} />} />

// Nivel 5: Props avanzadas con TypeScript
interface Props {
  items: Item[]
  onSelect: (item: Item) => void
  renderItem?: (item: Item) => ReactNode
}
```

---

### Recursos adicionales

1. **Documentación oficial**: [React Props](https://react.dev/learn/passing-props-to-a-component)
2. **PropTypes**: [Documentación](https://github.com/facebook/prop-types)
3. **Patrones avanzados**: [React Patterns](https://reactpatterns.com)

---

## 💡 Conclusión

Las props son el corazón de React. Dominarlas significa:

- 🧩 **Componentes reutilizables**: Escribe una vez, usa muchas
- 🔄 **Flujo de datos claro**: Siempre sabes de dónde vienen los datos
- 🛠️ **Mantenibilidad**: Código predecible y fácil de debuggear
- 🚀 **Escalabilidad**: Arquitectura que crece bien

**Recuerda**: Las props son como los argumentos de una función. Mantenlas simples, claras y enfocadas en un propósito específico.