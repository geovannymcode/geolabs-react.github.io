# 🚀 Taller de React: Mi Primera App con API

**Duración:** < 2 horas  
**Nivel:** Principiantes absolutos  
**Resultado:** Una aplicación React funcional desplegada en internet

---

## 📚 **¿Qué es React?**

React es una **librería de JavaScript** creada por Facebook para construir interfaces de usuario (UI). Piensa en React como una caja de herramientas que te ayuda a crear páginas web **interactivas** y **dinámicas**.

### **¿Por qué React es popular?**

- ✅ **Componentes reutilizables**: Como bloques de LEGO que puedes usar una y otra vez
- ✅ **Fácil de aprender**: Si sabes JavaScript básico, puedes aprender React
- ✅ **Muy demandado**: Las empresas más grandes del mundo lo usan
- ✅ **Comunidad gigante**: Millones de desarrolladores lo usan

### **¿Qué vamos a construir hoy?**

Una aplicación que muestre posts de un blog, consumiendo datos de una API real. Al final tendrás:

- 📱 Una app React funcionando
- 🌐 Publicada en internet (gratis)
- 📂 Código en GitHub
- 🧠 Conocimiento de React hooks

---

## 🛠️ **Requisitos Previos**

### **Software necesario:**

1. **Node.js** (versión 16 o superior)
   - Descargar de: https://nodejs.org/
   - Verificar instalación: `node --version`

2. **Editor de código**
   - VS Code (recomendado): https://code.visualstudio.com/
   - O cualquier editor que prefieras

3. **Git**
   - Descargar de: https://git-scm.com/
   - Verificar instalación: `git --version`

### **Cuentas necesarias:**

- **GitHub**: https://github.com/ (para guardar código)
- **Vercel**: https://vercel.com/ (para publicar la app)

---

## 🏗️ **PARTE 1: Setup del Proyecto**

### **Paso 1.1: Crear el proyecto React**

Abre tu terminal o consola de comandos y ejecuta:

```bash
# Crear un nuevo proyecto React
npx create-react-app posts-app

# Entrar a la carpeta del proyecto
cd posts-app

# Abrir VS Code (opcional)
code .
```

**🤔 ¿Qué acabamos de hacer?**

- `npx create-react-app` es un comando que crea un proyecto React completo
- `posts-app` es el nombre de nuestra aplicación
- Se crearon automáticamente todos los archivos necesarios

### **Paso 1.2: Ejecutar el proyecto**

```bash
# Iniciar el servidor de desarrollo
npm start
```

**🎉 ¡Deberías ver una página con el logo de React girando!**

- La página se abre automáticamente en: http://localhost:3000
- Cada vez que guardes cambios, la página se actualiza automáticamente

### **Paso 1.3: Explorar la estructura del proyecto**

```
posts-app/
├── public/
│   └── index.html          # Página principal
├── src/
│   ├── App.js             # Componente principal
│   ├── App.css            # Estilos de la app
│   ├── index.js           # Punto de entrada
│   └── ...otros archivos
├── package.json           # Configuración del proyecto
└── README.md             # Documentación
```

**🎯 Archivos importantes que modificaremos:**

- `src/App.js` - Componente principal
- `src/App.css` - Estilos

---

## 🧠 **PARTE 2: Conceptos Fundamentales de React**

### **2.1: ¿Qué es un Componente?**

Un **componente** es como una función que retorna HTML. Es un pedazo de interfaz reutilizable.

```javascript
// Ejemplo simple de componente
function Saludo() {
  return <h1>¡Hola, mundo!</h1>;
}
```

**🎯 Características importantes:**

- Los componentes **siempre empiezan con mayúscula**
- Retornan **JSX** (HTML dentro de JavaScript)
- Son **reutilizables**

### **2.2: ¿Qué es JSX?**

JSX es una sintaxis que nos permite escribir HTML dentro de JavaScript:

```javascript
// En lugar de escribir HTML puro:
// <h1>Hola</h1>

// En React escribimos JSX:
const elemento = <h1>¡Hola desde React!</h1>;
```

### **2.3: Hook useState**

`useState` nos permite **guardar y cambiar datos** en nuestro componente.

```javascript
import { useState } from 'react';

function Contador() {
  // [valor actual, función para cambiar el valor]
  const [numero, setNumero] = useState(0);

  return (
    <div>
      <p>Contador: {numero}</p>
      <button onClick={() => setNumero(numero + 1)}>
        Aumentar
      </button>
    </div>
  );
}
```

**🎯 Puntos clave:**

