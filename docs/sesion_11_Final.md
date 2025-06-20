# Workshop: Creando una Tienda de Productos con React y Testing

## 📋 Objetivos del Workshop
Al finalizar este workshop podrás:
- Crear una aplicación React desde cero usando Vite
- Consumir APIs externas para mostrar datos
- Escribir tests unitarios para tus componentes
- Entender conceptos básicos de testing en React

## 🛠️ Prerrequisitos
- Node.js instalado (versión 16 o superior)
- Editor de código (VS Code recomendado)
- Conocimientos básicos de JavaScript
- Nociones básicas de HTML y CSS

---

## Fase 1: Configuración Inicial

### 1.1 Crear el Proyecto Base

Primero vamos a crear nuestro proyecto usando Vite, que es una herramienta moderna para crear aplicaciones React de forma rápida.

```bash
# Crear el proyecto (seleccionar React cuando pregunte)
npm create vite@latest tienda-productos -- --template react

# Navegar al directorio del proyecto
cd tienda-productos

# Instalar las dependencias básicas
npm install
```

**¿Qué acabamos de hacer?**
- `npm create vite@latest` crea un nuevo proyecto con Vite
- `--template react` especifica que queremos un template de React
- `npm install` instala todas las dependencias necesarias

### 1.2 Instalar Dependencias Adicionales

Ahora instalamos las librerías que necesitaremos para el workshop:

```bash
# Para hacer peticiones HTTP
npm install axios

# Para testing (herramientas de desarrollo)
npm install -D @testing-library/react @testing-library/jest-dom @testing-library/user-event

# Framework de testing
npm install -D vitest jsdom
```

📘 **¿Para qué sirve cada librería?**
- `axios`: Para hacer peticiones HTTP de forma sencilla (aunque en este workshop usaremos `fetch` que es nativo)
- `@testing-library/react`: Para testear componentes React simulando cómo los usuarios interactúan con ellos
- `@testing-library/jest-dom`: Agrega funciones especiales para verificar elementos del DOM (como `toBeInTheDocument()`)
- `@testing-library/user-event`: Para simular clicks, escritura y otras interacciones del usuario
- `vitest`: Framework de testing moderno y rápido, como Jest pero optimizado para Vite
- `jsdom`: Simula el DOM del navegador en Node.js para que los tests puedan "ver" elementos HTML

💡 **¿Qué significa `-D`?**
La bandera `-D` instala las dependencias como "desarrollo" (devDependencies). Esto significa que solo se usan mientras desarrollamos, no en producción.

### 1.3 Configurar el Entorno de Testing

#### Paso 1: Modificar `vite.config.js`

Reemplaza el contenido del archivo `vite.config.js` con:

```javascript
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

// https://vite.dev/config/
export default defineConfig({
  plugins: [react()],
  test: {
    globals: true,
    environment: 'jsdom',
    setupFiles: './src/test/setup.js',
  },
})
```

📘 **¿Qué hace cada configuración?**
- `plugins: [react()]`: Habilita el soporte para React en Vite
- `test.globals: true`: Permite usar funciones como `describe`, `it`, `expect` sin importarlas
- `test.environment: 'jsdom'`: Simula un navegador para que los tests puedan "ver" elementos HTML
- `test.setupFiles`: Archivo que se ejecuta antes de cada test para configurar cosas comunes

💡 **¿Por qué necesitamos jsdom?**
Los tests se ejecutan en Node.js, que no tiene DOM (elementos HTML). jsdom simula un navegador para que podamos testear componentes React.

#### Paso 2: Crear archivo de setup para tests

Crea la carpeta `src/test/` y dentro el archivo `setup.js`:

```javascript
import '@testing-library/jest-dom'
```

📘 **¿Qué hace este archivo?**
Este archivo se ejecuta automáticamente antes de cada test y importa funciones especiales para verificar elementos del DOM, como:
- `toBeInTheDocument()`: Verifica que un elemento existe en la página
- `toHaveAttribute()`: Verifica que un elemento tiene cierto atributo
- `toHaveClass()`: Verifica que un elemento tiene cierta clase CSS

💡 **¿Por qué en una carpeta separada?**
Es una buena práctica mantener los archivos de configuración de tests separados del código principal.

#### Paso 3: Agregar scripts de testing al `package.json`

Agrega estos scripts en la sección `"scripts"` de tu `package.json`:

```json
"scripts": {
  "dev": "vite",
  "build": "vite build",
  "lint": "eslint .",
  "preview": "vite preview",
  "test": "vitest",
  "test:ui": "vitest --ui",
  "test:coverage": "vitest --coverage"
}
```

---

## Fase 2: Desarrollo de la Aplicación

### 2.1 Crear la Estructura de Carpetas

Crea esta estructura dentro de `src/`:

```
src/
├── components/
│   ├── ProductCard.jsx
│   └── ProductList.jsx
├── test/
│   └── setup.js
└── App.jsx
```

### 2.2 Desarrollar el Componente Principal (App.jsx)

Reemplaza el contenido de `src/App.jsx`:

```jsx
import ProductList from './components/ProductList';

function App() {
  return (
    <div style={{ padding: '2rem', fontFamily: 'Arial, sans-serif' }}>
      <h1>🛒 Catálogo de Productos</h1>
      <ProductList />
    </div>
  );
}

export default App;
```

📘 **¿Qué hace este componente?**
- `import ProductList`: Trae el componente ProductList desde la carpeta components
- `function App()`: Define un componente funcional (la forma moderna de crear componentes)
- `return (...)`: Devuelve el JSX que se va a mostrar en pantalla
- `<ProductList />`: Renderiza el componente ProductList dentro de App
- `export default App`: Permite que otros archivos puedan importar este componente

💡 **¿Qué es JSX?**
JSX es una extensión de JavaScript que permite escribir HTML dentro de JavaScript. React lo convierte a JavaScript normal.

### 2.3 Crear el Componente ProductCard

Crea `src/components/ProductCard.jsx`:

```jsx
function ProductCard({ product }) {
  return (
    <div style={{
      border: '1px solid #ccc',
      padding: '1rem',
      borderRadius: '5px',
      backgroundColor: '#fff',
      boxShadow: '0 2px 4px rgba(0,0,0,0.1)'
    }}>
      <img 
        src={product.thumbnail} 
        alt={product.title}
        style={{ 
          width: '100%', 
          height: '200px', 
          objectFit: 'cover',
          borderRadius: '3px'
        }} 
      />
      <h3 style={{ margin: '0.5rem 0', color: '#333' }}>
        {product.title}
      </h3>
      <p style={{ color: '#666', fontSize: '0.9rem' }}>
        {product.description}
      </p>
      <strong style={{ color: '#e74c3c', fontSize: '1.2rem' }}>
        💲 {product.price}
      </strong>
    </div>
  );
}

export default ProductCard;
```

📘 **¿Qué hace este componente?**
- `{ product }`: Recibe un objeto `product` como prop (propiedad) desde el componente padre
- `product.thumbnail`: Accede a la propiedad `thumbnail` del objeto product
- `{product.title}`: Las llaves `{}` permiten escribir JavaScript dentro de JSX
- `alt={product.title}`: Atributo alt de la imagen para accesibilidad (lectores de pantalla)
- `objectFit: 'cover'`: Hace que la imagen mantenga sus proporciones y llene el contenedor

💡 **¿Qué son las props?**
Las props son la forma de pasar datos de un componente padre a un componente hijo. Es como pasar argumentos a una función.

✅ **¿Por qué estilos inline?**
Para simplicidad en este workshop usamos estilos inline. En proyectos reales es mejor usar CSS modules o styled-components.

### 2.4 Crear el Componente ProductList

Crea `src/components/ProductList.jsx`:

