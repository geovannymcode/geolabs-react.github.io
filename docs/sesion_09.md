# 🚀 Enrutamiento con React Router DOM

## 📋 Índice
1. [¿Qué es React Router y por qué lo necesitamos?](#que-es-react-router)
2. [Instalación y configuración básica](#instalacion)
3. [Componentes principales de React Router](#componentes-principales)
4. [Navegación entre rutas](#navegacion)
5. [Rutas dinámicas y parámetros](#rutas-dinamicas)
6. [Rutas anidadas](#rutas-anidadas)
7. [Protección de rutas](#proteccion-rutas)
8. [Manejo de errores y 404](#manejo-errores)
9. [Hooks de React Router](#hooks-router)
10. [Mejores prácticas](#mejores-practicas)

---

## 🤔 ¿Qué es React Router y por qué lo necesitamos? <a name="que-es-react-router"></a>

### El problema

React es una **SPA (Single Page Application)** - toda la aplicación vive en una sola página HTML. Pero los usuarios esperan:
- URLs diferentes para diferentes páginas
- Botón atrás/adelante funcional
- Poder compartir enlaces
- Marcar páginas como favoritos

### La solución: React Router

React Router permite crear la **ilusión** de múltiples páginas en tu SPA:

```
Sin React Router:               Con React Router:
misitioweb.com                  misitioweb.com/
(Todo en una página)            misitioweb.com/productos
                                misitioweb.com/productos/123
                                misitioweb.com/contacto
```

### Conceptos clave

```jsx
// Navegación tradicional (recarga toda la página)
<a href="/productos">Productos</a>  // ❌ Recarga completa

// Navegación con React Router (solo cambia componentes)
<Link to="/productos">Productos</Link>  // ✅ Sin recarga
```

---

## 📦 Instalación y Configuración Básica <a name="instalacion"></a>

### Instalación

```bash
npm install react-router-dom
```

### Configuración básica en main.jsx o index.js

```jsx
import React from 'react'
import ReactDOM from 'react-dom/client'
import { BrowserRouter } from 'react-router-dom'
import App from './App'

ReactDOM.createRoot(document.getElementById('root')).render(
  <React.StrictMode>
    <BrowserRouter>
      <App />
    </BrowserRouter>
  </React.StrictMode>
)
```

### Primera aplicación con rutas

```jsx
// App.jsx
import { Routes, Route } from 'react-router-dom'
import Home from './pages/Home'
import About from './pages/About'
import Contact from './pages/Contact'

function App() {
  return (
    <div>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
        <Route path="/contact" element={<Contact />} />
      </Routes>
    </div>
  )
}
```

### Estructura de carpetas recomendada

```
src/
├── components/
│   ├── Layout.jsx
│   ├── Navbar.jsx
│   └── Footer.jsx
├── pages/
│   ├── Home.jsx
│   ├── About.jsx
│   ├── Products.jsx
│   └── ProductDetail.jsx
├── App.jsx
└── main.jsx
```

---

## 🧩 Componentes Principales de React Router <a name="componentes-principales"></a>

### 1. BrowserRouter

Envuelve toda tu aplicación y habilita el enrutamiento:

```jsx
<BrowserRouter>
  <App />
</BrowserRouter>
```

**Alternativas:**
- `HashRouter`: Usa # en las URLs (ejemplo.com/#/productos)
- `MemoryRouter`: Para testing o apps sin URL

### 2. Routes y Route

Define qué componente mostrar para cada ruta:

```jsx
<Routes>
  <Route path="/" element={<Home />} />
  <Route path="/productos" element={<Productos />} />
  <Route path="/productos/:id" element={<ProductoDetalle />} />
  <Route path="*" element={<NotFound />} />
</Routes>
```

### 3. Link y NavLink

Para navegación sin recargar la página:

```jsx
import { Link, NavLink } from 'react-router-dom'

function Navbar() {
  return (
    <nav>
      {/* Link básico */}
      <Link to="/">Inicio</Link>
      
      {/* NavLink con clase activa */}
      <NavLink 
        to="/productos"
        className={({ isActive }) => isActive ? 'active' : ''}
      >
        Productos
      </NavLink>
      
      {/* NavLink con estilos inline */}
      <NavLink
        to="/contacto"
        style={({ isActive }) => ({
          color: isActive ? '#3498db' : '#333',
          fontWeight: isActive ? 'bold' : 'normal'
        })}
      >
        Contacto
      </NavLink>
    </nav>
  )
}
```

### 4. Outlet (para layouts)

Permite crear layouts que envuelven múltiples páginas:

```jsx
// Layout.jsx
import { Outlet, Link } from 'react-router-dom'

function Layout() {
  return (
    <div>
      <header>
        <nav>
          <Link to="/">Inicio</Link>
          <Link to="/productos">Productos</Link>
          <Link to="/contacto">Contacto</Link>
        </nav>
      </header>
      
      <main>
        <Outlet /> {/* Aquí se renderiza la ruta actual */}
      </main>
      
      <footer>
        <p>© 2024 Mi Tienda</p>
      </footer>
    </div>
  )
}

// App.jsx
function App() {
  return (
    <Routes>
      <Route path="/" element={<Layout />}>
        <Route index element={<Home />} />
        <Route path="productos" element={<Productos />} />
        <Route path="contacto" element={<Contacto />} />
      </Route>
    </Routes>
  )
}
```

---

## 🧭 Navegación entre Rutas <a name="navegacion"></a>

### 1. Navegación declarativa con Link

```jsx
// Navegación simple
<Link to="/productos">Ver productos</Link>

// Con estado
<Link 
  to="/checkout"
  state={{ from: 'carrito' }}
>
  Proceder al pago
</Link>

// Link con query params
<Link to="/buscar?q=zapatos&categoria=deportes">
  Buscar zapatos deportivos
</Link>

// Link relativo
<Link to="editar">Editar</Link>  // Si estás en /productos/123 → /productos/123/editar

// Link absoluto
<Link to="/productos/nuevo">Nuevo producto</Link>
```

### 2. Navegación programática con useNavigate

```jsx
import { useNavigate } from 'react-router-dom'

function LoginForm() {
  const navigate = useNavigate()
  
  const handleSubmit = async (e) => {
    e.preventDefault()
    
    try {
      await loginAPI()
      
      // Navegar después del login exitoso
      navigate('/dashboard')
      
      // Navegar con reemplazo (no agrega al historial)
      navigate('/dashboard', { replace: true })
      
      // Navegar con estado
      navigate('/perfil', { 
        state: { mensaje: 'Login exitoso' }
      })
      
    } catch (error) {
      // Manejar error
    }
  }
  
  return (
    <form onSubmit={handleSubmit}>
      {/* Campos del formulario */}
      
      {/* Navegación en el historial */}
      <button type="button" onClick={() => navigate(-1)}>
        Volver
      </button>
    </form>
  )
}
```

### 3. Navigate (componente)

Para redirecciones condicionales:

```jsx
import { Navigate } from 'react-router-dom'

function PrivatePage() {
  const isAuthenticated = useAuth()
  
  if (!isAuthenticated) {
    return <Navigate to="/login" replace />
  }
  
  return <div>Contenido privado</div>
}
```

---

## 🔧 Rutas Dinámicas y Parámetros <a name="rutas-dinamicas"></a>

### 1. Parámetros de ruta (useParams)

```jsx
// Definir ruta con parámetro
<Route path="/productos/:id" element={<ProductoDetalle />} />
<Route path="/usuarios/:userId/posts/:postId" element={<Post />} />

// Componente que usa los parámetros
import { useParams } from 'react-router-dom'

function ProductoDetalle() {
  const { id } = useParams()
  const [producto, setProducto] = useState(null)
  
  useEffect(() => {
    // Cargar producto usando el ID
    fetch(`/api/productos/${id}`)
      .then(res => res.json())
      .then(setProducto)
  }, [id])
  
  if (!producto) return <div>Cargando...</div>
  
  return (
    <div>
      <h1>{producto.nombre}</h1>
      <p>ID del producto: {id}</p>
    </div>
  )
}
```

### 2. Query Parameters (useSearchParams)

```jsx
// URL: /buscar?q=laptop&categoria=tecnologia&precio=1000-2000
import { useSearchParams } from 'react-router-dom'

function BuscarProductos() {
  const [searchParams, setSearchParams] = useSearchParams()
  
  // Leer parámetros
  const query = searchParams.get('q') || ''
  const categoria = searchParams.get('categoria') || 'todas'
  const precio = searchParams.get('precio') || ''
  
  // Actualizar parámetros
  const handleFiltroCategoria = (nuevaCategoria) => {
    setSearchParams(prev => {
      prev.set('categoria', nuevaCategoria)
      return prev
    })
  }
  
  // Eliminar parámetro
  const limpiarFiltros = () => {
    setSearchParams({})
  }
  
  return (
    <div>
      <h1>Resultados para: {query}</h1>
      
      <div className="filtros">
        <select 
          value={categoria}
          onChange={(e) => handleFiltroCategoria(e.target.value)}
        >
          <option value="todas">Todas las categorías</option>
          <option value="tecnologia">Tecnología</option>
          <option value="ropa">Ropa</option>
        </select>
        
        <button onClick={limpiarFiltros}>
          Limpiar filtros
        </button>
      </div>
      
      {/* Mostrar productos filtrados */}
    </div>
  )
}
```

### 3. Rutas opcionales y comodines

```jsx
// Parámetro opcional
<Route path="/blog/:year?/:month?" element={<Blog />} />

// Múltiples segmentos (catch-all)
<Route path="/docs/*" element={<Documentation />} />

// En el componente Documentation
function Documentation() {
  const location = useLocation()
  // location.pathname podría ser /docs/react/hooks/useState
  
  const segments = location.pathname.split('/').slice(2)
  // ['react', 'hooks', 'useState']
}
```

---

## 🏗️ Rutas Anidadas <a name="rutas-anidadas"></a>

### Configuración de rutas anidadas

```jsx
// App.jsx
function App() {
  return (
    <Routes>
      <Route path="/" element={<Layout />}>
        <Route index element={<Home />} />
        
        {/* Rutas anidadas de productos */}
        <Route path="productos" element={<ProductosLayout />}>
          <Route index element={<ProductosLista />} />
          <Route path="nuevo" element={<ProductoNuevo />} />
          <Route path=":id" element={<ProductoDetalle />} />
          <Route path=":id/editar" element={<ProductoEditar />} />
        </Route>
        
        {/* Rutas anidadas de usuario */}
        <Route path="cuenta" element={<CuentaLayout />}>
          <Route index element={<Perfil />} />
          <Route path="pedidos" element={<Pedidos />} />
          <Route path="configuracion" element={<Configuracion />} />
        </Route>
      </Route>
      
      {/* Ruta fuera del layout principal */}
      <Route path="/login" element={<Login />} />
      <Route path="*" element={<NotFound />} />
    </Routes>
  )
}
```

### Layout para sección de productos

```jsx
// ProductosLayout.jsx
import { Outlet, NavLink } from 'react-router-dom'

function ProductosLayout() {
  return (
    <div className="productos-container">
      <aside className="productos-sidebar">
        <h2>Productos</h2>
        <nav>
          <NavLink to="/productos" end>
            Todos los productos
          </NavLink>
          <NavLink to="/productos/nuevo">
            Agregar producto
          </NavLink>
        </nav>
      </aside>
      
      <div className="productos-content">
        <Outlet /> {/* Aquí se renderizan las sub-rutas */}
      </div>
    </div>
  )
}
```

### Breadcrumbs dinámicos

```jsx
import { useLocation, Link } from 'react-router-dom'

function Breadcrumbs() {
  const location = useLocation()
  
  const pathnames = location.pathname.split('/').filter(x => x)
  
  return (
    <nav aria-label="breadcrumb">
      <ol className="breadcrumb">
        <li>
          <Link to="/">Inicio</Link>
        </li>
        
        {pathnames.map((name, index) => {
          const routeTo = `/${pathnames.slice(0, index + 1).join('/')}`
          const isLast = index === pathnames.length - 1
          
          return (
            <li key={name}>
              {isLast ? (
                <span>{name}</span>
              ) : (
                <Link to={routeTo}>{name}</Link>
              )}
            </li>
          )
        })}
      </ol>
    </nav>
  )
}
```

---

## 🔒 Protección de Rutas <a name="proteccion-rutas"></a>

### 1. Componente de ruta protegida

```jsx
import { Navigate, useLocation } from 'react-router-dom'
import { useAuth } from '../hooks/useAuth'

function RutaProtegida({ children }) {
  const { usuario, cargando } = useAuth()
  const location = useLocation()
  
  if (cargando) {
    return <div>Verificando autenticación...</div>
  }
  
  if (!usuario) {
    // Redirigir al login guardando la ubicación actual
    return <Navigate to="/login" state={{ from: location }} replace />
  }
  
  return children
}

// Uso
<Route 
  path="/dashboard" 
  element={
    <RutaProtegida>
      <Dashboard />
    </RutaProtegida>
  } 
/>
```

### 2. Rutas con roles

```jsx
function RutaConRol({ children, rolesPermitidos }) {
  const { usuario } = useAuth()
  
  if (!rolesPermitidos.includes(usuario?.rol)) {
    return <Navigate to="/sin-permisos" replace />
  }
  
  return children
}

// Uso
<Route 
  path="/admin" 
  element={
    <RutaConRol rolesPermitidos={['admin', 'moderador']}>
      <PanelAdmin />
    </RutaConRol>
  } 
/>
```

### 3. Redirección después del login

```jsx
function Login() {
  const navigate = useNavigate()
  const location = useLocation()
  const { login } = useAuth()
  
  const from = location.state?.from?.pathname || '/'
  
  const handleSubmit = async (e) => {
    e.preventDefault()
    
    try {
      await login(email, password)
      // Volver a donde estaba el usuario
      navigate(from, { replace: true })
    } catch (error) {
      // Manejar error
    }
  }
}
```

---

## ❌ Manejo de Errores y 404 <a name="manejo-errores"></a>

### 1. Página 404

```jsx
// NotFound.jsx
import { Link, useNavigate } from 'react-router-dom'

function NotFound() {
  const navigate = useNavigate()
  
  return (
    <div className="not-found">
      <h1>404</h1>
      <h2>Página no encontrada</h2>
      <p>La página que buscas no existe.</p>
      
      <div className="actions">
        <Link to="/">Ir al inicio</Link>
        <button onClick={() => navigate(-1)}>
          Volver atrás
        </button>
      </div>
    </div>
  )
}

// En App.jsx
<Route path="*" element={<NotFound />} />
```

### 2. Error Boundaries para rutas

```jsx
import { useRouteError } from 'react-router-dom'

function ErrorPage() {
  const error = useRouteError()
  
  return (
    <div className="error-page">
      <h1>¡Oops!</h1>
      <p>Ha ocurrido un error inesperado.</p>
      <p>
        <i>{error.statusText || error.message}</i>
      </p>
    </div>
  )
}

// Configurar en la ruta
<Route 
  path="/productos/:id" 
  element={<ProductoDetalle />}
  errorElement={<ErrorPage />}
/>
```

### 3. Manejo de rutas no autorizadas

```jsx
function SinPermisos() {
  const navigate = useNavigate()
  const { usuario } = useAuth()
  
  return (
    <div className="sin-permisos">
      <h1>Acceso Denegado</h1>
      <p>No tienes permisos para ver esta página.</p>
      
      {usuario ? (
        <p>Tu rol actual es: {usuario.rol}</p>
      ) : (
        <Link to="/login">Iniciar sesión</Link>
      )}
      
      <button onClick={() => navigate('/')}>
        Volver al inicio
      </button>
    </div>
  )
}
```

---

## 🪝 Hooks de React Router <a name="hooks-router"></a>

### 1. useLocation

Obtiene información sobre la ubicación actual:

```jsx
import { useLocation } from 'react-router-dom'

function Analytics() {
  const location = useLocation()
  
  useEffect(() => {
    // Enviar evento de página vista
    analytics.pageview(location.pathname)
  }, [location])
  
  return null
}

// Propiedades de location
{
  pathname: "/productos/123",
  search: "?categoria=electronica",
  hash: "#reviews",
  state: { from: "/carrito" }
}
```

### 2. useNavigate

Navegación programática:

```jsx
const navigate = useNavigate()

// Navegar a ruta
navigate('/productos')

// Navegar con estado
navigate('/checkout', { state: { items: carrito } })

// Navegar en el historial
navigate(-1)  // Atrás
navigate(1)   // Adelante

// Reemplazar en lugar de agregar al historial
navigate('/login', { replace: true })
```

### 3. useParams

Acceder a parámetros de ruta:

```jsx
// Ruta: /usuarios/:id/posts/:postId
const { id, postId } = useParams()
```

### 4. useSearchParams

Manejar query parameters:

```jsx
const [searchParams, setSearchParams] = useSearchParams()

// Leer
const page = searchParams.get('page') || 1

// Escribir
setSearchParams({ page: 2, sort: 'name' })
```

### 5. useMatch

Verificar si una ruta coincide:

```jsx
function NavItem({ to, children }) {
  const match = useMatch(to)
  
  return (
    <li className={match ? 'active' : ''}>
      <Link to={to}>{children}</Link>
    </li>
  )
}
```

### 6. useRoutes

Configuración de rutas con hooks:

```jsx
function App() {
  const element = useRoutes([
    {
      path: '/',
      element: <Layout />,
      children: [
        { index: true, element: <Home /> },
        { path: 'productos', element: <Productos /> },
        { path: 'productos/:id', element: <ProductoDetalle /> }
      ]
    },
    { path: '/login', element: <Login /> },
    { path: '*', element: <NotFound /> }
  ])
  
  return element
}
```

---

## 📚 Mejores Prácticas <a name="mejores-practicas"></a>

### 1. Organización de rutas

```jsx
// routes/index.jsx
export const routes = {
  home: '/',
  productos: {
    lista: '/productos',
    detalle: (id) => `/productos/${id}`,
    nuevo: '/productos/nuevo',
    editar: (id) => `/productos/${id}/editar`
  },
  auth: {
    login: '/login',
    registro: '/registro',
    recuperar: '/recuperar-password'
  }
}

// Uso
import { routes } from './routes'

<Link to={routes.productos.detalle(123)}>Ver producto</Link>
```

### 2. Lazy loading de rutas

```jsx
import { lazy, Suspense } from 'react'

// Cargar componentes de forma perezosa
const Dashboard = lazy(() => import('./pages/Dashboard'))
const Productos = lazy(() => import('./pages/Productos'))
const Configuracion = lazy(() => import('./pages/Configuracion'))

function App() {
  return (
    <Routes>
      <Route path="/" element={<Layout />}>
        <Route index element={<Home />} />
        
        <Route 
          path="dashboard" 
          element={
            <Suspense fallback={<Loading />}>
              <Dashboard />
            </Suspense>
          } 
        />
        
        <Route 
          path="productos/*" 
          element={
            <Suspense fallback={<Loading />}>
              <Productos />
            </Suspense>
          } 
        />
      </Route>
    </Routes>
  )
}
```

### 3. Scroll restoration

```jsx
import { useEffect } from 'react'
import { useLocation } from 'react-router-dom'

function ScrollToTop() {
  const { pathname } = useLocation()
  
  useEffect(() => {
    window.scrollTo(0, 0)
  }, [pathname])
  
  return null
}

// En App.jsx
function App() {
  return (
    <>
      <ScrollToTop />
      <Routes>
        {/* Tus rutas */}
      </Routes>
    </>
  )
}
```

### 4. Manejo de transiciones

```jsx
import { useLocation, useOutlet } from 'react-router-dom'
import { CSSTransition, SwitchTransition } from 'react-transition-group'

function AnimatedOutlet() {
  const location = useLocation()
  const currentOutlet = useOutlet()
  
  return (
    <SwitchTransition>
      <CSSTransition
        key={location.pathname}
        timeout={300}
        classNames="page"
        unmountOnExit
      >
        {currentOutlet || <div />}
      </CSSTransition>
    </SwitchTransition>
  )
}

// CSS
.page-enter {
  opacity: 0;
  transform: translateX(100%);
}

.page-enter-active {
  opacity: 1;
  transform: translateX(0);
  transition: all 300ms;
}

.page-exit {
  opacity: 1;
}

.page-exit-active {
  opacity: 0;
  transition: opacity 300ms;
}
```

### 5. SEO y meta tags

```jsx
import { Helmet } from 'react-helmet-async'

function ProductoDetalle() {
  const { id } = useParams()
  const [producto, setProducto] = useState(null)
  
  return (
    <>
      <Helmet>
        <title>{producto?.nombre || 'Cargando...'} - Mi Tienda</title>
        <meta name="description" content={producto?.descripcion} />
        <meta property="og:title" content={producto?.nombre} />
        <meta property="og:image" content={producto?.imagen} />
      </Helmet>
      
      <div>
        {/* Contenido del producto */}
      </div>
    </>
  )
}
```

---

## 🎯 Resumen

### Conceptos clave aprendidos:

1. **Configuración**: BrowserRouter envuelve la app
2. **Definición de rutas**: Routes y Route
3. **Navegación**: Link, NavLink, useNavigate
4. **Parámetros**: useParams, useSearchParams
5. **Layouts**: Outlet para rutas anidadas
6. **Protección**: Componentes wrapper para auth
7. **Hooks útiles**: useLocation, useMatch
8. **Optimización**: Lazy loading y code splitting

### Checklist de implementación:

✅ Instalar react-router-dom
✅ Envolver app con BrowserRouter
✅ Definir rutas principales
✅ Crear navegación con Link/NavLink
✅ Implementar rutas dinámicas
✅ Agregar página 404
✅ Proteger rutas privadas
✅ Optimizar con lazy loading

¡Con React Router DOM puedes crear aplicaciones con navegación profesional y experiencia de usuario fluida!