- `useState(0)` - el valor inicial es 0
- `numero` - variable que contiene el valor actual
- `setNumero` - función para cambiar el valor
- Cuando el estado cambia, React **re-renderiza** el componente

### **2.4: Hook useEffect**

`useEffect` nos permite ejecutar código cuando **algo específico sucede**.

```javascript
import { useState, useEffect } from 'react';

function MiComponente() {
  const [datos, setDatos] = useState([]);

  // Se ejecuta cuando el componente se monta
  useEffect(() => {
    console.log('¡El componente se montó!');
    // Aquí podemos hacer llamadas a APIs
  }, []); // Array vacío = solo una vez

  return <div>Mi componente</div>;
}
```

**🎯 Casos de uso comunes:**

- Llamadas a APIs
- Configurar timers
- Suscribirse a eventos

---

## 🏗️ **PARTE 3: Construyendo Nuestra App**

### **Paso 3.1: Crear la estructura de carpetas**

Dentro de `src/`, crea estas carpetas y archivos:

```
src/
├── components/
│   ├── PostCard.js
│   └── PostsList.js
├── services/
│   └── postService.js
├── App.js
├── App.css
└── index.js
```

**En VS Code:**

1. Click derecho en `src`
2. "New Folder" → `components`
3. "New Folder" → `services`
4. Click derecho en `components` → "New File" → `PostCard.js`
5. Repite para los demás archivos

### **Paso 3.2: Crear el servicio para la API (10 min)**

**📁 Archivo: `src/services/postService.js`**

```javascript
// src/services/postService.js

const API_BASE_URL = 'https://jsonplaceholder.typicode.com';

// 🔥 Función para obtener todos los posts
export const getAllPosts = async () => {
  try {
    // Hacemos la petición HTTP
    const response = await fetch(`${API_BASE_URL}/posts`);
    
    // Verificamos si la respuesta es exitosa
    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }
    
    // Convertimos la respuesta a JSON
    const posts = await response.json();
    return posts;
  } catch (error) {
    console.error('Error fetching posts:', error);
    throw error;
  }
};
```

**🤔 ¿Qué está pasando aquí?**

- `fetch()` hace una petición HTTP a la API
- `async/await` maneja código asíncrono de forma más limpia
- `try/catch` maneja errores si algo sale mal
- `export` nos permite usar esta función en otros archivos

**💡 Prueba la API:**
Abre en tu navegador: https://jsonplaceholder.typicode.com/posts

### **Paso 3.3: Crear el componente PostCard (10 min)**

**📁 Archivo: `src/components/PostCard.js`**

```javascript
// src/components/PostCard.js
import React from 'react';

// 🎯 Componente que recibe un post y lo muestra
const PostCard = ({ post }) => {
  return (
    <div className="post-card">
      {/* Encabezado con ID y usuario */}
      <div className="post-header">
        <span className="post-id">#{post.id}</span>
        <span className="post-user">Usuario {post.userId}</span>
      </div>
      
      {/* Título del post */}
      <h3 className="post-title">{post.title}</h3>
      
      {/* Cuerpo del post */}
      <p className="post-body">{post.body}</p>
    </div>
  );
};

export default PostCard;
```

**🤔 ¿Qué está pasando aquí?**

- `{ post }` - destructuring de props, recibimos un objeto post
- `{post.title}` - mostramos dinámicamente el título del post
- `className` - en React usamos className en lugar de class
- `export default` - exportamos el componente para usarlo en otros archivos

### **Paso 3.4: Crear el componente PostsList (15 min)**

**📁 Archivo: `src/components/PostsList.js`**