```jsx
import { useEffect, useState } from 'react';
import ProductCard from './ProductCard';

function ProductList() {
  // Estados para manejar los datos y el estado de la aplicación
  const [products, setProducts] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  // useEffect se ejecuta cuando el componente se monta
  useEffect(() => {
    // Función para obtener los productos de la API
    fetch('https://dummyjson.com/products?limit=10')
      .then((response) => {
        // Verificar si la respuesta es exitosa
        if (!response.ok) {
          throw new Error('Error al cargar productos');
        }
        return response.json();
      })
      .then((data) => {
        // Guardar los productos en el estado
        setProducts(data.products);
      })
      .catch((err) => {
        // Manejar errores
        setError(err.message);
      })
      .finally(() => {
        // Siempre ejecutar esto al final
        setLoading(false);
      });
  }, []); // El array vacío significa que solo se ejecuta una vez

  // Mostrar mensaje de carga
  if (loading) {
    return (
      <div style={{ textAlign: 'center', padding: '2rem' }}>
        <p>🔄 Cargando productos...</p>
      </div>
    );
  }

  // Mostrar mensaje de error
  if (error) {
    return (
      <div style={{ textAlign: 'center', padding: '2rem', color: 'red' }}>
        <p>❌ Error: {error}</p>
      </div>
    );
  }

  // Mostrar la lista de productos
  return (
    <div style={{
      display: 'grid',
      gridTemplateColumns: 'repeat(auto-fit, minmax(300px, 1fr))',
      gap: '1rem',
      padding: '1rem 0'
    }}>
      {products.map((product) => (
        <ProductCard key={product.id} product={product} />
      ))}
    </div>
  );
}

export default ProductList;
```

📘 **¿Qué hace `useState`?**
- `useState([])`: Crea una variable de estado `products` que empieza como array vacío
- `setProducts`: Función para cambiar el valor de `products`
- Cuando cambia el estado, React vuelve a renderizar el componente automáticamente

📘 **¿Qué hace `useEffect`?**
- Se ejecuta después de que el componente se renderiza por primera vez
- El array vacío `[]` significa "ejecutar solo una vez cuando el componente se monta"
- Es perfecto para hacer peticiones HTTP al cargar la página

📘 **¿Qué hace `fetch`?**
- `fetch()`: Función nativa de JavaScript para hacer peticiones HTTP
- `response.ok`: Verifica si la respuesta fue exitosa (status 200-299)
- `response.json()`: Convierte la respuesta a un objeto JavaScript
- `.then()`: Se ejecuta cuando la promesa se resuelve exitosamente
- `.catch()`: Se ejecuta si hay algún error
- `.finally()`: Se ejecuta siempre, haya error o no

💡 **¿Qué significa `limit=10`?**
Es un parámetro de la API que limita a 10 productos. Sin esto, traería todos los productos de la base de datos.

📘 **¿Qué hace `.map()`?**
- Recorre cada producto del array
- Por cada producto, crea un componente `<ProductCard>`
- `key={product.id}`: React necesita una key única para cada elemento de una lista
- Devuelve un nuevo array con todos los componentes ProductCard

✅ **¿Por qué tres estados diferentes?**
- `loading`: Para mostrar un spinner mientras carga
- `error`: Para mostrar mensajes de error si algo falla
- `products`: Para almacenar los datos cuando llegan exitosamente

Esta es la forma estándar de manejar peticiones HTTP en React.

### 2.5 Probar la Aplicación

Ejecuta el servidor de desarrollo:

```bash
npm run dev
```

Deberías ver tu aplicación funcionando en `http://localhost:5173`

---

## Fase 3: Testing de Componentes

### 3.1 ¿Por qué Testing?

Los tests nos ayudan a:
- Verificar que nuestros componentes funcionan correctamente
- Prevenir errores cuando hacemos cambios
- Documentar cómo deben comportarse nuestros componentes
- Tener confianza al hacer refactoring

### 3.2 Test del Componente ProductCard

Crea `src/components/ProductCard.test.jsx`:

```jsx
import { render, screen } from '@testing-library/react';
import ProductCard from './ProductCard';

describe('ProductCard', () => {
  // Datos de prueba
  const mockProduct = {
    id: 1,
    title: 'Producto de prueba',
    price: 150,
    description: 'Una descripción de prueba para el producto',
    thumbnail: 'https://dummyimage.com/300x300/cccccc/969696.png&text=Producto',
  };

  it('debería renderizar todos los elementos del producto', () => {
    // Renderizar el componente con datos de prueba
    render(<ProductCard product={mockProduct} />);
    
    // Verificar que todos los elementos estén presentes
    expect(screen.getByText('Producto de prueba')).toBeInTheDocument();
    expect(screen.getByText('Una descripción de prueba para el producto')).toBeInTheDocument();
    expect(screen.getByText(/150/)).toBeInTheDocument();
    
    // Verificar que la imagen tenga los atributos correctos
    const image = screen.getByRole('img');
    expect(image).toHaveAttribute('src', mockProduct.thumbnail);
    expect(image).toHaveAttribute('alt', mockProduct.title);
  });

  it('debería mostrar el precio con el símbolo de dinero', () => {
    render(<ProductCard product={mockProduct} />);
    
    // Verificar que el precio se muestre con el formato correcto
    expect(screen.getByText('💲 150')).toBeInTheDocument();
  });
});
```

📘 **¿Qué hace `describe`?**
- Agrupa tests relacionados bajo un nombre descriptivo
- Es como crear una "suite" o familia de tests para un componente
- Ayuda a organizar y leer los resultados de los tests

📘 **¿Qué hace `it` (o `test`)?**
- Define un test individual con una descripción específica
- `it('debería hacer algo')` se lee como "debería hacer algo"
- Cada `it` prueba un comportamiento específico del componente

📘 **¿Qué hace `render`?**
- Renderiza el componente React en un DOM virtual para testing
- Es como "montar" el componente en una página invisible
- Después podemos buscar elementos y verificar que estén ahí

📘 **¿Qué hace `screen.getByText`?**
- Busca un elemento que contenga el texto especificado
- `getByText('Producto de prueba')`: Busca exactamente ese texto
- `getByText(/150/)`: Busca texto que contenga "150" (usando regex)
- Si no encuentra el elemento, el test falla automáticamente

📘 **¿Qué hace `expect`?**
- Define lo que esperamos que sea verdad
- `toBeInTheDocument()`: Verifica que el elemento existe en el DOM
- `toHaveAttribute('src', url)`: Verifica que un elemento tiene cierto atributo con cierto valor

💡 **¿Por qué datos mock?**
Usamos datos falsos pero realistas para no depender de APIs externas en los tests. Esto hace los tests más rápidos y confiables.

### 3.3 Test del Componente ProductList

Crea `src/components/ProductList.test.jsx`:

