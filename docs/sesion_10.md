# 🗓️ Workshop: Catálogo de Productos en React 

## 🚀 Objetivo

En este taller aprenderás a construir una aplicación web en React que consume datos desde una API pública para mostrar un catálogo de productos. Además, exploraremos buenas prácticas como la creación de componentes reutilizables, organización del código en capas (servicios, hooks y vistas), y cómo escribir pruebas automatizadas para asegurar que todo funciona correctamente.

Este workshop está pensado para personas que están comenzando en el mundo de React y desean adquirir una base sólida trabajando con datos externos y estructura de proyecto real.

---

## ✅ Requisitos Previos

- Tener instalado Node.js (versión 16 o superior)
- Conocimientos básicos de HTML, JavaScript y React
- Conocimientos básicos de la terminal (comandos npm y navegación de carpetas)

---

## ⚡ Paso 1: Crear el proyecto con Vite

[Vite](https://vitejs.dev/) es una herramienta moderna para crear proyectos frontend rápidamente. Mucho más rápido que `create-react-app` y optimizado para desarrollo moderno.

```bash
npm create vite@latest react-products-app -- --template react
cd react-products-app
npm install
```

Este comando crea una estructura básica de React con soporte de módulos ES, y luego instala las dependencias necesarias.

---

## ⚡ Paso 2: Instalar herramientas adicionales

- `axios`: herramienta para hacer peticiones HTTP de forma más amigable y con manejo automático de errores, cancelaciones, headers, etc.
- `vitest`: herramienta de testing rápida y moderna similar a Jest, pero pensada para proyectos con Vite.
- `jsdom`: simula un entorno de navegador para poder hacer pruebas de componentes que interactúan con el DOM.
- `@testing-library/react`: librería para testear componentes de React como si fueran usados por un usuario real.

```bash
npm install axios
npm install -D vitest jsdom @testing-library/react @testing-library/jest-dom @testing-library/user-event
```

📌 **Nota**: Aunque instalamos `axios`, en este proyecto usaremos `fetch` nativo para que los participantes principiantes aprendan cómo funciona el navegador al hacer peticiones HTTP. Pero al final del workshop podrás adaptar fácilmente tu código a `axios` si deseas usarlo.

### ¿Qué es `fetch`?

`fetch()` es una función nativa de JavaScript que permite hacer peticiones HTTP a servidores. Devuelve una `Promise`, por eso normalmente la usamos con `async/await` para manejar la respuesta de manera más clara.

---

## 🛠️ Paso 3: Configurar Vitest para pruebas

Vitest es una herramienta de pruebas moderna pensada para proyectos construidos con Vite. Para que nuestras pruebas funcionen correctamente con React y simulen el entorno del navegador, debemos configurar algunas opciones básicas.

### `vite.config.js`

Este archivo configura Vite y Vitest para incluir el entorno de pruebas y un archivo de setup para preparar Testing Library.

```js
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';

export default defineConfig({
  plugins: [react()],
  test: {
    globals: true,             // Permite usar 'describe', 'it', 'expect' sin importar cada uno
    environment: 'jsdom',      // Simula el DOM del navegador en las pruebas
    setupFiles: './src/test/setup.js', // Archivo que se ejecuta antes de cada test
  },
});
```

### `src/test/setup.js`

Este archivo importa `@testing-library/jest-dom`, que agrega matchers adicionales a Jest (como `toBeInTheDocument`).

```js
import '@testing-library/jest-dom';
```

### Agregar scripts en `package.json`

Esto nos permitirá correr las pruebas fácilmente desde la terminal:

```json
"scripts": {
  "dev": "vite",
  "build": "vite build",
  "preview": "vite preview",
  "test": "vitest",
  "test:ui": "vitest --ui",
  "test:coverage": "vitest --coverage"
}
```

Con esto ya puedes correr:

```bash
npm test
```

Y ejecutarás todas las pruebas del proyecto usando `Vitest`.

---

## 🌐 Paso 5: Crear funciones para consumir la API

La API que usaremos es pública: [https://dummyjson.com](https://dummyjson.com)

Creamos un módulo llamado `productsAPI` que contiene funciones reutilizables para comunicarse con esta API. Cada función hace uso de `fetch` para obtener los datos y maneja posibles errores lanzando mensajes adecuados. Estas funciones se utilizarán más adelante en los hooks y componentes.

### `src/services/api.js`

```js
const BASE_URL = 'https://dummyjson.com';

export const productsAPI = {
  async getAllProducts(limit = 10, skip = 0) {
    try {
      const response = await fetch(`${BASE_URL}/products?limit=${limit}&skip=${skip}`);
      if (!response.ok) throw new Error('Error al obtener productos');
      return await response.json();
    } catch (error) {
      throw new Error(`Error al obtener productos: ${error.message}`);
    }
  },

  async getProductById(id) {
    try {
      const response = await fetch(`${BASE_URL}/products/${id}`);
      if (!response.ok) throw new Error('Producto no encontrado');
      return await response.json();
    } catch (error) {
      throw new Error(`Error al obtener producto: ${error.message}`);
    }
  },

  async searchProducts(query) {
    try {
      const response = await fetch(`${BASE_URL}/products/search?q=${query}`);
      if (!response.ok) throw new Error('Error al buscar productos');
      return await response.json();
    } catch (error) {
      throw new Error(`Error en búsqueda: ${error.message}`);
    }
  }
};
```

### Explicación del código:

- `BASE_URL`: se define como constante para evitar repetir la dirección base de la API en cada función.
- Las funciones están marcadas como `async` porque devuelven promesas. `async` permite usar `await`, que pausa la ejecución hasta que se resuelva la promesa.
- `await fetch(...)`: hace una solicitud HTTP a la API.
- `if (!response.ok) throw ...`: verifica que la respuesta fue exitosa (códigos 200–299), si no, lanza un error.
- `await response.json()`: convierte el cuerpo de la respuesta en un objeto JavaScript.
- `limit = 10, skip = 0`: son **valores por defecto** en los parámetros. Si no se envían desde fuera, se usarán 10 productos y comenzará desde el primero.
- `try/catch`: se usa para manejar cualquier error que ocurra en la petición y lanzar un mensaje entendible.

Esto nos permite separar la lógica de red del resto del código, para poder reutilizarla o testearla más fácilmente.

---

## 🧠 Paso 6: Crear un hook personalizado: `useProducts`

Ahora encapsulamos toda la lógica de carga y búsqueda de productos en un hook llamado `useProducts`. Este hook podrá ser reutilizado por cualquier componente que necesite interactuar con la lista de productos.

### `src/hooks/useProducts.js`

```js
import { useState, useEffect } from 'react';
import { productsAPI } from '../services/api';

export function useProducts() {
  const [products, setProducts] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  async function loadProducts() {
    setLoading(true);
    try {
      const data = await productsAPI.getAllProducts();
      setProducts(data.products);
      setError(null);
    } catch (err) {
      setError(err.message);
      setProducts([]);
    } finally {
      setLoading(false);
    }
  }

  async function searchProducts(query) {
    setLoading(true);
    try {
      const data = await productsAPI.searchProducts(query);
      setProducts(data.products);
      setError(null);
    } catch (err) {
      setError(err.message);
      setProducts([]);
    } finally {
      setLoading(false);
    }
  }

  useEffect(() => {
    loadProducts();
  }, []);

  return { products, loading, error, searchProducts };
}
```

### Explicación del código:

- `useState` se usa para guardar el estado de los productos, si está cargando (`loading`) y si hay errores (`error`).
- `loadProducts` carga productos desde la API y actualiza el estado.
- `searchProducts` permite hacer búsquedas personalizadas a través de la API.
- `useEffect(() => loadProducts(), [])` asegura que los productos se cargan automáticamente cuando se monta el componente.
- Finalmente, se retorna un objeto con todo lo necesario para que un componente pueda usar este hook: productos, loading, error y función de búsqueda.

---