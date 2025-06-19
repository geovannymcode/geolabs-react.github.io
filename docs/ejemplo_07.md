# 🛍️ Ejercicio Práctico: Sistema de Productos con useEffect y useContext

## 🎯 Objetivo del ejercicio

Construir un sistema de gestión de productos que:
- Use **useEffect** para cargar datos de una API
- Use **useContext** para compartir el carrito de compras
- Implemente búsqueda en tiempo real con debounce
- Maneje estados de carga y error correctamente

---

## 📁 Estructura del proyecto

```
tienda-hooks/
├── src/
│   ├── App.jsx
│   ├── contexts/
│   │   └── CarritoContext.jsx
│   ├── components/
│   │   ├── ListaProductos.jsx
│   │   ├── ProductoCard.jsx
│   │   ├── Carrito.jsx
│   │   ├── Navbar.jsx
│   │   └── Buscador.jsx
│   └── hooks/
│       └── useProductos.js
```

---

## 🔧 Implementación paso a paso

### 1. Contexto del Carrito (CarritoContext.jsx)

```jsx
import { createContext, useContext, useState, useEffect } from 'react'

// Crear el contexto
const CarritoContext = createContext()

// Provider del carrito
export function CarritoProvider({ children }) {
  const [items, setItems] = useState([])
  const [abierto, setAbierto] = useState(false)
  
  // Cargar carrito desde localStorage al iniciar
  useEffect(() => {
    const carritoGuardado = localStorage.getItem('carrito')
    if (carritoGuardado) {
      try {
        setItems(JSON.parse(carritoGuardado))
      } catch (error) {
        console.error('Error al cargar carrito:', error)
      }
    }
  }, [])
  
  // Guardar carrito en localStorage cuando cambie
  useEffect(() => {
    localStorage.setItem('carrito', JSON.stringify(items))
  }, [items])
  
  // Agregar producto al carrito
  const agregarAlCarrito = (producto) => {
    setItems(prevItems => {
      const itemExistente = prevItems.find(item => item.id === producto.id)
      
      if (itemExistente) {
        // Si existe, aumentar cantidad
        return prevItems.map(item =>
          item.id === producto.id
            ? { ...item, cantidad: item.cantidad + 1 }
            : item
        )
      }
      
      // Si no existe, agregar con cantidad 1
      return [...prevItems, { ...producto, cantidad: 1 }]
    })
    
    // Mostrar notificación (opcional)
    mostrarNotificacion('Producto agregado al carrito')
  }
  
  // Quitar producto del carrito
  const quitarDelCarrito = (id) => {
    setItems(prevItems => prevItems.filter(item => item.id !== id))
  }
  
  // Actualizar cantidad
  const actualizarCantidad = (id, nuevaCantidad) => {
    if (nuevaCantidad <= 0) {
      quitarDelCarrito(id)
      return
    }
    
    setItems(prevItems =>
      prevItems.map(item =>
        item.id === id
          ? { ...item, cantidad: nuevaCantidad }
          : item
      )
    )
  }
  
  // Vaciar carrito
  const vaciarCarrito = () => {
    if (window.confirm('¿Estás seguro de vaciar el carrito?')) {
      setItems([])
    }
  }
  
  // Calcular totales
  const calcularTotales = () => {
    const subtotal = items.reduce((sum, item) => 
      sum + (item.price * item.cantidad), 0
    )
    const impuestos = subtotal * 0.16 // 16% de impuestos
    const total = subtotal + impuestos
    
    return {
      subtotal: subtotal.toFixed(2),
      impuestos: impuestos.toFixed(2),
      total: total.toFixed(2),
      cantidadItems: items.reduce((sum, item) => sum + item.cantidad, 0)
    }
  }
  
  // Mostrar notificación temporal
  const mostrarNotificacion = (mensaje) => {
    // Aquí podrías usar una librería de notificaciones
    console.log(mensaje)
  }
  
  const valor = {
    items,
    abierto,
    setAbierto,
    agregarAlCarrito,
    quitarDelCarrito,
    actualizarCantidad,
    vaciarCarrito,
    totales: calcularTotales()
  }
  
  return (
    <CarritoContext.Provider value={valor}>
      {children}
    </CarritoContext.Provider>
  )
}

// Hook personalizado para usar el carrito
export function useCarrito() {
  const context = useContext(CarritoContext)
  if (!context) {
    throw new Error('useCarrito debe usarse dentro de CarritoProvider')
  }
  return context
}
```

### 2. Hook personalizado para productos (useProductos.js)