```javascript
// src/components/PostsList.js
import React, { useState, useEffect } from 'react';
import PostCard from './PostCard';
import { getAllPosts } from '../services/postService';

const PostsList = () => {
  // 🎯 Estados para manejar diferentes situaciones
  const [posts, setPosts] = useState([]);           // Array de posts
  const [loading, setLoading] = useState(true);     // ¿Está cargando?
  const [error, setError] = useState(null);         // ¿Hay algún error?

  // 🔥 useEffect - Se ejecuta cuando el componente se monta
  useEffect(() => {
    // Función para obtener los posts
    const fetchPosts = async () => {
      try {
        setLoading(true);           // Empezamos a cargar
        setError(null);             // Limpiamos errores previos
        
        // Llamamos a nuestra API
        const postsData = await getAllPosts();
        
        // Guardamos los posts en el estado
        setPosts(postsData);
        
      } catch (err) {
        // Si hay error, lo guardamos
        setError('Error al cargar los posts. Inténtalo de nuevo.');
        console.error('Error:', err);
        
      } finally {
        // Siempre quitamos el loading
        setLoading(false);
      }
    };

    // Ejecutamos la función
    fetchPosts();
    
    // [] = solo se ejecuta una vez al montar el componente
  }, []);

  // 🎯 Función para recargar los posts
  const handleRefresh = () => {
    setLoading(true);
    setError(null);
    
    getAllPosts()
      .then(postsData => setPosts(postsData))
      .catch(err => setError('Error al recargar los posts'))
      .finally(() => setLoading(false));
  };

  // 🎨 Si está cargando, mostramos un spinner
  if (loading) {
    return (
      <div className="loading-container">
        <div className="loading-spinner"></div>
        <p>Cargando posts...</p>
      </div>
    );
  }

  // 🎨 Si hay error, mostramos mensaje de error
  if (error) {
    return (
      <div className="error-container">
        <h3>¡Oops! Algo salió mal</h3>
        <p>{error}</p>
        <button onClick={handleRefresh} className="retry-button">
          Intentar de nuevo
        </button>
      </div>
    );
  }

  // 🎨 Si todo está bien, mostramos los posts
  return (
    <div className="posts-container">
      {/* Header con información */}
      <div className="posts-header">
        <h2>📝 Posts del Blog</h2>
        <p>Total de posts: {posts.length}</p>
        <button onClick={handleRefresh} className="refresh-button">
          🔄 Actualizar
        </button>
      </div>

      {/* Grid con todos los posts */}
      <div className="posts-grid">
        {posts.map(post => (
          <PostCard 
            key={post.id}    // Key único para cada elemento
            post={post}      // Pasamos el post como prop
          />
        ))}
      </div>
    </div>
  );
};

export default PostsList;
```

**🤔 ¿Qué acabamos de aprender?**

- **Múltiples estados**: loading, error, datos
- **useEffect con array vacío**: se ejecuta solo una vez
- **Render condicional**: mostramos diferentes cosas según el estado
- **map()**: convertimos un array de datos en componentes JSX
- **Props**: pasamos datos del padre al hijo

---

## 🎨 **PARTE 4: Actualizando la App Principal (10 min)**

### **Paso 4.1: Actualizar App.js**

**📁 Archivo: `src/App.js`**

```javascript
// src/App.js
import React from 'react';
import PostsList from './components/PostsList';
import './App.css';

function App() {
  return (
    <div className="App">
      {/* Header de la aplicación */}
      <header className="app-header">
        <h1>🚀 Mi Primera App React</h1>
        <p>Consumiendo API de JSONPlaceholder</p>
      </header>

      {/* Contenido principal */}
      <main className="app-main">
        <PostsList />
      </main>

      {/* Footer */}
      <footer className="app-footer">
        <p>
          Hecho con ❤️ en React | 
          API: <a 
            href="https://jsonplaceholder.typicode.com" 
            target="_blank" 
            rel="noopener noreferrer"
          >
            JSONPlaceholder
          </a>
        </p>
      </footer>
    </div>
  );
}

export default App;
```

### **Paso 4.2: Actualizar los estilos**

**📁 Archivo: `src/App.css`**