```jsx
import { render, screen, waitFor } from '@testing-library/react';
import ProductList from './ProductList';
import { vi } from 'vitest';

// Mock de la función fetch global
const mockFetch = vi.fn();
vi.stubGlobal('fetch', mockFetch);

describe('ProductList', () => {
  // Datos de prueba
  const mockProducts = [
    {
      id: 1,
      title: 'Producto 1',
      description: 'Descripción del producto 1',
      price: 100,
      thumbnail: 'https://ejemplo.com/imagen1.jpg'
    },
    {
      id: 2,
      title: 'Producto 2',
      description: 'Descripción del producto 2',
      price: 200,
      thumbnail: 'https://ejemplo.com/imagen2.jpg'
    }
  ];

  beforeEach(() => {
    // Limpiar mocks antes de cada test
    mockFetch.mockClear();
  });

  it('debería mostrar mensaje de carga inicialmente', () => {
    // Simular una petición que nunca se resuelve
    mockFetch.mockImplementation(() => new Promise(() => {}));
    
    render(<ProductList />);
    
    // Verificar que se muestre el mensaje de carga
    expect(screen.getByText(/Cargando productos/)).toBeInTheDocument();
  });

  it('debería renderizar productos después de cargarlos exitosamente', async () => {
    // Simular una respuesta exitosa de la API
    mockFetch.mockResolvedValue({
      ok: true,
      json: () => Promise.resolve({
        products: mockProducts
      })
    });

    render(<ProductList />);

    // Verificar que inicialmente se muestre el mensaje de carga
    expect(screen.getByText(/Cargando productos/)).toBeInTheDocument();

    // Esperar a que los productos se carguen
    await waitFor(() => {
      expect(screen.getByText('Producto 1')).toBeInTheDocument();
      expect(screen.getByText('Producto 2')).toBeInTheDocument();
    });

    // Verificar que el mensaje de carga desaparezca
    expect(screen.queryByText(/Cargando productos/)).not.toBeInTheDocument();
  });

  it('debería mostrar mensaje de error si la petición falla', async () => {
    // Simular una respuesta de error
    mockFetch.mockResolvedValue({
      ok: false
    });

    render(<ProductList />);

    // Esperar a que se muestre el mensaje de error
    await waitFor(() => {
      expect(screen.getByText(/Error/)).toBeInTheDocument();
    });

    // Verificar que no se muestren productos
    expect(screen.queryByText('Producto 1')).not.toBeInTheDocument();
  });

  it('debería hacer la petición a la URL correcta', () => {
    mockFetch.mockResolvedValue({
      ok: true,
      json: () => Promise.resolve({ products: [] })
    });

    render(<ProductList />);

    // Verificar que se haga la petición a la URL correcta
    expect(mockFetch).toHaveBeenCalledWith('https://dummyjson.com/products?limit=10');
  });
});
```

📘 **¿Qué es un Mock?**
- Un "mock" es una imitación de algo real (como la función `fetch`)
- `vi.fn()`: Crea una función falsa que podemos controlar en los tests
- `vi.stubGlobal('fetch', mockFetch)`: Reemplaza la función `fetch` real con nuestra versión falsa
- Esto nos permite simular diferentes respuestas de la API sin hacer peticiones reales

📘 **¿Qué hace `mockResolvedValue`?**
- Hace que nuestra función falsa `fetch` devuelva una promesa que se resuelve con el valor que especificamos
- Es como decir: "cuando llamen a fetch, responde con esto"
- `mockImplementation`: Permite definir exactamente qué hace la función falsa

📘 **¿Qué hace `waitFor`?**
- Espera a que algo aparezca en el DOM de forma asíncrona
- Como `fetch` es asíncrono, los productos no aparecen inmediatamente
- `waitFor` revisa repetidamente hasta que la condición se cumple o se agota el tiempo

📘 **¿Qué hace `beforeEach`?**
- Se ejecuta antes de cada test individual
- `mockClear()`: Borra el historial de llamadas de la función mock
- Esto asegura que cada test empiece "limpio" sin afectar a otros tests

📘 **¿Diferencia entre `getByText` y `queryByText`?**
- `getByText`: Falla el test si no encuentra el elemento (bueno para verificar que algo está)
- `queryByText`: Devuelve `null` si no encuentra el elemento (bueno para verificar que algo NO está)
- `.not.toBeInTheDocument()`: Verifica que el elemento no existe

💡 **¿Por qué testear errores?**
Es importante probar que nuestra aplicación maneja correctamente los casos de error, no solo los casos exitosos.

✅ **¿Por qué estos tests son importantes?**
- Verifican que el componente maneja correctamente los estados de carga, éxito y error
- Aseguran que las peticiones HTTP se hacen correctamente
- Garantizan que la interfaz se actualiza apropiadamente según la respuesta de la API

**¿Qué estamos probando?**
- El estado de carga inicial
- La carga exitosa de productos
- El manejo de errores
- Que se haga la petición a la URL correcta

### 3.4 Ejecutar los Tests

```bash
# Ejecutar todos los tests
npm test

# Ejecutar tests con interfaz gráfica
npm run test:ui

# Ejecutar tests con reporte de cobertura
npm run test:coverage
```

📘 **¿Qué hace cada comando?**
- `npm test`: Ejecuta todos los tests en modo "watch" (se re-ejecutan automáticamente cuando cambias código)
- `npm run test:ui`: Abre una interfaz web donde puedes ver los tests de forma visual y interactiva
- `npm run test:coverage`: Muestra qué porcentaje de tu código está cubierto por tests

💡 **¿Qué significa "coverage" (cobertura)?**
La cobertura te dice qué líneas de código han sido ejecutadas por los tests. Un 100% significa que todos los tests pasan por todas las líneas de código.

✅ **Consejos para interpretar los resultados:**
- ✅ Verde: Test pasó correctamente
- ❌ Rojo: Test falló, necesita corrección
- ⚠️ Amarillo: Test se saltó o hay advertencias

📘 **¿Cómo leer los mensajes de error?**
Cuando un test falla, Vitest te muestra:
- Qué esperabas que pasara (`Expected`)
- Qué realmente pasó (`Received`)
- En qué línea del test ocurrió el error
- Una "diff" que muestra las diferencias

---

## Comandos Útiles

### Desarrollo
```bash
npm run dev          # Iniciar servidor de desarrollo
npm run build        # Crear build de producción
npm run preview      # Preview del build de producción
```

📘 **¿Qué hace cada comando de desarrollo?**
- `npm run dev`: Inicia un servidor local en `http://localhost:5173` con hot reload (se actualiza automáticamente cuando cambias código)
- `npm run build`: Crea una versión optimizada de tu aplicación para producción (archivos minificados y optimizados)
- `npm run preview`: Te permite ver cómo se vería tu aplicación en producción sin subirla a un servidor

### Testing
```bash
npm test             # Ejecutar tests en modo watch
npm run test:ui      # Interfaz gráfica para tests
npm run test:coverage # Reporte de cobertura de código
```

📘 **¿Qué significa "modo watch"?**
En modo watch, los tests se ejecutan automáticamente cada vez que guardas un archivo. Es súper útil para desarrollo porque ves inmediatamente si algo se rompió.

### Otros comandos útiles
```bash
npm run lint         # Verificar código con ESLint
npm install [paquete] # Instalar nueva dependencia
npm install -D [paquete] # Instalar dependencia de desarrollo
```

📘 **¿Qué hace ESLint?**
ESLint revisa tu código en busca de errores comunes y problemas de estilo. Es como un corrector ortográfico pero para código JavaScript.

💡 **Comandos adicionales útiles:**
```bash
npm list             # Ver todas las dependencias instaladas
npm outdated         # Ver qué paquetes tienen actualizaciones disponibles
npm run dev -- --port 3000  # Cambiar el puerto del servidor de desarrollo
```

---

## 🎯 Conceptos Clave Aprendidos

### React
📘 **Componentes funcionales**
- Son funciones que devuelven JSX (HTML dentro de JavaScript)
- `function MiComponente() { return <div>Hola</div>; }`
- Son la forma moderna y recomendada de crear componentes

📘 **Props (propiedades)**
- Forma de pasar datos de un componente padre a un hijo
- `<ProductCard product={miProducto} />` pasa `miProducto` como prop
- Se reciben como parámetros: `function ProductCard({ product })`

📘 **useState (estado local)**
- Hook para manejar datos que pueden cambiar en el componente
- `const [productos, setProductos] = useState([])` crea estado
- Cuando cambias el estado con `setProductos()`, React re-renderiza automáticamente

