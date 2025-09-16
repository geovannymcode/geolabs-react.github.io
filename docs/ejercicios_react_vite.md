# Taller de React: Consumo de API y Despliegue

## 📋 Descripción
En este taller aprenderemos a crear una aplicación React desde cero, consumir la API JSONPlaceholder para mostrar posts, implementar un sistema simple de caché del lado del cliente, usar Git para control de versiones y finalmente desplegar la aplicación en Vercel.

## 🎯 Objetivos
- Crear una aplicación React desde cero
- Consumir una API externa (JSONPlaceholder)
- Implementar caché básico en el cliente
- Usar Git para control de versiones
- Desplegar la aplicación en Vercel

## 🔧 Requisitos previos
- Node.js instalado (versión 14 o superior)
- Editor de código (VS Code recomendado)
- Conocimientos básicos de JavaScript
- Cuenta de GitHub
- Cuenta de Vercel (crearemos una durante el taller)

## 🚀 Paso 1: Configuración inicial del proyecto

### 1.1 Crear una nueva aplicación React con Vite

Vite es una herramienta de compilación que proporciona una experiencia de desarrollo más rápida comparada con Create React App.

Abre tu terminal y ejecuta:

```bash
# Crear proyecto con Vite
npm create vite@latest mi-primera-app-react -- --template react

# Entrar al directorio del proyecto
cd mi-primera-app-react

# Instalar dependencias
npm install
```

### 1.2 Estructura inicial del proyecto

Exploremos la estructura de archivos generada:

- `src/`: Directorio principal donde escribiremos nuestro código
  - `main.jsx`: Punto de entrada de la aplicación
  - `App.jsx`: Componente principal
- `public/`: Archivos estáticos
- `index.html`: Archivo HTML principal
- `package.json`: Configuración del proyecto

### 1.3 Iniciar el servidor de desarrollo

```bash
npm run dev
```

Abre tu navegador en http://localhost:5173 (o el puerto que te indique la terminal).

## 📁 Paso 2: Creación de estructura de carpetas

### 2.1 Organizar la estructura del proyecto

```bash
# Crear carpetas para componentes y servicios
mkdir -p src/components src/services
```

### 2.2 Explicación de la estructura
- `components/`: Contendrá todos nuestros componentes React
- `services/`: Contendrá el código para interactuar con la API

## 🧩 Paso 3: Creación de los componentes principales

### 3.1 Crear el componente PostList

Este componente mostrará la lista de posts obtenidos de la API.

Crea el archivo `src/components/PostList.jsx`:

```jsx
import { useState, useEffect } from 'react';

function PostList({ onSelectPost }) {
  // Estado para almacenar los posts y el estado de carga
  const [posts, setPosts] = useState([]);
  const [loading, setLoading] = useState(true);

  // useEffect se ejecuta cuando el componente se monta
  useEffect(() => {
    // Función para obtener los posts de la API
    const fetchPosts = async () => {
      try {
        // Por ahora, hacemos la petición directamente aquí
        // Más tarde la moveremos a un servicio
        const response = await fetch('https://jsonplaceholder.typicode.com/posts');
        
        if (!response.ok) {
          throw new Error(`Error HTTP: ${response.status}`);
        }
        
        const data = await response.json();
        setPosts(data); // Guardamos los posts en el estado
      } catch (error) {
        console.error("Error al cargar posts:", error);
      } finally {
        setLoading(false); // Marcamos que ya terminó la carga
      }
    };
    
    fetchPosts(); // Llamamos a la función
  }, []); // El array vacío significa que solo se ejecuta una vez al montar el componente

  // Si está cargando, mostramos un mensaje
  if (loading) return <p>Cargando posts...</p>;

  return (
    <div className="post-list">
      <h2>Listado de Posts</h2>
      {/* Iteramos sobre la lista de posts */}
      {posts.map(post => (
        <div 
          key={post.id} 
          className="post-card"
          onClick={() => onSelectPost(post.id)} // Al hacer clic, llamamos a la función que nos pasaron
        >
          <h3>{post.title}</h3>
          <p>{post.body.substring(0, 100)}...</p> {/* Mostramos solo una parte del contenido */}
        </div>
      ))}
    </div>
  );
}

export default PostList;
```

### 3.2 Crear el componente PostDetail

Este componente mostrará los detalles de un post específico.

Crea el archivo `src/components/PostDetail.jsx`:

```jsx
import { useState, useEffect } from 'react';

function PostDetail({ id, onBack }) {
  // Estados para el post y la carga
  const [post, setPost] = useState(null);
  const [loading, setLoading] = useState(true);

  // Obtener los detalles del post cuando cambia el id
  useEffect(() => {
    const fetchPost = async () => {
      try {
        // Por ahora, hacemos la petición directamente aquí
        const response = await fetch(`https://jsonplaceholder.typicode.com/posts/${id}`);
        
        if (!response.ok) {
          throw new Error(`Error HTTP: ${response.status}`);
        }
        
        const data = await response.json();
        setPost(data);
      } catch (error) {
        console.error(`Error al cargar post ${id}:`, error);
      } finally {
        setLoading(false);
      }
    };
    
    fetchPost();
  }, [id]); // Este efecto se ejecuta cuando cambia el id

  // Estados de carga y error
  if (loading) return <p>Cargando detalles del post...</p>;
  if (!post) return <p>Post no encontrado</p>;

  return (
    <div className="post-detail">
      <h2>{post.title}</h2>
      <p>{post.body}</p>
      <p><small>Post ID: {post.id} - Usuario ID: {post.userId}</small></p>
      <button onClick={onBack}>Volver a la lista</button>
    </div>
  );
}