```jsx
import { useState, useEffect } from 'react'

export function useProductos(busqueda = '') {
  const [productos, setProductos] = useState([])
  const [cargando, setCargando] = useState(true)
  const [error, setError] = useState(null)
  
  // Cargar productos
  useEffect(() => {
    let cancelado = false
    
    const cargarProductos = async () => {
      try {
        setCargando(true)
        setError(null)
        
        // API real: https://fakestoreapi.com/products
        const respuesta = await fetch('https://fakestoreapi.com/products')
        
        if (!respuesta.ok) {
          throw new Error(`Error HTTP: ${respuesta.status}`)
        }
        
        const datos = await respuesta.json()
        
        // Solo actualizar si el componente sigue montado
        if (!cancelado) {
          setProductos(datos)
        }
      } catch (err) {
        if (!cancelado) {
          setError(err.message)
        }
      } finally {
        if (!cancelado) {
          setCargando(false)
        }
      }
    }
    
    cargarProductos()
    
    // Cleanup: cancelar si el componente se desmonta
    return () => {
      cancelado = true
    }
  }, []) // Solo cargar una vez
  
  // Filtrar productos según búsqueda
  const productosFiltrados = productos.filter(producto =>
    producto.title.toLowerCase().includes(busqueda.toLowerCase()) ||
    producto.description.toLowerCase().includes(busqueda.toLowerCase())
  )
  
  return {
    productos: productosFiltrados,
    cargando,
    error,
    total: productos.length
  }
}
```

### 3. Componente Buscador con debounce (Buscador.jsx)

```jsx
import { useState, useEffect } from 'react'

function Buscador({ onBuscar }) {
  const [termino, setTermino] = useState('')
  
  // Implementar debounce con useEffect
  useEffect(() => {
    // Si no hay término, ejecutar inmediatamente
    if (termino === '') {
      onBuscar('')
      return
    }
    
    // Configurar timer para debounce
    const timer = setTimeout(() => {
      console.log('Buscando:', termino)
      onBuscar(termino)
    }, 500) // Esperar 500ms después de dejar de escribir
    
    // Cleanup: cancelar timer anterior
    return () => {
      clearTimeout(timer)
    }
  }, [termino, onBuscar])
  
  return (
    <div className="buscador">
      <input
        type="text"
        value={termino}
        onChange={(e) => setTermino(e.target.value)}
        placeholder="Buscar productos..."
        className="buscador-input"
      />
      {termino && (
        <button 
          onClick={() => setTermino('')}
          className="buscador-limpiar"
        >
          ✕
        </button>
      )}
    </div>
  )
}

export default Buscador
```

### 4. Lista de Productos (ListaProductos.jsx)

```jsx
import { useState } from 'react'
import { useProductos } from '../hooks/useProductos'
import ProductoCard from './ProductoCard'
import Buscador from './Buscador'

function ListaProductos() {
  const [busqueda, setBusqueda] = useState('')
  const { productos, cargando, error } = useProductos(busqueda)
  
  // Manejar estados de carga y error
  if (cargando) {
    return (
      <div className="cargando">
        <div className="spinner"></div>
        <p>Cargando productos...</p>
      </div>
    )
  }
  
  if (error) {
    return (
      <div className="error">
        <p>😕 Error al cargar productos</p>
        <p>{error}</p>
        <button onClick={() => window.location.reload()}>
          Reintentar
        </button>
      </div>
    )
  }
  
  return (
    <div className="lista-productos">
      <div className="lista-productos-header">
        <h2>Nuestros Productos</h2>
        <Buscador onBuscar={setBusqueda} />
      </div>
      
      {busqueda && (
        <p className="resultados-busqueda">
          Mostrando {productos.length} resultados para "{busqueda}"
        </p>
      )}
      
      {productos.length === 0 ? (
        <div className="sin-resultados">
          <p>No se encontraron productos</p>
          {busqueda && (
            <button onClick={() => setBusqueda('')}>
              Ver todos los productos
            </button>
          )}
        </div>
      ) : (
        <div className="productos-grid">
          {productos.map(producto => (
            <ProductoCard key={producto.id} producto={producto} />
          ))}
        </div>
      )}
    </div>
  )
}

export default ListaProductos

### 5. Tarjeta de Producto (ProductoCard.jsx)

```jsx
import { useState } from 'react'
import { useCarrito } from '../contexts/CarritoContext'