📘 **useEffect (efectos secundarios)**
- Hook para ejecutar código cuando el componente se monta o actualiza
- `useEffect(() => { /* código */ }, [])` se ejecuta una sola vez al montar
- Perfecto para peticiones HTTP, suscripciones, timers, etc.

💡 **¿Por qué hooks?**
Los hooks permiten usar estado y efectos en componentes funcionales, que antes solo estaban disponibles en componentes de clase.

### Testing
📘 **describe/it (estructura de tests)**
- `describe`: Agrupa tests relacionados bajo un nombre
- `it` o `test`: Define un test individual con descripción específica
- Ayuda a organizar y entender qué está siendo probado

📘 **render (renderizar para testing)**
- `render(<MiComponente />)` monta el componente en un DOM virtual
- Permite interactuar con el componente como si estuviera en el navegador
- Es el primer paso de casi todos los tests de React

📘 **screen (buscar elementos)**
- `screen.getByText()`: Busca elementos por su texto visible
- `screen.getByRole()`: Busca por rol de accesibilidad (img, button, etc.)
- `screen.queryByText()`: Como getByText pero no falla si no encuentra

📘 **waitFor (operaciones asíncronas)**
- Espera a que algo aparezca o cambie en el DOM
- Esencial para probar componentes que hacen peticiones HTTP
- `await waitFor(() => expect(algo).toBeInTheDocument())`

📘 **Mocking (simulación)**
- Crear versiones falsas de funciones o APIs para testing
- `vi.fn()` crea una función mock
- Permite controlar exactamente qué respuestas devuelve una función

📘 **expect (verificaciones)**
- Define qué esperamos que sea verdad
- `expect(elemento).toBeInTheDocument()`: verifica que existe
- `expect(funcion).toHaveBeenCalledWith(parametros)`: verifica llamadas

💡 **¿Por qué testear?**
- Previene errores cuando cambias código
- Documenta cómo debe comportarse tu aplicación
- Te da confianza para hacer cambios grandes
- Facilita el trabajo en equipo

### Herramientas
📘 **Vite (herramienta de build)**
- Herramienta moderna para crear aplicaciones web
- Mucho más rápida que Create React App
- Hot reload súper rápido durante desarrollo
- Optimizaciones automáticas para producción

📘 **Vitest (framework de testing)**
- Framework de testing moderno y rápido
- Integrado perfectamente con Vite
- Compatible con Jest pero más rápido
- Incluye interfaz gráfica para ver tests

📘 **Testing Library (librería de testing)**
- Enfoque centrado en el usuario (prueba cómo el usuario interactúa)
- Busca elementos como los vería un usuario real
- Mejores prácticas para testing de React
- Hace que los tests sean más mantenibles

💡 **¿Por qué estas herramientas?**
Son el stack moderno recomendado para React: rápidas, bien mantenidas y con gran comunidad.

---

## Fase 4: Hooks Personalizados y API

### 4.1 ¿Qué son los Hooks Personalizados?

📘 **¿Qué es un hook personalizado?**
- Un hook personalizado es una función que usa otros hooks de React
- Permite reutilizar lógica entre diferentes componentes
- Siempre debe empezar con la palabra "use" (como `useProducts`, `useAuth`, etc.)
- Es una forma de extraer lógica compleja y reutilizarla

💡 **¿Por qué crear hooks personalizados?**
- **Reutilización**: La misma lógica puede usarse en múltiples componentes
- **Organización**: Separa la lógica de negocio de la presentación
- **Testing**: Es más fácil testear la lógica por separado
- **Mantenimiento**: Cambios en un solo lugar afectan todos los usos

### 4.2 Crear el Archivo de API

Crea `src/api/products.js`:

```javascript
// Configuración base de la API
const API_BASE_URL = 'https://dummyjson.com';

/**
 * Obtiene productos de la API
 * @param {number} limit - Número de productos a obtener (por defecto 10)
 * @param {number} skip - Número de productos a saltar (para paginación)
 * @returns {Promise} Promesa que resuelve con los datos de productos
 */
export async function getProducts(limit = 10, skip = 0) {
  try {
    const response = await fetch(`${API_BASE_URL}/products?limit=${limit}&skip=${skip}`);
    
    if (!response.ok) {
      throw new Error(`Error HTTP: ${response.status}`);
    }
    
    const data = await response.json();
    return data;
  } catch (error) {
    console.error('Error al obtener productos:', error);
    throw error;
  }
}

/**
 * Busca productos por término de búsqueda
 * @param {string} query - Término de búsqueda
 * @param {number} limit - Número de productos a obtener
 * @returns {Promise} Promesa que resuelve con los productos encontrados
 */
export async function searchProducts(query, limit = 10) {
  try {
    if (!query || query.trim() === '') {
      // Si no hay término de búsqueda, devolver productos normales
      return getProducts(limit);
    }
    
    const response = await fetch(`${API_BASE_URL}/products/search?q=${encodeURIComponent(query)}&limit=${limit}`);
    
    if (!response.ok) {
      throw new Error(`Error HTTP: ${response.status}`);
    }
    
    const data = await response.json();
    return data;
  } catch (error) {
    console.error('Error al buscar productos:', error);
    throw error;
  }
}

/**
 * Obtiene un producto específico por ID
 * @param {number} id - ID del producto
 * @returns {Promise} Promesa que resuelve con el producto
 */
export async function getProductById(id) {
  try {
    const response = await fetch(`${API_BASE_URL}/products/${id}`);
    
    if (!response.ok) {
      throw new Error(`Error HTTP: ${response.status}`);
    }
    
    const data = await response.json();
    return data;
  } catch (error) {
    console.error('Error al obtener producto:', error);
    throw error;
  }
}
```

📘 **¿Qué hace cada función?**
- `getProducts()`: Obtiene una lista de productos con paginación
- `searchProducts()`: Busca productos por un término específico
- `getProductById()`: Obtiene un producto específico por su ID
- Todas manejan errores y usan `async/await` para código más limpio

💡 **¿Por qué separar la API?**
- **Centralización**: Todas las llamadas a la API están en un lugar
- **Reutilización**: Diferentes componentes pueden usar las mismas funciones
- **Mantenimiento**: Cambiar la URL base o lógica de error se hace una sola vez
- **Testing**: Es más fácil hacer mock de funciones específicas

### 4.3 Crear el Hook Personalizado

Crea `src/hooks/useProducts.js`:

```javascript
import { useState, useEffect, useCallback } from 'react';
import { getProducts, searchProducts } from '../api/products';

/**
 * Hook personalizado para manejar productos
 * @param {string} searchTerm - Término de búsqueda (opcional)
 * @param {number} limit - Número de productos a cargar
 * @returns {Object} Estado y funciones para manejar productos
 */
export function useProducts(searchTerm = '', limit = 10) {
  // Estados para manejar los datos
  const [products, setProducts] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  // Función para cargar productos (memoizada para evitar re-creaciones innecesarias)
  const loadProducts = useCallback(async (term = '', productLimit = limit) => {
    try {
      setLoading(true);
      setError(null);
      
      let data;
      if (term && term.trim() !== '') {
        // Si hay término de búsqueda, buscar productos
        data = await searchProducts(term, productLimit);
      } else {
        // Si no hay término, obtener productos normales
        data = await getProducts(productLimit);
      }
      
      setProducts(data.products || []);
    } catch (err) {
      setError(err.message);
      setProducts([]);
    } finally {
      setLoading(false);
    }
  }, [limit]);

  // Efecto para cargar productos cuando cambia el término de búsqueda
  useEffect(() => {
    loadProducts(searchTerm);
  }, [searchTerm, loadProducts]);

  // Función para recargar productos manualmente
  const refetch = useCallback(() => {
    loadProducts(searchTerm);
  }, [loadProducts, searchTerm]);

  // Función para limpiar búsqueda
  const clearSearch = useCallback(() => {
    loadProducts('');
  }, [loadProducts]);

  // Devolver estado y funciones útiles
  return {
    products,
    loading,
    error,
    refetch,
    clearSearch,
    // Meta información útil
    hasProducts: products.length > 0,
    isEmpty: !loading && products.length === 0,
  };
}
```