export default PostDetail;
```

### 3.3 Actualizar App.jsx

Ahora vamos a modificar el componente principal para usar nuestros nuevos componentes.

Modifica el archivo `src/App.jsx`:

```jsx
import { useState } from 'react';
import PostList from './components/PostList';
import PostDetail from './components/PostDetail';
import './App.css';

function App() {
  // Estado para almacenar el ID del post seleccionado
  const [selectedPostId, setSelectedPostId] = useState(null);

  return (
    <div className="app">
      <header>
        <h1>Mi Primera App React - JSONPlaceholder</h1>
      </header>
      
      <main>
        {selectedPostId ? (
          // Si hay un post seleccionado, mostramos sus detalles
          <PostDetail 
            id={selectedPostId} 
            onBack={() => setSelectedPostId(null)} 
          />
        ) : (
          // Si no, mostramos la lista de posts
          <PostList onSelectPost={setSelectedPostId} />
        )}
      </main>
    </div>
  );
}

export default App;
```

## 🔄 Paso 4: Creación del servicio para consumir la API

### 4.1 Crear el servicio de posts

Vamos a mover la lógica de consumo de API a un servicio separado.

Crea el archivo `src/services/postService.js`:

```js
// Implementación simple de caché del lado del cliente
const cache = {
  posts: null,           // Almacenará todos los posts
  postDetails: {},       // Almacenará posts individuales por ID
  timestamp: null,       // Cuándo se actualizó por última vez la caché
  // Caché válido por 5 minutos (300,000 ms)
  isValid: () => cache.timestamp && (Date.now() - cache.timestamp < 5 * 60 * 1000)
};

// Función para obtener todos los posts
export async function getPosts() {
  // Si tenemos datos en caché válidos, los devolvemos
  if (cache.posts && cache.isValid()) {
    console.log('Usando posts desde caché');
    return cache.posts;
  }

  // Si no hay caché o expiró, hacemos la petición
  try {
    const response = await fetch('https://jsonplaceholder.typicode.com/posts');
    
    if (!response.ok) {
      throw new Error(`Error HTTP: ${response.status}`);
    }
    
    const data = await response.json();
    
    // Actualizamos la caché
    cache.posts = data;
    cache.timestamp = Date.now();
    
    return data;
  } catch (error) {
    console.error("Error en getPosts:", error);
    throw error;
  }
}

// Función para obtener un post por su ID
export async function getPostById(id) {
  // Si tenemos el post específico en caché y es válido, lo devolvemos
  if (cache.postDetails[id] && cache.isValid()) {
    console.log(`Usando post ${id} desde caché`);
    return cache.postDetails[id];
  }

  try {
    const response = await fetch(`https://jsonplaceholder.typicode.com/posts/${id}`);
    
    if (!response.ok) {
      throw new Error(`Error HTTP: ${response.status}`);
    }
    
    const data = await response.json();
    
    // Actualizamos la caché
    cache.postDetails[id] = data;
    cache.timestamp = Date.now();
    
    return data;
  } catch (error) {
    console.error(`Error en getPostById(${id}):`, error);
    throw error;
  }
}
```

### 4.2 Actualizar los componentes para usar el servicio

Actualiza `src/components/PostList.jsx`:

```jsx
import { useState, useEffect } from 'react';
import { getPosts } from '../services/postService';

function PostList({ onSelectPost }) {
  const [posts, setPosts] = useState([]);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    const fetchPosts = async () => {
      try {
        // Ahora usamos el servicio en lugar de hacer la petición directamente
        const data = await getPosts();
        setPosts(data);
      } catch (error) {
        console.error("Error al cargar posts:", error);
      } finally {
        setLoading(false);
      }
    };
    
    fetchPosts();
  }, []);

  if (loading) return <p>Cargando posts...</p>;

  return (
    <div className="post-list">
      <h2>Listado de Posts</h2>
      {posts.map(post => (
        <div 
          key={post.id} 
          className="post-card"
          onClick={() => onSelectPost(post.id)}
        >
          <h3>{post.title}</h3>
          <p>{post.body.substring(0, 100)}...</p>
        </div>
      ))}
    </div>
  );
}

export default PostList;
```

Actualiza `src/components/PostDetail.jsx`:

```jsx
import { useState, useEffect } from 'react';
import { getPostById } from '../services/postService';