function ProductoCard({ producto }) {
  const { agregarAlCarrito } = useCarrito()
  const [agregando, setAgregando] = useState(false)
  const [imagenError, setImagenError] = useState(false)
  
  const manejarAgregar = async () => {
    setAgregando(true)
    
    // Simular delay para mostrar estado de carga
    await new Promise(resolve => setTimeout(resolve, 500))
    
    agregarAlCarrito(producto)
    setAgregando(false)
    
    // Feedback visual temporal
    const boton = document.getElementById(`btn-${producto.id}`)
    boton.classList.add('agregado')
    setTimeout(() => {
      boton.classList.remove('agregado')
    }, 1000)
  }
  
  return (
    <div className="producto-card">
      <div className="producto-imagen">
        <img 
          src={imagenError ? '/placeholder.jpg' : producto.image} 
          alt={producto.title}
          onError={() => setImagenError(true)}
          loading="lazy"
        />
      </div>
      
      <div className="producto-info">
        <h3 className="producto-titulo">{producto.title}</h3>
        <p className="producto-categoria">{producto.category}</p>
        <p className="producto-precio">${producto.price.toFixed(2)}</p>
        
        <button
          id={`btn-${producto.id}`}
          className={`btn-agregar ${agregando ? 'cargando' : ''}`}
          onClick={manejarAgregar}
          disabled={agregando}
        >
          {agregando ? (
            <>
              <span className="spinner-mini"></span>
              Agregando...
            </>
          ) : (
            <>
              <span>🛒</span>
              Agregar al carrito
            </>
          )}
        </button>
      </div>
    </div>
  )
}

export default ProductoCard
```

### 6. Componente Carrito (Carrito.jsx)

```jsx
import { useCarrito } from '../contexts/CarritoContext'

function Carrito() {
  const {
    items,
    abierto,
    setAbierto,
    quitarDelCarrito,
    actualizarCantidad,
    vaciarCarrito,
    totales
  } = useCarrito()
  
  if (!abierto) return null
  
  return (
    <>
      {/* Overlay */}
      <div 
        className="carrito-overlay"
        onClick={() => setAbierto(false)}
      />
      
      {/* Panel del carrito */}
      <div className="carrito-panel">
        <div className="carrito-header">
          <h2>Tu Carrito</h2>
          <button 
            className="btn-cerrar"
            onClick={() => setAbierto(false)}
          >
            ✕
          </button>
        </div>
        
        {items.length === 0 ? (
          <div className="carrito-vacio">
            <p>Tu carrito está vacío</p>
            <button onClick={() => setAbierto(false)}>
              Continuar comprando
            </button>
          </div>
        ) : (
          <>
            <div className="carrito-items">
              {items.map(item => (
                <div key={item.id} className="carrito-item">
                  <img src={item.image} alt={item.title} />
                  
                  <div className="item-info">
                    <h4>{item.title}</h4>
                    <p>${item.price.toFixed(2)}</p>
                  </div>
                  
                  <div className="item-controles">
                    <button
                      onClick={() => actualizarCantidad(item.id, item.cantidad - 1)}
                    >
                      -
                    </button>
                    <span>{item.cantidad}</span>
                    <button
                      onClick={() => actualizarCantidad(item.id, item.cantidad + 1)}
                    >
                      +
                    </button>
                  </div>
                  
                  <button
                    className="btn-eliminar"
                    onClick={() => quitarDelCarrito(item.id)}
                  >
                    🗑️
                  </button>
                </div>
              ))}
            </div>
            
            <div className="carrito-resumen">
              <div className="resumen-linea">
                <span>Subtotal:</span>
                <span>${totales.subtotal}</span>
              </div>
              <div className="resumen-linea">
                <span>Impuestos:</span>
                <span>${totales.impuestos}</span>
              </div>
              <div className="resumen-linea total">
                <span>Total:</span>
                <span>${totales.total}</span>
              </div>
            </div>
            
            <div className="carrito-acciones">
              <button 
                className="btn-vaciar"
                onClick={vaciarCarrito}
              >
                Vaciar carrito
              </button>
              <button className="btn-checkout">
                Proceder al pago
              </button>
            </div>
          </>
        )}
      </div>
    </>
  )
}

export default Carrito
```

### 7. Navbar con contador (Navbar.jsx)

```jsx
import { useCarrito } from '../contexts/CarritoContext'

function Navbar() {
  const { setAbierto, totales } = useCarrito()
  
  return (
    <nav className="navbar">
      <div className="navbar-container">
        <h1 className="logo">🛍️ Mi Tienda</h1>
        
        <button 
          className="btn-carrito"
          onClick={() => setAbierto(true)}
        >
          <span>🛒</span>
          {totales.cantidadItems > 0 && (
            <span className="carrito-badge">
              {totales.cantidadItems}
            </span>
          )}
        </button>
      </div>
    </nav>
  )
}

