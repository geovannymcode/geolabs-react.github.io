# 🎣 Introducción a Hooks: useEffect y useContext - Guía para Principiantes

## 📋 Índice
1. [¿Qué son los Hooks?](#que-son-hooks)
2. [useEffect: Efectos secundarios](#useeffect)
3. [useContext: Compartir datos globalmente](#usecontext)
4. [Comparación y cuándo usar cada uno](#comparacion)
5. [Errores comunes y soluciones](#errores-comunes)
6. [Resumen y próximos pasos](#resumen)

---

## 🤔 ¿Qué son los Hooks? <a name="que-son-hooks"></a>

### Definición simple

Los **Hooks** son funciones especiales que permiten "engancharte" a las características de React desde componentes funcionales.

```jsx
// Antes de Hooks (componentes de clase) 😢
class Contador extends React.Component {
  state = { cuenta: 0 }
  
  componentDidMount() {
    console.log("Componente montado")
  }
  
  render() {
    return <div>{this.state.cuenta}</div>
  }
}

// Con Hooks (componentes funcionales) 😊
function Contador() {
  const [cuenta, setCuenta] = useState(0)
  
  useEffect(() => {
    console.log("Componente montado")
  }, [])
  
  return <div>{cuenta}</div>
}
```

### Los Hooks más importantes

1. **useState**: Manejar estado local (ya lo conoces)
2. **useEffect**: Efectos secundarios (lo veremos ahora)
3. **useContext**: Compartir datos entre componentes (lo veremos después)
4. **useReducer**: Estado complejo
5. **useMemo/useCallback**: Optimización

### Reglas de los Hooks

```jsx
// ✅ REGLA 1: Solo llamar Hooks al nivel superior
function Componente() {
  const [estado, setEstado] = useState(0)  // ✅ Bien
  
  if (condicion) {
    const [otro, setOtro] = useState(0)    // ❌ Mal - dentro de if
  }
}

// ✅ REGLA 2: Solo llamar Hooks desde funciones de React
function Componente() {
  useEffect(() => {})  // ✅ Bien - componente React
}

function utilidad() {
  useEffect(() => {})  // ❌ Mal - función normal
}
```

---

## 🎬 useEffect: Efectos Secundarios <a name="useeffect"></a>

### ¿Qué son los efectos secundarios?

**Efectos secundarios** = Cualquier cosa que afecte algo fuera del componente:
- 🌐 Llamadas a APIs
- ⏰ Timers (setTimeout, setInterval)
- 📝 Modificar el DOM directamente
- 🔔 Suscripciones a eventos
- 💾 Local Storage

### Anatomía de useEffect

```jsx
useEffect(() => {
  // 1. Código del efecto
  console.log("Este código se ejecuta")
  
  // 2. Función de limpieza (opcional)
  return () => {
    console.log("Este código limpia")
  }
}, [dependencias]) // 3. Array de dependencias
```

### Los 3 tipos de useEffect

#### 1. **Efecto sin dependencias (se ejecuta siempre)**

```jsx
function Componente() {
  const [contador, setContador] = useState(0)
  
  // ⚠️ Se ejecuta en CADA render
  useEffect(() => {
    console.log("Componente renderizado")
  }) // Sin array de dependencias
  
  return (
    <button onClick={() => setContador(contador + 1)}>
      Clicks: {contador}
    </button>
  )
}
```

**Cuándo usar**: Casi nunca. Puede causar problemas de rendimiento.

#### 2. **Efecto con array vacío (se ejecuta una vez)**

```jsx
function PerfilUsuario() {
  const [usuario, setUsuario] = useState(null)
  const [cargando, setCargando] = useState(true)
  
  // Se ejecuta SOLO cuando el componente se monta
  useEffect(() => {
    console.log("Componente montado - Obteniendo datos...")
    
    fetch('/api/usuario')
      .then(res => res.json())
      .then(data => {
        setUsuario(data)
        setCargando(false)
      })
      
    // Cleanup: se ejecuta cuando el componente se desmonta
    return () => {
      console.log("Componente desmontado")
    }
  }, []) // Array vacío = solo una vez
  
  if (cargando) return <div>Cargando...</div>
  return <div>Hola {usuario.nombre}</div>
}
```

**Cuándo usar**: Para inicializar datos, suscripciones únicas.

#### 3. **Efecto con dependencias (se ejecuta cuando cambian)**

```jsx
function BuscadorProductos() {
  const [busqueda, setBusqueda] = useState('')
  const [resultados, setResultados] = useState([])
  
  // Se ejecuta cuando 'busqueda' cambia
  useEffect(() => {
    // Si no hay búsqueda, limpiar resultados
    if (busqueda === '') {
      setResultados([])
      return
    }
    
    console.log(`Buscando: ${busqueda}`)
    
    // Simular delay de búsqueda
    const timer = setTimeout(() => {
      // Aquí normalmente harías fetch a la API
      const resultadosFiltrados = productos.filter(p =>
        p.nombre.toLowerCase().includes(busqueda.toLowerCase())
      )
      setResultados(resultadosFiltrados)
    }, 300)
    
    // Cleanup: cancelar búsqueda anterior
    return () => {
      console.log("Cancelando búsqueda anterior")
      clearTimeout(timer)
    }
  }, [busqueda]) // Se ejecuta cuando busqueda cambia
  
  return (
    <div>
      <input
        value={busqueda}
        onChange={(e) => setBusqueda(e.target.value)}
        placeholder="Buscar productos..."
      />
      
      {resultados.map(producto => (
        <div key={producto.id}>{producto.nombre}</div>
      ))}
    </div>
  )
}
```

**Cuándo usar**: Cuando necesitas reaccionar a cambios específicos.

### Ejemplo visual del ciclo de vida

```
MONTAJE:
1. Componente se renderiza
2. useEffect se ejecuta
3. Se muestra en pantalla

ACTUALIZACIÓN:
1. Estado/props cambian
2. Componente re-renderiza
3. useEffect se ejecuta SI las dependencias cambiaron

DESMONTAJE:
1. Componente se va a quitar
2. Función de cleanup se ejecuta
3. Componente se elimina
```

### Casos de uso comunes

#### 1. **Obtener datos de una API**

```jsx
function ListaProductos() {
  const [productos, setProductos] = useState([])
  const [cargando, setCargando] = useState(true)
  const [error, setError] = useState(null)
  
  useEffect(() => {
    // Función async dentro de useEffect
    const obtenerProductos = async () => {
      try {
        setCargando(true)
        const respuesta = await fetch('https://fakestoreapi.com/products')
        
        if (!respuesta.ok) {
          throw new Error('Error al cargar productos')
        }
        
        const datos = await respuesta.json()
        setProductos(datos)
      } catch (err) {
        setError(err.message)
      } finally {
        setCargando(false)
      }
    }
    
    obtenerProductos()
  }, []) // Solo al montar
  
  if (cargando) return <div>Cargando productos...</div>
  if (error) return <div>Error: {error}</div>
  
  return (
    <div>
      {productos.map(producto => (
        <div key={producto.id}>
          <h3>{producto.title}</h3>
          <p>${producto.price}</p>
        </div>
      ))}
    </div>
  )
}
```

#### 2. **Actualizar el título de la página**

```jsx
function PaginaProducto({ producto }) {
  useEffect(() => {
    // Cambiar título del navegador
    document.title = `${producto.nombre} - Mi Tienda`
    
    // Restaurar título al desmontar
    return () => {
      document.title = 'Mi Tienda'
    }
  }, [producto.nombre])
  
  return <div>{/* contenido */}</div>
}
```

#### 3. **Suscribirse a eventos**

```jsx
function ScrollDetector() {
  const [scrollY, setScrollY] = useState(0)
  
  useEffect(() => {
    const manejarScroll = () => {
      setScrollY(window.scrollY)
    }
    
    // Suscribirse al evento
    window.addEventListener('scroll', manejarScroll)
    
    // Desuscribirse al desmontar
    return () => {
      window.removeEventListener('scroll', manejarScroll)
    }
  }, [])
  
  return <div>Scroll: {scrollY}px</div>
}
```

#### 4. **Timer con cleanup**

```jsx
function Temporizador() {
  const [segundos, setSegundos] = useState(0)
  
  useEffect(() => {
    const intervalo = setInterval(() => {
      setSegundos(s => s + 1)
    }, 1000)
    
    // MUY IMPORTANTE: limpiar el intervalo
    return () => clearInterval(intervalo)
  }, [])
  
  return <div>Tiempo: {segundos}s</div>
}
```

---

## 🌍 useContext: Compartir Datos Globalmente <a name="usecontext"></a>

### El problema: Prop Drilling

Imagina pasar datos a través de muchos componentes:

```jsx
// ❌ Prop Drilling - datos pasan por componentes que no los usan
function App() {
  const [usuario, setUsuario] = useState({ nombre: "Juan" })
  return <Layout usuario={usuario} />
}

function Layout({ usuario }) {
  return <Header usuario={usuario} />
}

function Header({ usuario }) {
  return <NavBar usuario={usuario} />
}

function NavBar({ usuario }) {
  return <div>Hola {usuario.nombre}</div>
}
```

### La solución: Context

Context permite "teletransportar" datos a cualquier componente:

```jsx
// ✅ Con Context - datos disponibles donde se necesiten
const UsuarioContext = createContext()

function App() {
  const [usuario, setUsuario] = useState({ nombre: "Juan" })
  
  return (
    <UsuarioContext.Provider value={usuario}>
      <Layout />  {/* No necesita props */}
    </UsuarioContext.Provider>
  )
}

function NavBar() {
  const usuario = useContext(UsuarioContext)
  return <div>Hola {usuario.nombre}</div>
}
```

### Los 3 pasos para usar Context

#### Paso 1: Crear el Context

```jsx
import { createContext } from 'react'

// Crear contexto con valor por defecto (opcional)
const TemaContext = createContext('claro')
```

#### Paso 2: Proveer el Context

```jsx
function App() {
  const [tema, setTema] = useState('claro')
  
  const cambiarTema = () => {
    setTema(tema === 'claro' ? 'oscuro' : 'claro')
  }
  
  // Provider envuelve los componentes que necesitan acceso
  return (
    <TemaContext.Provider value={{ tema, cambiarTema }}>
      <div className={`app tema-${tema}`}>
        <Header />
        <Main />
        <Footer />
      </div>
    </TemaContext.Provider>
  )
}
```

#### Paso 3: Consumir el Context

```jsx
function BotonTema() {
  // Obtener valor del contexto
  const { tema, cambiarTema } = useContext(TemaContext)
  
  return (
    <button onClick={cambiarTema}>
      Tema actual: {tema}
    </button>
  )
}
```

### Ejemplo completo: Sistema de autenticación

```jsx
// 1. Crear el contexto de autenticación
const AuthContext = createContext()

// 2. Crear un Provider personalizado
function AuthProvider({ children }) {
  const [usuario, setUsuario] = useState(null)
  const [cargando, setCargando] = useState(true)
  
  // Verificar si hay usuario al cargar
  useEffect(() => {
    const usuarioGuardado = localStorage.getItem('usuario')
    if (usuarioGuardado) {
      setUsuario(JSON.parse(usuarioGuardado))
    }
    setCargando(false)
  }, [])
  
  const iniciarSesion = async (email, password) => {
    try {
      // Simular llamada a API
      const respuesta = await fetch('/api/login', {
        method: 'POST',
        body: JSON.stringify({ email, password })
      })
      
      const datosUsuario = await respuesta.json()
      setUsuario(datosUsuario)
      localStorage.setItem('usuario', JSON.stringify(datosUsuario))
      
      return { exito: true }
    } catch (error) {
      return { exito: false, error: error.message }
    }
  }
  
  const cerrarSesion = () => {
    setUsuario(null)
    localStorage.removeItem('usuario')
  }
  
  const valor = {
    usuario,
    cargando,
    iniciarSesion,
    cerrarSesion,
    estaAutenticado: !!usuario
  }
  
  return (
    <AuthContext.Provider value={valor}>
      {children}
    </AuthContext.Provider>
  )
}

// 3. Hook personalizado para usar el contexto
function useAuth() {
  const contexto = useContext(AuthContext)
  if (!contexto) {
    throw new Error('useAuth debe usarse dentro de AuthProvider')
  }
  return contexto
}

// 4. Usar en la aplicación
function App() {
  return (
    <AuthProvider>
      <Router>
        <Routes>
          <Route path="/login" element={<Login />} />
          <Route path="/perfil" element={<RutaProtegida><Perfil /></RutaProtegida>} />
        </Routes>
      </Router>
    </AuthProvider>
  )
}

// 5. Componente de login
function Login() {
  const { iniciarSesion } = useAuth()
  const [email, setEmail] = useState('')
  const [password, setPassword] = useState('')
  
  const manejarSubmit = async (e) => {
    e.preventDefault()
    const resultado = await iniciarSesion(email, password)
    
    if (resultado.exito) {
      // Redirigir a perfil
    } else {
      alert('Error al iniciar sesión')
    }
  }
  
  return (
    <form onSubmit={manejarSubmit}>
      <input
        type="email"
        value={email}
        onChange={(e) => setEmail(e.target.value)}
      />
      <input
        type="password"
        value={password}
        onChange={(e) => setPassword(e.target.value)}
      />
      <button type="submit">Iniciar Sesión</button>
    </form>
  )
}

// 6. Ruta protegida
function RutaProtegida({ children }) {
  const { estaAutenticado, cargando } = useAuth()
  
  if (cargando) return <div>Verificando...</div>
  
  if (!estaAutenticado) {
    return <Navigate to="/login" />
  }
  
  return children
}
```

### Cuándo usar Context

✅ **USA Context cuando:**
- Datos necesarios en muchos componentes (tema, idioma, usuario)
- Evitar prop drilling excesivo
- Estado global de la aplicación

❌ **NO uses Context cuando:**
- Solo 2-3 componentes necesitan los datos
- Los datos cambian muy frecuentemente
- Es un estado muy local

---

## 🆚 Comparación y Cuándo Usar Cada Uno <a name="comparacion"></a>

### useEffect vs useContext

| Aspecto | useEffect | useContext |
|---------|-----------|------------|
| **Propósito** | Efectos secundarios | Compartir datos |
| **Cuándo se ejecuta** | Después del render | Al leer el valor |
| **Casos de uso** | APIs, timers, eventos | Tema, auth, configuración |
| **Complejidad** | Media | Baja-Media |

### Pueden trabajar juntos

```jsx
function UsuarioProvider({ children }) {
  const [usuario, setUsuario] = useState(null)
  
  // useEffect para cargar datos
  useEffect(() => {
    fetch('/api/usuario/actual')
      .then(res => res.json())
      .then(setUsuario)
  }, [])
  
  // Context para compartir
  return (
    <UsuarioContext.Provider value={usuario}>
      {children}
    </UsuarioContext.Provider>
  )
}
```

---

## ❌ Errores Comunes y Soluciones <a name="errores-comunes"></a>

### Error 1: Olvidar el array de dependencias

```jsx
// ❌ Loop infinito
useEffect(() => {
  setContador(contador + 1)
}) // Sin array = se ejecuta siempre

// ✅ Correcto
useEffect(() => {
  setContador(contador + 1)
}, []) // Con array vacío = una vez
```

### Error 2: Dependencias incorrectas

```jsx
// ❌ No se actualiza cuando cambia usuario
useEffect(() => {
  console.log(usuario.nombre)
}, []) // Falta usuario en dependencias

// ✅ Correcto
useEffect(() => {
  console.log(usuario.nombre)
}, [usuario.nombre])
```

### Error 3: No limpiar efectos

```jsx
// ❌ Memory leak - el timer sigue corriendo
useEffect(() => {
  setInterval(() => {
    setTiempo(t => t + 1)
  }, 1000)
}, [])

// ✅ Correcto - con cleanup
useEffect(() => {
  const timer = setInterval(() => {
    setTiempo(t => t + 1)
  }, 1000)
  
  return () => clearInterval(timer)
}, [])
```

### Error 4: Context sin Provider

```jsx
// ❌ Error: Cannot read context
function App() {
  return <ComponenteQueUsaContext />
}

// ✅ Correcto - con Provider
function App() {
  return (
    <MiProvider>
      <ComponenteQueUsaContext />
    </MiProvider>
  )
}
```

---

## 🎯 Resumen y Próximos Pasos <a name="resumen"></a>

### Lo que aprendiste

1. **useEffect**:
   - Manejar efectos secundarios
   - 3 tipos: sin deps, array vacío, con deps
   - Cleanup para evitar memory leaks
   - Casos: APIs, timers, eventos, DOM

2. **useContext**:
   - Compartir datos sin prop drilling
   - Crear, proveer y consumir contexto
   - Casos: tema, auth, configuración
   - Combinar con useEffect

### Checklist de comprensión

✅ ¿Entiendes cuándo se ejecuta useEffect?
✅ ¿Sabes cuándo necesitas cleanup?
✅ ¿Entiendes la diferencia entre los 3 tipos de useEffect?
✅ ¿Sabes cuándo usar Context vs props?
✅ ¿Puedes crear un Provider personalizado?

### Próximos pasos

1. **Practicar**: Crear ejemplos propios
2. **Combinar**: useEffect + useContext
3. **Explorar**: useReducer para estado complejo
4. **Optimizar**: useMemo y useCallback
5. **Custom Hooks**: Crear tus propios hooks

¡Con estos dos hooks fundamentales, ya puedes crear aplicaciones React mucho más poderosas y organizadas!