```css
/* src/App.css */

/* 🎨 Reset básico */
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', 'Roboto', sans-serif;
  background-color: #f5f5f5;
  color: #333;
  line-height: 1.6;
}

/* 🎨 Layout principal */
.App {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
}

/* 🎨 Header */
.app-header {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
  text-align: center;
  padding: 2rem 1rem;
  box-shadow: 0 2px 10px rgba(0,0,0,0.1);
}

.app-header h1 {
  font-size: 2.5rem;
  margin-bottom: 0.5rem;
  font-weight: 700;
}

/* 🎨 Main content */
.app-main {
  flex: 1;
  padding: 2rem 1rem;
  max-width: 1200px;
  margin: 0 auto;
  width: 100%;
}

/* 🎨 Posts header */
.posts-header {
  text-align: center;
  margin-bottom: 2rem;
}

.posts-header h2 {
  color: #333;
  margin-bottom: 0.5rem;
  font-size: 2rem;
}

/* 🎨 Botones */
.refresh-button, .retry-button {
  background: #667eea;
  color: white;
  border: none;
  padding: 0.75rem 1.5rem;
  border-radius: 8px;
  cursor: pointer;
  font-size: 1rem;
  font-weight: 600;
  transition: all 0.3s ease;
  margin: 1rem;
}

.refresh-button:hover, .retry-button:hover {
  background: #5a67d8;
  transform: translateY(-2px);
}

/* 🎨 Grid de posts */
.posts-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(350px, 1fr));
  gap: 1.5rem;
  margin-top: 2rem;
}

/* 🎨 Tarjeta de post */
.post-card {
  background: white;
  border-radius: 12px;
  padding: 1.5rem;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  transition: all 0.3s ease;
  border: 1px solid #e2e8f0;
}

.post-card:hover {
  transform: translateY(-5px);
  box-shadow: 0 8px 25px rgba(0, 0, 0, 0.15);
}

.post-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 1rem;
  padding-bottom: 0.5rem;
  border-bottom: 2px solid #f7fafc;
}

.post-id {
  background: #667eea;
  color: white;
  padding: 0.25rem 0.75rem;
  border-radius: 20px;
  font-size: 0.875rem;
  font-weight: 600;
}

.post-user {
  color: #718096;
  font-size: 0.875rem;
  font-weight: 500;
}

.post-title {
  color: #2d3748;
  margin-bottom: 1rem;
  font-size: 1.25rem;
  font-weight: 600;
  line-height: 1.4;
  text-transform: capitalize;
}

.post-body {
  color: #4a5568;
  line-height: 1.6;
}

/* 🎨 Loading spinner */
.loading-container {
  text-align: center;
  padding: 3rem;
}

.loading-spinner {
  width: 50px;
  height: 50px;
  border: 4px solid #f3f3f3;
  border-top: 4px solid #667eea;
  border-radius: 50%;
  animation: spin 1s linear infinite;
  margin: 0 auto 1rem;
}

@keyframes spin {
  0% { transform: rotate(0deg); }
  100% { transform: rotate(360deg); }
}

/* 🎨 Error container */
.error-container {
  text-align: center;
  padding: 3rem;
  background: #fed7d7;
  border: 1px solid #feb2b2;
  border-radius: 12px;
  margin: 2rem auto;
  max-width: 500px;
}

.error-container h3 {
  color: #c53030;
  margin-bottom: 1rem;
}

/* 🎨 Footer */
.app-footer {
  background: #2d3748;
  color: white;
  text-align: center;
  padding: 1.5rem;
  margin-top: auto;
}

.app-footer a {
  color: #667eea;
  text-decoration: none;
}

/* 🎨 Responsive */
@media (max-width: 768px) {
  .posts-grid {
    grid-template-columns: 1fr;
    gap: 1rem;
  }
  
  .app-main {
    padding: 1rem;
  }
  
  .app-header h1 {
    font-size: 2rem;
  }
}
```

---

## 🎉 **PARTE 5: Probar Nuestra App (5 min)**

### **Paso 5.1: Verificar que todo funciona**

En la terminal, asegúrate de que el servidor sigue corriendo:

```bash
npm start
```

**🎯 Deberías ver:**

- ✅ Header con el título de la app
- ✅ Loading spinner mientras cargan los posts
- ✅ Grid con 100 posts de la API
- ✅ Botón de actualizar funcionando
- ✅ Diseño responsive

**🐛 Si algo no funciona:**

- Verifica que todos los archivos estén guardados
- Revisa la consola del navegador (F12)
- Asegúrate de que no hay errores de sintaxis

---

## 📦 **PARTE 6: Git y GitHub (15 min)**

### **Paso 6.1: Configurar Git (solo primera vez)**

```bash
# Configurar tu identidad (cambiar por tus datos)
git config --global user.name "Tu Nombre"
git config --global user.email "tu@email.com"

# Verificar configuración
git config --list
```

### **Paso 6.2: Inicializar repositorio**

```bash
# Navegar a la carpeta del proyecto
cd posts-app

# Inicializar Git (si no está inicializado)
git init

# Ver el estado actual
git status
```

### **Paso 6.3: Hacer el primer commit**

```bash
# Agregar todos los archivos
git add .

# Crear el commit
git commit -m "🚀 Primera versión de la app React"

# Verificar el commit
git log --oneline
```

### **Paso 6.4: Subir a GitHub**

1. **Crear repositorio en GitHub:**

   - Ve a https://github.com/
   - Click en "New repository"
   - Nombre: `posts-app`
   - Descripción: `Mi primera aplicación React`
   - Público o privado (tu elección)
   - **NO** marcar "Initialize with README"
   - Click "Create repository"

2. **Conectar y subir:**

```bash
# Conectar con GitHub (cambiar por tu URL)
git remote add origin https://github.com/TU_USUARIO/posts-app.git

# Subir el código
git branch -M main
git push -u origin main
```