📘 **¿Qué hace este hook?**
- **Encapsula lógica**: Toda la lógica de cargar productos está en un lugar
- **Maneja estados**: loading, error, y productos de forma automática
- **Búsqueda automática**: Cuando cambia `searchTerm`, busca automáticamente
- **Funciones útiles**: `refetch` para recargar, `clearSearch` para limpiar
- **useCallback**: Evita que las funciones se recreen en cada render

💡 **¿Qué es useCallback?**
`useCallback` memoriza una función para que no se recree en cada render, lo que mejora la performance y evita efectos infinitos.

### 4.4 Actualizar ProductList con el Hook

Actualiza `src/components/ProductList.jsx`:

```jsx
import { useState } from 'react';
import ProductCard from './ProductCard';
import { useProducts } from '../hooks/useProducts';

function ProductList() {
  // Estado para el término de búsqueda
  const [searchTerm, setSearchTerm] = useState('');
  
  // Usar nuestro hook personalizado
  const { products, loading, error, hasProducts, isEmpty } = useProducts(searchTerm);

  // Manejar cambios en el input de búsqueda
  const handleSearchChange = (event) => {
    setSearchTerm(event.target.value);
  };

  // Limpiar búsqueda
  const handleClearSearch = () => {
    setSearchTerm('');
  };

  return (
    <div>
      {/* Sección de búsqueda */}
      <div style={{
        marginBottom: '2rem',
        padding: '1rem',
        backgroundColor: '#f8f9fa',
        borderRadius: '8px'
      }}>
        <div style={{ display: 'flex', gap: '1rem', alignItems: 'center' }}>
          <input
            type="text"
            placeholder="Buscar productos..."
            value={searchTerm}
            onChange={handleSearchChange}
            style={{
              flex: 1,
              padding: '0.5rem',
              border: '1px solid #ddd',
              borderRadius: '4px',
              fontSize: '1rem'
            }}
          />
          {searchTerm && (
            <button
              onClick={handleClearSearch}
              style={{
                padding: '0.5rem 1rem',
                backgroundColor: '#6c757d',
                color: 'white',
                border: 'none',
                borderRadius: '4px',
                cursor: 'pointer'
              }}
            >
              Limpiar
            </button>
          )}
        </div>
        
        {/* Mostrar qué se está buscando */}
        {searchTerm && (
          <p style={{ marginTop: '0.5rem', color: '#666', fontSize: '0.9rem' }}>
            {loading ? 'Buscando...' : `Resultados para: "${searchTerm}"`}
          </p>
        )}
      </div>

      {/* Estados de carga y error */}
      {loading && (
        <div style={{ textAlign: 'center', padding: '2rem' }}>
          <p>🔄 {searchTerm ? 'Buscando productos...' : 'Cargando productos...'}</p>
        </div>
      )}

      {error && (
        <div style={{ textAlign: 'center', padding: '2rem', color: 'red' }}>
          <p>❌ Error: {error}</p>
        </div>
      )}

      {/* Mensaje cuando no hay productos */}
      {isEmpty && !error && (
        <div style={{ textAlign: 'center', padding: '2rem', color: '#666' }}>
          <p>
            {searchTerm 
              ? `No se encontraron productos para "${searchTerm}"` 
              : 'No hay productos disponibles'}
          </p>
        </div>
      )}

      {/* Lista de productos */}
      {hasProducts && (
        <div style={{
          display: 'grid',
          gridTemplateColumns: 'repeat(auto-fit, minmax(300px, 1fr))',
          gap: '1rem',
          padding: '1rem 0'
        }}>
          {products.map((product) => (
            <ProductCard key={product.id} product={product} />
          ))}
        </div>
      )}
    </div>
  );
}

export default ProductList;
```

📘 **¿Qué cambió en ProductList?**
- **Hook personalizado**: Usa `useProducts` en lugar de `useEffect` + `fetch`
- **Búsqueda en tiempo real**: El input actualiza `searchTerm` y automáticamente busca
- **Mejor UX**: Botón para limpiar búsqueda y mensajes más descriptivos
- **Código más limpio**: La lógica compleja está en el hook, el componente solo se enfoca en UI

💡 **¿Por qué es mejor esta aproximación?**
- **Separación de responsabilidades**: El hook maneja datos, el componente maneja UI
- **Reutilización**: El hook se puede usar en otros componentes
- **Testing**: Puedes testear el hook y el componente por separado

### 4.5 Tests para el Hook Personalizado

Crea `src/hooks/useProducts.test.js`:

```javascript
import { renderHook, waitFor } from '@testing-library/react';
import { useProducts } from './useProducts';
import * as api from '../api/products';
import { vi } from 'vitest';

// Mock de las funciones de la API
vi.mock('../api/products');

describe('useProducts', () => {
  const mockProducts = [
    { id: 1, title: 'Producto 1', price: 100, description: 'Desc 1', thumbnail: 'img1.jpg' },
    { id: 2, title: 'Producto 2', price: 200, description: 'Desc 2', thumbnail: 'img2.jpg' }
  ];

  beforeEach(() => {
    vi.clearAllMocks();
  });

  it('debería cargar productos inicialmente', async () => {
    // Simular respuesta exitosa de la API
    api.getProducts.mockResolvedValue({ products: mockProducts });

    const { result } = renderHook(() => useProducts());

    // Verificar estado inicial
    expect(result.current.loading).toBe(true);
    expect(result.current.products).toEqual([]);

    // Esperar a que carguen los productos
    await waitFor(() => {
      expect(result.current.loading).toBe(false);
      expect(result.current.products).toEqual(mockProducts);
      expect(result.current.hasProducts).toBe(true);
    });
  });

  it('debería buscar productos cuando se proporciona searchTerm', async () => {
    api.searchProducts.mockResolvedValue({ products: [mockProducts[0]] });

    const { result } = renderHook(() => useProducts('laptop'));

    await waitFor(() => {
      expect(result.current.loading).toBe(false);
      expect(api.searchProducts).toHaveBeenCalledWith('laptop', 10);
      expect(result.current.products).toEqual([mockProducts[0]]);
    });
  });

  it('debería manejar errores correctamente', async () => {
    const errorMessage = 'Error de red';
    api.getProducts.mockRejectedValue(new Error(errorMessage));

    const { result } = renderHook(() => useProducts());

    await waitFor(() => {
      expect(result.current.loading).toBe(false);
      expect(result.current.error).toBe(errorMessage);
      expect(result.current.products).toEqual([]);
      expect(result.current.isEmpty).toBe(true);
    });
  });

  it('debería poder recargar productos con refetch', async () => {
    api.getProducts.mockResolvedValue({ products: mockProducts });

    const { result } = renderHook(() => useProducts());

    // Esperar carga inicial
    await waitFor(() => {
      expect(result.current.loading).toBe(false);
    });

    // Limpiar mocks para verificar nueva llamada
    vi.clearAllMocks();
    api.getProducts.mockResolvedValue({ products: [mockProducts[0]] });

    // Llamar refetch
    result.current.refetch();

    await waitFor(() => {
      expect(api.getProducts).toHaveBeenCalledTimes(1);
      expect(result.current.products).toEqual([mockProducts[0]]);
    });
  });
});
```

📘 **¿Qué hace `renderHook`?**
- Es una función especial de Testing Library para testear hooks
- `renderHook(() => useProducts())` "ejecuta" el hook en un entorno de testing
- `result.current` contiene el valor actual que devuelve el hook