export default Navbar
```

### 8. App principal (App.jsx)

```jsx
import { CarritoProvider } from './contexts/CarritoContext'
import Navbar from './components/Navbar'
import ListaProductos from './components/ListaProductos'
import Carrito from './components/Carrito'
import './App.css'

function App() {
  return (
    <CarritoProvider>
      <div className="app">
        <Navbar />
        <main className="contenido-principal">
          <ListaProductos />
        </main>
        <Carrito />
      </div>
    </CarritoProvider>
  )
}

export default App
```

### 9. Estilos CSS (App.css)

```css
/* Reset básico */
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
  background-color: #f5f5f5;
}

/* Navbar */
.navbar {
  background-color: white;
  box-shadow: 0 2px 4px rgba(0,0,0,0.1);
  position: sticky;
  top: 0;
  z-index: 100;
}

.navbar-container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 1rem;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.logo {
  font-size: 1.5rem;
  color: #333;
}

.btn-carrito {
  background: none;
  border: none;
  font-size: 1.5rem;
  cursor: pointer;
  position: relative;
  padding: 0.5rem;
}

.carrito-badge {
  position: absolute;
  top: 0;
  right: 0;
  background-color: #ff4757;
  color: white;
  border-radius: 50%;
  width: 20px;
  height: 20px;
  font-size: 0.75rem;
  display: flex;
  align-items: center;
  justify-content: center;
}

/* Contenido principal */
.contenido-principal {
  max-width: 1200px;
  margin: 0 auto;
  padding: 2rem 1rem;
}

/* Lista de productos */
.lista-productos-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 2rem;
}

/* Buscador */
.buscador {
  position: relative;
  width: 300px;
}

.buscador-input {
  width: 100%;
  padding: 0.5rem 2rem 0.5rem 1rem;
  border: 1px solid #ddd;
  border-radius: 4px;
  font-size: 1rem;
}

.buscador-limpiar {
  position: absolute;
  right: 0.5rem;
  top: 50%;
  transform: translateY(-50%);
  background: none;
  border: none;
  cursor: pointer;
  color: #999;
}

/* Grid de productos */
.productos-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
  gap: 2rem;
}

/* Tarjeta de producto */
.producto-card {
  background: white;
  border-radius: 8px;
  overflow: hidden;
  box-shadow: 0 2px 8px rgba(0,0,0,0.1);
  transition: transform 0.2s;
}

.producto-card:hover {
  transform: translateY(-4px);
  box-shadow: 0 4px 12px rgba(0,0,0,0.15);
}

.producto-imagen {
  width: 100%;
  height: 200px;
  overflow: hidden;
}

.producto-imagen img {
  width: 100%;
  height: 100%;
  object-fit: contain;
  padding: 1rem;
}

.producto-info {
  padding: 1rem;
}

.producto-titulo {
  font-size: 1rem;
  margin-bottom: 0.5rem;
  height: 2.5rem;
  overflow: hidden;
  text-overflow: ellipsis;
  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
}

.producto-categoria {
  color: #666;
  font-size: 0.875rem;
  margin-bottom: 0.5rem;
}

.producto-precio {
  font-size: 1.25rem;
  font-weight: bold;
  color: #2ecc71;
  margin-bottom: 1rem;
}

.btn-agregar {
  width: 100%;
  padding: 0.75rem;
  background-color: #3498db;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 1rem;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 0.5rem;
  transition: background-color 0.2s;
}

.btn-agregar:hover {
  background-color: #2980b9;
}

.btn-agregar.cargando {
  background-color: #95a5a6;
  cursor: not-allowed;
}

.btn-agregar.agregado {
  background-color: #27ae60;
}

/* Spinner */
.spinner-mini {
  width: 12px;
  height: 12px;
  border: 2px solid #fff;
  border-top-color: transparent;
  border-radius: 50%;
  animation: spin 0.6s linear infinite;
}

@keyframes spin {
  to { transform: rotate(360deg); }
}

/* Estados de carga y error */
.cargando, .error, .sin-resultados {
  text-align: center;
  padding: 4rem 2rem;
}

.spinner {
  width: 40px;
  height: 40px;
  border: 4px solid #f3f3f3;
  border-top: 4px solid #3498db;
  border-radius: 50%;
  animation: spin 1s linear infinite;
  margin: 0 auto 1rem;
}