**🎉 ¡Tu código ahora está en GitHub!**

---

## 🚀 **PARTE 7: Desplegar en Vercel (15 min)**

### **Paso 7.1: Preparar el proyecto**

```bash
# Crear una versión optimizada para producción
npm run build
```

Este comando crea una carpeta `build/` con la versión optimizada.

### **Paso 7.2: Desplegar con Vercel**

**Opción A: Desde la web (Recomendado)**

1. Ve a https://vercel.com/
2. Click "Sign up" o "Login"
3. Elegir "Continue with GitHub"
4. Autorizar Vercel en GitHub
5. Click "New Project"
6. Buscar tu repositorio `posts-app`
7. Click "Import"
8. **Configuración automática** (React es detectado automáticamente)
9. Click "Deploy"
10. **¡Esperar 1-2 minutos!**

**🎉 ¡Tu app está en vivo en internet!**

### **Paso 7.3: Verificar el despliegue**

Vercel te dará una URL como: `https://posts-app-xxx.vercel.app`

**🎯 Prueba que funciona:**

- ✅ La app carga correctamente
- ✅ Los posts se muestran
- ✅ Los botones funcionan
- ✅ Es responsive en móvil

---

## 🎯 **¿Qué Aprendimos Hoy?**

### **Conceptos de React:**

- ✅ **Componentes**: Bloques reutilizables de UI
- ✅ **JSX**: HTML dentro de JavaScript
- ✅ **useState**: Para manejar estado que cambia
- ✅ **useEffect**: Para efectos secundarios (APIs)
- ✅ **Props**: Pasar datos entre componentes

### **Conceptos de desarrollo:**

- ✅ **API consumption**: Hacer peticiones HTTP
- ✅ **async/await**: Manejar código asíncrono
- ✅ **Error handling**: Manejar errores gracefully
- ✅ **Git**: Control de versiones
- ✅ **Deploy**: Publicar en internet

### **Resultado final:**

- 🎉 **Una app React funcional**
- 🌐 **Publicada en internet**
- 📂 **Código en GitHub**
- 🧠 **Fundamentos de React**

---

## 🚀 **Próximos Pasos (Para Casa)**

### **Mejoras que puedes hacer:**

1. **Agregar búsqueda:**

```javascript
const [searchTerm, setSearchTerm] = useState('');
const filteredPosts = posts.filter(post => 
  post.title.toLowerCase().includes(searchTerm.toLowerCase())
);
```

1. **Agregar paginación:**
      - Mostrar solo 10 posts por página
      - Botones "Anterior" y "Siguiente"

2. **Agregar filtros:**
      - Filtrar por usuario
      - Ordenar por título o ID

3. **Mejorar el diseño:**
      - Agregar modo oscuro
      - Animaciones con CSS
      - Mejores colores

4. **Agregar más funcionalidades:**
      - Ver detalles de un post
      - Agregar comentarios
      - Crear nuevos posts

### **Recursos para seguir aprendiendo:**

- 📚 **Documentación oficial**: https://reactjs.org/
- 🎥 **Videos**: Buscar "React tutorials" en YouTube
- 🏗️ **Proyectos**: Hacer más apps pequeñas
- 👥 **Comunidad**: Unirse a grupos de React en Discord/Slack

---

## 🔧 **Resolución de Problemas Comunes**

### **"npm no es reconocido"**

- Instalar Node.js desde https://nodejs.org/
- Reiniciar la terminal

### **"Module not found"**

- Verificar que estás en la carpeta correcta: `cd posts-app`
- Reinstalar dependencias: `rm -rf node_modules && npm install`

### **Error de CORS**

- La API de JSONPlaceholder permite CORS, no debería haber problemas
- Si persiste, verificar la URL de la API

### **Error al hacer push a GitHub**

- Verificar que el repositorio existe en GitHub
- Verificar credenciales: `git config --list`
- Verificar la URL: `git remote -v`

### **Error en Vercel**

- Verificar que el proyecto compila localmente: `npm run build`
- Revisar los logs en el dashboard de Vercel
- Asegurarse de que el repositorio está conectado

---

## 🎉 **¡Felicitaciones!**

Has creado tu primera aplicación React que:

- ✅ Consume una API real
- ✅ Maneja estados complejos
- ✅ Tiene buen diseño
- ✅ Está publicada en internet
- ✅ Tiene el código en GitHub

**¡Ahora eres oficialmente un desarrollador React!** 🚀

¿Listo para el siguiente desafío? ¡Sigue practicando y construyendo más proyectos!