💡 **¿Por qué testear hooks por separado?**
- **Aislamiento**: Pruebas más específicas y fáciles de debuggear
- **Reutilización**: Si el hook se usa en múltiples componentes, solo necesitas testearlo una vez
- **Lógica compleja**: Puedes probar todos los casos edge sin preocuparte por UI

### 4.6 Tests Actualizados para ProductList

Actualiza `src/components/ProductList.test.jsx`:

```javascript
import { render, screen, waitFor, fireEvent } from '@testing-library/react';
import ProductList from './ProductList';
import * as api from '../api/products';
import { vi } from 'vitest';

// Mock de las funciones de la API
vi.mock('../api/products');

describe('ProductList con búsqueda', () => {
  const mockProducts = [
    { id: 1, title: 'iPhone 15', description: 'Smartphone Apple', price: 999, thumbnail: 'iphone.jpg' },
    { id: 2, title: 'Samsung Galaxy', description: 'Smartphone Samsung', price: 899, thumbnail: 'samsung.jpg' }
  ];

  beforeEach(() => {
    vi.clearAllMocks();
  });

  it('debería mostrar productos inicialmente', async () => {
    api.getProducts.mockResolvedValue({ products: mockProducts });

    render(<ProductList />);

    // Verificar que se muestre el input de búsqueda
    expect(screen.getByPlaceholderText('Buscar productos...')).toBeInTheDocument();

    // Esperar a que carguen los productos
    await waitFor(() => {
      expect(screen.getByText('iPhone 15')).toBeInTheDocument();
      expect(screen.getByText('Samsung Galaxy')).toBeInTheDocument();
    });
  });

  it('debería buscar productos cuando se escribe en el input', async () => {
    // Mock para carga inicial
    api.getProducts.mockResolvedValue({ products: mockProducts });
    // Mock para búsqueda
    api.searchProducts.mockResolvedValue({ products: [mockProducts[0]] });

    render(<ProductList />);

    // Esperar carga inicial
    await waitFor(() => {
      expect(screen.getByText('iPhone 15')).toBeInTheDocument();
    });

    // Buscar en el input
    const searchInput = screen.getByPlaceholderText('Buscar productos...');
    fireEvent.change(searchInput, { target: { value: 'iPhone' } });

    // Verificar que se muestre el indicador de búsqueda
    await waitFor(() => {
      expect(screen.getByText('Resultados para: "iPhone"')).toBeInTheDocument();
      expect(api.searchProducts).toHaveBeenCalledWith('iPhone', 10);
    });
  });

  it('debería mostrar botón de limpiar cuando hay búsqueda', async () => {
    api.getProducts.mockResolvedValue({ products: mockProducts });

    render(<ProductList />);

    const searchInput = screen.getByPlaceholderText('Buscar productos...');
    
    // Escribir en el input
    fireEvent.change(searchInput, { target: { value: 'test' } });

    // Verificar que aparezca el botón de limpiar
    expect(screen.getByText('Limpiar')).toBeInTheDocument();
  });

  it('debería limpiar búsqueda al hacer click en limpiar', async () => {
    api.getProducts.mockResolvedValue({ products: mockProducts });
    api.searchProducts.mockResolvedValue({ products: [mockProducts[0]] });

    render(<ProductList />);

    const searchInput = screen.getByPlaceholderText('Buscar productos...');
    
    // Escribir en el input
    fireEvent.change(searchInput, { target: { value: 'test' } });
    
    // Click en limpiar
    fireEvent.click(screen.getByText('Limpiar'));

    // Verificar que el input se limpie
    expect(searchInput.value).toBe('');
    
    // Verificar que se carguen todos los productos nuevamente
    await waitFor(() => {
      expect(api.getProducts).toHaveBeenCalled();
    });
  });
});
```

📘 **¿Qué hace `fireEvent`?**
- Simula eventos del usuario como clicks, cambios en inputs, etc.
- `fireEvent.change(input, { target: { value: 'texto' } })` simula escribir en un input
- `fireEvent.click(button)` simula hacer click en un botón

✅ **¿Qué estamos testeando ahora?**
- Que la búsqueda funcione correctamente
- Que el botón de limpiar aparezca y funcione
- Que los indicadores de búsqueda se muestren apropiadamente
- Que se hagan las llamadas correctas a la API

---

# Workshop React - Paso 6: Build y Deploy con Netlify

## 🚀 Llevando tu Aplicación a Producción

En este paso final, aprenderás a publicar tu aplicación React en internet para que cualquiera pueda acceder a ella. Usaremos Netlify, una plataforma gratuita y fácil de usar para principiantes.

---

## 6.1 Preparar el Proyecto para Producción

### Verificar que Todo Funciona Localmente

Antes de hacer deploy, asegúrate de que todo funcione correctamente:

```bash
# Ejecutar tests para verificar que todo esté bien
npm test

# Probar el build de producción
npm run build

# Ver el build localmente
npm run preview
```

📘 **¿Qué hace cada comando?**
- `npm test`: Ejecuta todos los tests para verificar que no hay errores
- `npm run build`: Crea una versión optimizada para producción
- `npm run preview`: Te permite ver cómo se verá en producción

💡 **¿Por qué es importante probar el build?**
A veces el código funciona en desarrollo pero falla en producción debido a optimizaciones y configuraciones diferentes.

### Verificar Variables de Entorno

Si tu aplicación usa variables de entorno, verifica que estén configuradas:

```javascript
// src/config/environment.js
export const config = {
  apiUrl: import.meta.env.VITE_API_URL || 'https://dummyjson.com',
  environment: import.meta.env.MODE,
  isDevelopment: import.meta.env.DEV,
  isProduction: import.meta.env.PROD,
};
```

📘 **¿Qué son las variables de entorno?**
- Valores que pueden cambiar según donde esté ejecutándose la app (desarrollo, testing, producción)
- En Vite, las variables deben empezar con `VITE_`
- Se definen en archivos `.env`, `.env.local`, `.env.production`

💡 **Ejemplo de .env.production:**
```bash
VITE_API_URL=https://api.mitienda.com
VITE_GOOGLE_ANALYTICS_ID=UA-123456789-1
```

---

## 6.2 Subir el Proyecto a GitHub

### Paso 1: Inicializar Git (si no lo has hecho)

```bash
# Inicializar repositorio git
git init

# Agregar todos los archivos
git add .

# Hacer primer commit
git commit -m "feat: aplicación React completa con routing y carrito"
```

📘 **¿Qué es Git?**
- Sistema de control de versiones que guarda el historial de cambios de tu código
- Te permite colaborar con otros desarrolladores
- GitHub es una plataforma que aloja repositorios Git en la nube

### Paso 2: Crear Repositorio en GitHub