/* Carrito */
.carrito-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: rgba(0,0,0,0.5);
  z-index: 200;
}

.carrito-panel {
  position: fixed;
  right: 0;
  top: 0;
  height: 100vh;
  width: 400px;
  background-color: white;
  box-shadow: -2px 0 8px rgba(0,0,0,0.1);
  z-index: 201;
  display: flex;
  flex-direction: column;
  animation: slideIn 0.3s ease-out;
}

@keyframes slideIn {
  from {
    transform: translateX(100%);
  }
  to {
    transform: translateX(0);
  }
}

.carrito-header {
  padding: 1rem;
  border-bottom: 1px solid #eee;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.btn-cerrar {
  background: none;
  border: none;
  font-size: 1.5rem;
  cursor: pointer;
  color: #666;
}

.carrito-vacio {
  flex: 1;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 2rem;
}

.carrito-items {
  flex: 1;
  overflow-y: auto;
  padding: 1rem;
}

.carrito-item {
  display: flex;
  align-items: center;
  gap: 1rem;
  padding: 1rem 0;
  border-bottom: 1px solid #eee;
}

.carrito-item img {
  width: 60px;
  height: 60px;
  object-fit: contain;
}

.item-info {
  flex: 1;
}

.item-info h4 {
  font-size: 0.875rem;
  margin-bottom: 0.25rem;
}

.item-controles {
  display: flex;
  align-items: center;
  gap: 0.5rem;
}

.item-controles button {
  width: 24px;
  height: 24px;
  border: 1px solid #ddd;
  background: white;
  cursor: pointer;
  border-radius: 4px;
}

.btn-eliminar {
  background: none;
  border: none;
  cursor: pointer;
  font-size: 1.2rem;
  opacity: 0.6;
}

.btn-eliminar:hover {
  opacity: 1;
}

.carrito-resumen {
  padding: 1rem;
  border-top: 1px solid #eee;
}

.resumen-linea {
  display: flex;
  justify-content: space-between;
  margin-bottom: 0.5rem;
}

.resumen-linea.total {
  font-weight: bold;
  font-size: 1.2rem;
  margin-top: 0.5rem;
  padding-top: 0.5rem;
  border-top: 1px solid #eee;
}

.carrito-acciones {
  padding: 1rem;
  display: flex;
  gap: 1rem;
}

.btn-vaciar {
  flex: 1;
  padding: 0.75rem;
  border: 1px solid #e74c3c;
  background: white;
  color: #e74c3c;
  border-radius: 4px;
  cursor: pointer;
}

.btn-checkout {
  flex: 1;
  padding: 0.75rem;
  background-color: #27ae60;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

/* Responsive */
@media (max-width: 768px) {
  .carrito-panel {
    width: 100%;
  }
  
  .productos-grid {
    grid-template-columns: repeat(auto-fill, minmax(150px, 1fr));
    gap: 1rem;
  }
  
  .buscador {
    width: 100%;
  }
  
  .lista-productos-header {
    flex-direction: column;
    gap: 1rem;
  }
}
```

---

## 🧪 Conceptos aplicados en el ejercicio

### useEffect en acción:

1. **Cargar datos al montar componente** (useProductos)
2. **Cleanup para evitar actualizaciones en componentes desmontados**
3. **Debounce en el buscador**
4. **Persistencia con localStorage**
5. **Efectos con dependencias** (guardar carrito cuando cambia)

### useContext en acción:

1. **Estado global del carrito** compartido entre componentes
2. **Evitar prop drilling** (no pasar carrito por props)
3. **Hook personalizado** (useCarrito) para mejor DX
4. **Provider envolviendo la aplicación**

### Patrones y mejores prácticas:

1. **Manejo de estados de carga y error**
2. **Optimización con lazy loading de imágenes**
3. **Feedback visual al usuario**
4. **Responsive design**
5. **Separación de responsabilidades**

---

## 📚 Para profundizar

1. **Optimizaciones**:
   - Implementar `useMemo` para cálculos costosos
   - Usar `React.memo` para evitar re-renders
   - Virtualización para listas largas

2. **Funcionalidades adicionales**:
   - Filtros por categoría
   - Ordenamiento por precio
   - Paginación
   - Wishlist/favoritos

3. **Mejoras de UX**:
   - Skeleton loaders
   - Animaciones con Framer Motion
   - Notificaciones toast
   - Modo oscuro

¡Con este ejercicio has aplicado useEffect y useContext en un escenario real y práctico!
        