function PostDetail({ id, onBack }) {
  const [post, setPost] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    const fetchPost = async () => {
      try {
        // Ahora usamos el servicio
        const data = await getPostById(id);
        setPost(data);
      } catch (error) {
        console.error(`Error al cargar post ${id}:`, error);
      } finally {
        setLoading(false);
      }
    };
    
    fetchPost();
  }, [id]);

  if (loading) return <p>Cargando detalles del post...</p>;
  if (!post) return <p>Post no encontrado</p>;

  return (
    <div className="post-detail">
      <h2>{post.title}</h2>
      <p>{post.body}</p>
      <p><small>Post ID: {post.id} - Usuario ID: {post.userId}</small></p>
      <button onClick={onBack}>Volver a la lista</button>
    </div>
  );
}

export default PostDetail;
```

## 💅 Paso 5: Añadir estilos básicos

### 5.1 Crear estilos para la aplicación

Actualiza el archivo `src/App.css`:

```css
/* Estilos generales */
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

body {
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  line-height: 1.6;
  color: #333;
  background-color: #f9f9f9;
}

.app {
  max-width: 800px;
  margin: 0 auto;
  padding: 20px;
}

/* Estilos del encabezado */
header {
  margin-bottom: 30px;
  padding-bottom: 10px;
  border-bottom: 1px solid #eaeaea;
}

header h1 {
  color: #0077cc;
}

/* Estilos para la lista de posts */
.post-list {
  display: flex;
  flex-direction: column;
  gap: 15px;
}

.post-card {
  border: 1px solid #ddd;
  padding: 15px;
  border-radius: 8px;
  cursor: pointer;
  transition: transform 0.2s, box-shadow 0.2s;
  background-color: white;
}

.post-card:hover {
  transform: translateY(-3px);
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

.post-card h3 {
  color: #0077cc;
  margin-bottom: 8px;
}

/* Estilos para los detalles del post */
.post-detail {
  background-color: white;
  border-radius: 8px;
  padding: 25px;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.05);
}

.post-detail h2 {
  color: #0077cc;
  margin-bottom: 15px;
}

.post-detail p {
  margin-bottom: 15px;
}

/* Estilos para botones */
button {
  padding: 8px 16px;
  background-color: #0077cc;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 16px;
  transition: background-color 0.2s;
  margin-top: 10px;
}

button:hover {
  background-color: #005fa3;
}
```

## 📊 Paso 6: Probar la aplicación

Ahora podemos probar nuestra aplicación y ver cómo funciona el sistema de caché.

1. Inicia la aplicación:
```bash
npm run dev
```

2. Abre la consola del navegador (F12 o click derecho > Inspeccionar)
3. Navega por la aplicación:
   - Carga inicial: Verás la petición a la API
   - Selecciona un post: Verás la petición para ese post específico
   - Vuelve a la lista: Verás el mensaje "Usando posts desde caché"
   - Selecciona el mismo post de nuevo: Verás "Usando post X desde caché"

## 🔄 Paso 7: Control de versiones con Git

### 7.1 Inicializar repositorio Git

```bash
# Inicializar repositorio Git
git init

# Añadir todos los archivos al staging
git add .

# Hacer el primer commit
git commit -m "Implementación inicial: consumo de API con caché"
```

### 7.2 Crear un repositorio en GitHub (opcional)

1. Ve a [GitHub](https://github.com) y crea una cuenta si no tienes una
2. Crea un nuevo repositorio (botón "+" en la esquina superior derecha)
3. Sigue las instrucciones para subir tu repositorio local:

```bash
git remote add origin https://github.com/TU_USUARIO/TU_REPOSITORIO.git
git branch -M main
git push -u origin main
```

## 🚀 Paso 8: Desplegar en Vercel

### 8.1 Preparar la aplicación para producción

```bash
# Construir la versión de producción
npm run build
```

Esto creará una carpeta `dist/` con los archivos optimizados para producción.

### 8.2 Desplegar en Vercel

1. Crea una cuenta en [Vercel](https://vercel.com) si no tienes una
2. Instala la CLI de Vercel:

```bash
npm install -g vercel
```

3. Inicia sesión desde la terminal:

```bash
vercel login
```

4. Despliega la aplicación:

```bash
vercel
```

5. Sigue las instrucciones en pantalla:
   - Confirma el directorio del proyecto
   - Configura el proyecto (puedes usar las opciones por defecto)
   - Si quieres, puedes vincular a un proyecto existente

¡Y listo! Vercel te proporcionará una URL donde tu aplicación estará desplegada.

## 🎉 Conclusión

¡Felicidades! Has creado una aplicación React que:

1. Consume una API externa (JSONPlaceholder)
2. Implementa un sistema de caché para optimizar las peticiones
3. Tiene una interfaz atractiva y responsive
4. Está desplegada en la nube y accesible desde cualquier dispositivo

## 📚 Recursos adicionales

- [Documentación oficial de React](https://reactjs.org/docs/getting-started.html)
- [Documentación de Vite](https://vitejs.dev/guide/)
- [JSONPlaceholder API](https://jsonplaceholder.typicode.com/)
- [Vercel Documentation](https://vercel.com/docs)