1. Ve a [github.com](https://github.com) y crea una cuenta si no tienes
2. Haz click en "New repository" (botón verde)
3. Nombra tu repositorio (ej: `mi-tienda-react`)
4. Selecciona "Public" para que sea gratuito
5. **NO** selecciones "Initialize with README" (ya tienes código)
6. Haz click en "Create repository"

### Paso 3: Conectar tu Proyecto Local con GitHub

```bash
# Agregar el repositorio remoto (reemplaza con tu URL)
git remote add origin https://github.com/TU_USUARIO/mi-tienda-react.git

# Cambiar a la rama main (GitHub usa main por defecto)
git branch -M main

# Subir el código
git push -u origin main
```

📘 **¿Qué hace cada comando?**
- `git remote add origin`: Conecta tu proyecto local con el repositorio en GitHub
- `git branch -M main`: Cambia el nombre de la rama principal a "main"
- `git push -u origin main`: Sube tu código a GitHub por primera vez

💡 **¿Problemas con autenticación?**
GitHub requiere autenticación. Opciones:
- **HTTPS con token**: Genera un Personal Access Token en GitHub Settings
- **SSH**: Configura llaves SSH (más seguro)
- **GitHub CLI**: `gh auth login` para autenticación fácil

### Paso 4: Verificar que se Subió Correctamente

Ve a tu repositorio en GitHub y verifica que todos los archivos estén ahí:
- `src/` con todos tus componentes
- `package.json` con las dependencias
- `vite.config.js` con la configuración
- Tests y otros archivos

---

## 6.3 Deploy Automático con Netlify

### Paso 1: Crear Cuenta en Netlify

1. Ve a [netlify.com](https://netlify.com)
2. Haz click en "Sign up"
3. Selecciona "GitHub" para registrarte con tu cuenta de GitHub
4. Autoriza a Netlify para acceder a tus repositorios

📘 **¿Por qué usar Netlify?**
- **Gratuito**: Plan free generoso para proyectos personales
- **Fácil**: Deploy automático desde GitHub
- **Rápido**: CDN global para cargar tu app rápidamente
- **HTTPS**: Certificados SSL gratuitos
- **Builds automáticos**: Se actualiza cuando haces push a GitHub

### Paso 2: Crear Nuevo Sitio

1. En el dashboard de Netlify, haz click en "New site from Git"
2. Selecciona "GitHub"
3. Autoriza a Netlify (si no lo hiciste antes)
4. Busca y selecciona tu repositorio `mi-tienda-react`

### Paso 3: Configurar Build Settings

En la pantalla de configuración:

```bash
# Build command (comando para crear el build)
npm run build

# Publish directory (carpeta donde está el build)
dist

# Branch to deploy (rama a desplegar)
main
```

⚠️ **IMPORTANTE**: Aunque configures esto manualmente, es MUY recomendable crear también el archivo `netlify.toml` (explicado más abajo) para evitar errores comunes.

📘 **¿Por qué estas configuraciones?**
- `npm run build`: Le dice a Netlify cómo crear la versión de producción
- `dist`: Vite genera el build en la carpeta `dist` (no `build` como Create React App)
- `main`: La rama principal que se desplegará automáticamente

💡 **Para otros frameworks:**
- **Create React App**: Build command `npm run build`, publish directory `build`
- **Next.js**: Build command `npm run build`, publish directory `out` (con export)
- **Nuxt.js**: Build command `npm run generate`, publish directory `dist`

### Paso 4: Deploy!

1. Haz click en "Deploy site"
2. Netlify comenzará a:
   - Clonar tu repositorio
   - Instalar dependencias (`npm install`)
   - Ejecutar el build (`npm run build`)
   - Publicar los archivos
3. En unos minutos tendrás un enlace como `https://amazing-fermat-123456.netlify.app`

### Paso 5: Configurar Dominio Personalizado (Opcional)

1. En el dashboard del sitio, ve a "Domain settings"
2. Haz click en "Add custom domain"
3. Ingresa tu dominio (ej: `mitienda.com`)
4. Sigue las instrucciones para configurar DNS

📘 **¿Cómo conseguir un dominio?**
- **Gratuitos**: `.tk`, `.ml`, `.ga` en Freenom
- **Baratos**: Namecheap, GoDaddy, Google Domains
- **Para estudiantes**: GitHub Student Pack incluye dominios gratis

---

## 6.4 Configuración Avanzada de Deploy

### Variables de Entorno en Netlify

1. En tu sitio de Netlify, ve a "Site settings"
2. En el menú lateral, selecciona "Environment variables"
3. Haz click en "Add variable"
4. Agrega tus variables:
   ```
   Key: VITE_API_URL
   Value: https://api.mitienda.com
   ```

📘 **¿Por qué variables de entorno en producción?**
- **Seguridad**: No exponer datos sensibles en el código
- **Flexibilidad**: Diferentes configuraciones para desarrollo/producción
- **APIs**: URLs diferentes para development y production

### Configuración de Netlify con netlify.toml (IMPORTANTE)

Crea `netlify.toml` en la raíz de tu proyecto (no en `public/`):

```toml
[build]
  command = "npm run build"
  publish = "dist"

[[redirects]]
  from = "/*"
  to = "/index.html"
  status = 200
```

📘 **¿Por qué usar netlify.toml en lugar de _redirects?**
- **Más confiable**: Netlify lo detecta automáticamente
- **Configuración centralizada**: Build settings y redirects en un archivo
- **Menos errores**: Evita problemas de configuración manual en el dashboard
- **Versionado**: Se guarda en Git junto con tu código

💡 **Estructura correcta del proyecto:**
```
/mi-tienda-react
├── dist/              ⬅️ carpeta generada por Vite
├── src/
├── public/
├── netlify.toml       ⬅️ 🔥 DEBE estar en la raíz
├── vite.config.js
├── package.json
```

### Aplicar la Configuración

```bash
# Agregar el archivo de configuración
git add netlify.toml
git commit -m "fix: add Netlify config to serve dist correctly"
git push
```

📘 **¿Qué hace cada configuración?**
- `command = "npm run build"`: Le dice a Netlify cómo construir tu app
- `publish = "dist"`: Especifica que los archivos finales están en la carpeta `dist`
- `redirects`: Todas las rutas van a `index.html` para que React Router funcione

### Optimizaciones de Performance

Crea `public/_headers`:

```bash
# Cachear archivos estáticos por 1 año
/static/*
  Cache-Control: public, max-age=31536000, immutable

# Cachear assets de Vite
/assets/*
  Cache-Control: public, max-age=31536000, immutable

# No cachear el HTML principal
/
  Cache-Control: public, max-age=0, must-revalidate

/*.html
  Cache-Control: public, max-age=0, must-revalidate
```

📘 **¿Qué hacen estos headers?**
- **Cache-Control**: Define cuánto tiempo el navegador guarda archivos en caché
- **max-age=31536000**: 1 año en segundos
- **immutable**: El archivo nunca cambia (perfecto para assets con hash)
- **must-revalidate**: Verificar con el servidor antes de usar caché

---

## 6.5 Deploy Automático con GitHub Actions (Avanzado)

Para mayor control, puedes usar GitHub Actions:

Crea `.github/workflows/deploy.yml`:

```yaml
name: Deploy to Netlify

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    
    steps:
    - name: Checkout code
      uses: actions/checkout@v3
      
    - name: Setup Node.js
      uses: actions/setup-node@v3
      with:
        node-version: '18'
        cache: 'npm'
        
    - name: Install dependencies
      run: npm ci
      
    - name: Run tests
      run: npm test
      
    - name: Build
      run: npm run build
      env:
        VITE_API_URL: ${{ secrets.VITE_API_URL }}
        
    - name: Deploy to Netlify
      uses: nwtgck/actions-netlify@v2.0
      with:
        publish-dir: './dist'
        production-branch: main
        github-token: ${{ secrets.GITHUB_TOKEN }}
        deploy-message: "Deploy from GitHub Actions"
      env:
        NETLIFY_AUTH_TOKEN: ${{ secrets.NETLIFY_AUTH_TOKEN }}
        NETLIFY_SITE_ID: ${{ secrets.NETLIFY_SITE_ID }}
```

📘 **¿Qué hace GitHub Actions?**
- **CI/CD**: Integración y deploy continuo
- **Automático**: Se ejecuta en cada push a la rama main
- **Tests**: Ejecuta tests antes de hacer deploy
- **Secrets**: Variables seguras para tokens y configuraciones

---

## 6.6 Monitoreo y Analytics

### Google Analytics (Opcional)

Instala analytics:

```bash
npm install gtag
```

Configura en `src/main.jsx`:

```jsx
import { gtag } from 'gtag';

// Configurar Google Analytics
if (import.meta.env.PROD && import.meta.env.VITE_GA_ID) {
  gtag('config', import.meta.env.VITE_GA_ID, {
    page_title: document.title,
    page_location: window.location.href
  });
}
```

### Netlify Analytics

1. En tu sitio de Netlify, ve a "Analytics"
2. Habilita "Netlify Analytics" (plan paid, pero hay trial gratuito)
3. Ve métricas de:
   - Visitantes únicos
   - Page views
   - Referrers
   - Geolocalización

📘 **¿Qué métricas son importantes?**
- **Page views**: Qué páginas visitan más
- **Bounce rate**: % que sale inmediatamente
- **Load time**: Qué tan rápido carga tu app
- **Mobile vs Desktop**: Dispositivos de tus usuarios

---

## 6.7 Alternativas de Deploy

### GitHub Pages (Gratuito)

```bash
# Instalar gh-pages
npm install --save-dev gh-pages

# Agregar script en package.json
"scripts": {
  "deploy": "gh-pages -d dist"
}

# Hacer deploy
npm run build
npm run deploy
```

📘 **GitHub Pages vs Netlify:**
- **GitHub Pages**: Gratuito, pero solo sitios estáticos
- **Netlify**: Más funciones (redirects, forms, functions)
- **Ambos**: Perfectos para React apps

### Vercel (Alternativa Premium)

```bash
# Instalar Vercel CLI
npm install -g vercel

# Deploy
vercel

# Deploy a producción
vercel --prod
```

📘 **¿Cuándo usar Vercel?**
- Creado por el equipo de Next.js
- Excelente para React/Next.js
- Funciones serverless fáciles
- Analytics y performance insights

### Railway (Para Full-Stack)

Si tu app necesita backend:

```bash
# Instalar Railway CLI
npm install -g @railway/cli

# Login y deploy
railway login
railway deploy
```

📘 **¿Cuándo usar Railway?**
- Cuando necesitas base de datos
- Apps full-stack (frontend + backend)
- Variables de entorno más complejas
- Servicios adicionales (Redis, PostgreSQL)

---

## 6.8 Solución de Problemas Comunes

### Error: "Page Not Found" en Rutas (SOLUCIÓN CONFIRMADA)

**Problema**: `/product/123` funciona localmente pero da 404 en producción.

**❌ Método anterior (menos confiable)**:
```bash
# public/_redirects
/*    /index.html   200
```

**✅ Solución confirmada que SIEMPRE funciona**:

1. **Crea `netlify.toml` en la raíz del proyecto** (no en `public/`):

```toml
[build]
  command = "npm run build"
  publish = "dist"

[[redirects]]
  from = "/*"
  to = "/index.html"
  status = 200
```

2. **Verifica la estructura de tu repositorio**:
```
/mi-tienda-react
├── dist/              ⬅️ carpeta generada por Vite
├── src/
├── public/
├── netlify.toml       ⬅️ 🔥 DEBE estar aquí
├── vite.config.js
├── package.json
```

3. **Commit y push para que Netlify lo detecte**:
```bash
git add netlify.toml
git commit -m "fix: add Netlify config to serve dist correctly"
git push
```

4. **Verifica en Netlify Dashboard**:
   - Ve a tu sitio → "Site settings" → "Build & deploy"
   - Confirma que aparezca:
     - **Build command**: `npm run build`
     - **Publish directory**: `dist`

📘 **¿Por qué esta solución es mejor?**
- **Más confiable**: Netlify lee automáticamente `netlify.toml`
- **Versionado**: El archivo se guarda en Git con tu código
- **Menos errores**: No depende de configuración manual del dashboard
- **Centralizado**: Build settings y redirects en un solo lugar

### Error: Variables de Entorno No Funcionan

**Problema**: `import.meta.env.VITE_API_URL` es `undefined`.

**Soluciones**:
1. Variables deben empezar con `VITE_`
2. Definir en Netlify environment variables
3. Verificar que estén en `.env.production`

### Build Falla por Errores de Linting

**Problema**: Build falla por warnings de ESLint.

**Solución**: Configurar Vite para no fallar por warnings:

```javascript
// vite.config.js
export default defineConfig({
  plugins: [react()],
  build: {
    rollupOptions: {
      onwarn(warning, warn) {
        // Ignorar warnings específicos
        if (warning.code === 'UNUSED_EXTERNAL_IMPORT') return;
        warn(warning);
      }
    }
  }
});
```

### App Carga Lento

**Problemas comunes**:
- Imágenes muy grandes
- Muchas dependencias
- Sin lazy loading

**Soluciones**:
```javascript
// 1. Lazy loading de páginas
const ProductDetail = lazy(() => import('./pages/ProductDetail'));

// 2. Optimizar imágenes
<img 
  src={product.thumbnail} 
  loading="lazy"
  width="300" 
  height="200"
/>

// 3. Analizar bundle
npm run build -- --analyze
```

---

## 6.9 Mantenimiento Post-Deploy

### Actualizaciones Automáticas

Una vez configurado, tu flujo será:

```bash
# 1. Hacer cambios localmente
git add .
git commit -m "feat: nueva funcionalidad"
git push origin main

# 2. Netlify automáticamente:
# - Detecta el push
# - Ejecuta tests
# - Hace build
# - Despliega si todo está bien
```

### Monitoreo de Errores

Considera usar servicios como:
- **Sentry**: Para tracking de errores en producción
- **LogRocket**: Para reproducir sesiones de usuario
- **Hotjar**: Para heatmaps y análisis de UX

### Backups y Rollbacks

```bash
# Ver deploys anteriores en Netlify
# Hacer rollback desde el dashboard

# O con CLI
netlify sites:list
netlify rollback
```

---

## 6.10 Optimizaciones de Performance

### Análisis de Bundle

```bash
# Ver qué librerías ocupan más espacio
npm run build -- --analyze

# Alternativa con webpack-bundle-analyzer
npm install -g webpack-bundle-analyzer
npx webpack-bundle-analyzer dist
```

### Code Splitting

```jsx
// Lazy loading de páginas
import { lazy, Suspense } from 'react';

const ProductDetail = lazy(() => import('./pages/ProductDetail'));
const Cart = lazy(() => import('./pages/Cart'));

function App() {
  return (
    <Suspense fallback={<div>Cargando...</div>}>
      <Routes>
        <Route path="/product/:id" element={<ProductDetail />} />
        <Route path="/cart" element={<Cart />} />
      </Routes>
    </Suspense>
  );
}
```

### Optimización de Imágenes

```jsx
// Componente de imagen optimizada
function OptimizedImage({ src, alt, ...props }) {
  return (
    <img
      src={src}
      alt={alt}
      loading="lazy"
      decoding="async"
      {...props}
      style={{
        ...props.style,
        maxWidth: '100%',
        height: 'auto'
      }}
    />
  );
}
```

---

## 🎯 Resumen del Deploy

### ✅ Lo que hemos logrado:

**Preparación:**
- ✅ Verificar que todo funciona localmente
- ✅ Configurar variables de entorno
- ✅ Optimizar para producción

**GitHub:**
- ✅ Subir código a repositorio
- ✅ Configurar Git correctamente
- ✅ Mantener historial de cambios

**Netlify Deploy:**
- ✅ Deploy automático desde GitHub
- ✅ Configuración de build correcto
- ✅ URL pública funcionando
- ✅ HTTPS automático

**Optimizaciones:**
- ✅ Redirects para React Router
- ✅ Headers de performance
- ✅ Variables de entorno seguras

### 🚀 Tu aplicación ahora está en línea!

**URLs de ejemplo:**
- **Staging**: `https://gilded-praline-e439e2.netlify.app/`
- **Custom domain**: `https://mitienda.com` (si configuraste)