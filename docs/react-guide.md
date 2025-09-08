# 🚀 Introducción a React - Guía Detallada para Principiantes y Nivel Intermedio

## 📋 Índice
1. [¿Qué es React?](#qué-es-react)
2. [Primer ejemplo: Hola Mundo](#primer-ejemplo-hola-mundo)
3. [JSX: JavaScript + XML](#jsx-javascript--xml)
4. [Creando tu primer proyecto React](#creando-tu-primer-proyecto-react)
5. [Componentes: Los bloques de construcción](#componentes-los-bloques-de-construcción)
6. [Estado: Haciendo tu app interactiva](#estado-haciendo-tu-app-interactiva)
7. [Fragmentos: Agrupando elementos](#fragmentos-agrupando-elementos)
8. [Estilos en React](#estilos-en-react)
9. [Resumen y próximos pasos](#resumen-y-próximos-pasos)

---

## 🤔 ¿Qué es React?

### Definición técnica completa

React es una **biblioteca JavaScript declarativa, eficiente y flexible** para construir interfaces de usuario. Desarrollada por Facebook (ahora Meta) en 2013, React permite construir UIs complejas a partir de pequeñas piezas de código encapsuladas llamadas "componentes".

A nivel técnico, React implementa:

- **Virtual DOM**: Una representación en memoria del DOM real, que React utiliza para optimizar las actualizaciones de la interfaz.
- **Sistema de reconciliación (Reconciler)**: Algoritmo (conocido como "React Fiber" desde React 16) que determina qué partes del DOM necesitan actualizarse.
- **Paradigma declarativo**: Describe el "qué" debe mostrarse, no el "cómo" actualizarlo.
- **Flujo de datos unidireccional**: La información fluye en una sola dirección, facilitando el seguimiento de cambios.

### 🎯 Comparación con otros frameworks

| Característica | React | Angular | Vue | Vanilla JS |
|----------------|-------|---------|-----|------------|
| **Tipo** | Biblioteca | Framework completo | Framework progresivo | Lenguaje base |
| **Curva de aprendizaje** | Moderada | Pronunciada | Suave | Variable |
| **Tamaño (gzipped)** | ~40KB | ~143KB | ~33KB | 0KB |
| **Enfoque** | Solo UI, ecosistema para lo demás | Todo incluido | Adaptable | Manual |
| **Renderizado** | Virtual DOM | Real DOM + Dirty Checking | Virtual DOM | Real DOM |
| **Datos** | Unidireccional | Bidireccional | Bidireccional o unidireccional | Manual |

### 🔍 El Virtual DOM explicado

```
Cuando actualizas un componente en React:

1. Se genera un nuevo árbol Virtual DOM
   ┌───────────────────────┐
   │ Virtual DOM (Nuevo)   │
   │  ┌──────┐             │
   │  │ Div  │             │
   │  │  ┌───┴──┐   ┌────┐ │
   │  │  │ h1   │   │ p  │ │
   │  │  └──────┘   └────┘ │
   │  └──────┘             │
   └───────────────────────┘
           │
           ▼
2. Se compara con el Virtual DOM anterior
   ┌─────────────────────────┐
   │ Algoritmo de Diffing    │
   │ "¿Qué cambió realmente?"|
   └─────────────────────────┘
           │
           ▼
3. Solo se actualiza lo necesario en el DOM real
   ┌───────────────────────┐
   │ DOM Real              │
   │ (Solo se actualiza    │
   │  el elemento <p>)     │
   └───────────────────────┘
```

#### Ventajas técnicas:
- **Actualización selectiva**: React solo modifica los nodos del DOM que realmente cambiaron.
- **Actualizaciones por lotes**: Agrupa múltiples cambios en una sola actualización.
- **Optimización automática**: Prioriza actualizaciones críticas con su algoritmo de conciliación.

### 🚫 Limitaciones de React

1. **Biblioteca, no framework**: React solo maneja la vista (UI). Necesitarás otras bibliotecas para:
      - Manejo de estado global (Redux, Context API, Zustand)
      - Enrutamiento (React Router)
      - Llamadas a API (Axios, fetch)

2. **JSX requiere transpilación**: El código no funciona directamente en navegadores sin compilación previa.

3. **Empaquetado requerido**: Necesitas herramientas como Webpack, Vite o Create React App.

4. **Ciclo de actualizaciones rápido**: La API y mejores prácticas evolucionan constantemente.

### ⚠️ Errores comunes para principiantes

1. **Modificar estado directamente**: 
   ```jsx
   // ❌ INCORRECTO
   this.state.count = this.state.count + 1;
   
   // ✅ CORRECTO
   this.setState({ count: this.state.count + 1 });
   // o en Hooks:
   setCount(prevCount => prevCount + 1);
   ```

2. **No entender la naturaleza asíncrona de setState**:
   ```jsx
   // ❌ PROBLEMA
   setState({ count: count + 1 });
   console.log(count); // Muestra el valor antiguo, no el actualizado
   
   // ✅ SOLUCIÓN
   setState({ count: count + 1 }, () => {
     console.log(count); // Callback que se ejecuta después de la actualización
   });
   ```

---

## 👋 Primer ejemplo: Hola Mundo

### Código desglosado a nivel técnico

```jsx
// 1. Importar dependencias (normalmente sería así)
import React from 'react';
import ReactDOM from 'react-dom/client';

// 2. Encontrar el elemento contenedor
const divRoot = document.querySelector('#root');

// 3. Crear la raíz React y renderizar
const root = ReactDOM.createRoot(divRoot);
root.render(<h1>Hola Mundo</h1>);
```

### 🔬 Análisis técnico detallado

1. **ReactDOM.createRoot vs ReactDOM.render**
      - React 18 introdujo `createRoot` que reemplaza al antiguo `render`
      - Permite el modo concurrente (Concurrent Mode)
      - Mejora el rendimiento y habilita nuevas funcionalidades como Suspense

2. **Qué ocurre realmente durante el renderizado**:
   ```
   1. JSX <h1>Hola Mundo</h1> se transpila a:
      React.createElement('h1', null, 'Hola Mundo')
   
   2. createElement crea un objeto que describe el elemento:
      {
        type: 'h1',
        props: {
          children: 'Hola Mundo'
        },
        key: null,
        ref: null
      }
   
   3. Este objeto alimenta el algoritmo de reconciliación
   
   4. React crea/actualiza el DOM real:
      document.createElement('h1')
      textNode = document.createTextNode('Hola Mundo')
      h1.appendChild(textNode)
      container.appendChild(h1)
   ```

### 📦 HTML completo con explicaciones

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8" />
    <title>Mi App React</title>
</head>
<body>
    <!-- React necesita un nodo DOM para "montarse" -->
    <div id="root"></div>
    
    <!-- Scripts de React (versión de desarrollo) -->
    <script src="https://unpkg.com/react@18/umd/react.development.js"></script>
    <script src="https://unpkg.com/react-dom@18/umd/react-dom.development.js"></script>
    
    <!-- Babel para transpilación en el navegador (solo para desarrollo) -->
    <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
    
    <!-- Nuestro código React con la directiva para Babel -->
    <script type="text/babel">
        // 1. Encontrar el contenedor
        const container = document.getElementById('root');
        
        // 2. Crear una raíz React (React 18+)
        const root = ReactDOM.createRoot(container);
        
        // 3. Definir componente funcional
        function App() {
            return <h1>Hola Mundo desde React</h1>;
        }
        
        // 4. Renderizar el componente
        root.render(<App />);
    </script>
</body>
</html>
```

### ⚠️ Limitaciones de este enfoque

1. **Solo para experimentación**: Este método de cargar React directamente en HTML:
      - No utiliza módulos ES6 (import/export)
      - Transpila JSX en tiempo de ejecución (lento)
      - No tiene hot-reloading ni optimizaciones

2. **Rendimiento**: Babel en el navegador es significativamente más lento que la transpilación previa.

3. **Producción**: Para producción necesitarías:
      - Archivos de producción minificados (react.production.min.js)
      - Eliminar Babel del navegador
      - Pre-compilar tu código

### 🔄 Alternativas de renderizado

```jsx
// Opción 1: Antiguo método (React < 18)
ReactDOM.render(<App />, container);

// Opción 2: Modo estricto (ayuda a encontrar problemas)
root.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);

// Opción 3: Hidratar contenido renderizado en servidor
ReactDOM.hydrateRoot(container, <App />);
```

### 🌐 Modo estricto (StrictMode) explicado

React StrictMode:

- Identifica componentes con métodos de ciclo de vida inseguros
- Advierte sobre legacy API usage
- Detecta efectos secundarios inesperados ejecutando el ciclo de vida dos veces
- Verifica uso de legacy context API
- Advierte sobre API deprecated

---

## 🎨 JSX: JavaScript + XML

### Definición técnica completa

JSX (JavaScript XML) es una extensión de sintaxis para JavaScript que parece HTML pero con todo el poder de JavaScript. Técnicamente, JSX:

- Es azúcar sintáctico que se transpila a llamadas `React.createElement()`
- Permite escribir código declarativo que describe la UI
- Facilita la inclusión de lógica JavaScript y marcado en el mismo lugar
- No es un string ni HTML, sino que produce "elementos React"

### 🔄 El proceso de transpilación al detalle

```jsx
// JSX que escribes
const element = (
  <div className="container">
    <h1 className="title">Hola {nombre}</h1>
    <p>Bienvenido a React</p>
  </div>
);

// Lo que Babel convierte
const element = React.createElement(
  'div',
  { className: 'container' },
  React.createElement(
    'h1',
    { className: 'title' },
    'Hola ', 
    nombre
  ),
  React.createElement(
    'p',
    null,
    'Bienvenido a React'
  )
);

// El objeto que React procesa internamente
{
  type: 'div',
  props: {
    className: 'container',
    children: [
      {
        type: 'h1',
        props: {
          className: 'title',
          children: ['Hola ', nombre]
        }
      },
      {
        type: 'p',
        props: {
          children: 'Bienvenido a React'
        }
      }
    ]
  }
}
```

### 📋 Reglas completas de JSX con explicaciones

#### 1. **Elementos deben cerrarse**
```jsx
// ❌ INCORRECTO (en JSX)
<img src="foto.jpg">
<br>

// ✅ CORRECTO 
<img src="foto.jpg" />
<br />
```
*Por qué*: JSX sigue reglas más cercanas a XML que a HTML, requiriendo cierre de todas las etiquetas.

#### 2. **Atributos en camelCase**
```jsx
// ❌ INCORRECTO
<div class="container" tabindex="0" onclick={handleClick}>

// ✅ CORRECTO
<div className="container" tabIndex="0" onClick={handleClick}>
```
*Por qué*: JSX se convierte a objetos JavaScript donde las propiedades se escriben en camelCase.

#### 3. **Atributos específicos renombrados**
| HTML | JSX | Razón |
|------|-----|-------|
| `class` | `className` | `class` es palabra reservada en JS |
| `for` | `htmlFor` | `for` es palabra reservada en JS |
| `tabindex` | `tabIndex` | Consistencia con camelCase |
| `maxlength` | `maxLength` | Consistencia con camelCase |

#### 4. **Expresiones JavaScript en llaves `{}`**
```jsx
const nombre = "Juan";
const edad = 25;
const esAdmin = true;

return (
  <div>
    {/* Variables */}
    <h1>Hola {nombre}</h1>
    
    {/* Expresiones */}
    <p>En 5 años tendrás {edad + 5} años</p>
    
    {/* Operador ternario (if inline) */}
    <p>{esAdmin ? 'Eres administrador' : 'Usuario normal'}</p>
    
    {/* Llamadas a funciones */}
    <p>{formatearNombre(nombre)}</p>
    
    {/* Expresiones con operadores lógicos */}
    {esAdmin && <AdminPanel />}
  </div>
);
```

#### 5. **Comentarios en JSX**
```jsx
<div>
  {/* Este es un comentario JSX */}
  <h1>Título</h1>
  {
    // También funciona así
    // pero requiere las llaves
  }
</div>
```

#### 6. **Espacios en blanco**
JSX elimina espacios en blanco al inicio y final de línea, y colapsa espacios consecutivos en uno solo.

```jsx
// Estos dos son equivalentes:
<p>Hola     Mundo</p>
<p>Hola Mundo</p>

// Para preservar espacios:
<p>Hola{' '}Mundo</p>
```

### ⚠️ Limitaciones de JSX

1. **No es HTML válido**:
      - No todos los atributos HTML funcionan igual (`class` → `className`)
      - Requiere que todos los elementos se cierren (`<img />`, no `<img>`)
      - No permite múltiples elementos raíz sin un contenedor

2. **Requiere transpilación**:
      - No se ejecuta directamente en navegadores
      - Necesita herramientas como Babel o TypeScript

3. **Errores comunes**:
      - Devolver múltiples elementos sin envolverlos
      - Olvidar que `{}` en JSX evalúa expresiones, no sentencias
      - Problemas con el scope de `this` en funciones

4. **Diferencias de estilo en línea**:
   ```jsx
   // ❌ INCORRECTO (sintaxis CSS normal)
   <div style="color: red; font-size: 16px;"></div>
   
   // ✅ CORRECTO (objeto JavaScript)
   <div style={{ color: 'red', fontSize: '16px' }}></div>
   ```

### 🔄 JSX vs Alternativas

| Característica | JSX | Template Strings | React.createElement | Frameworks con templates |
|----------------|-----|------------------|---------------------|--------------------------|
| **Sintaxis** | Similar a HTML | JavaScript puro | JavaScript puro | Similar a HTML |
| **Tipado** | Verificable con TypeScript | Sin verificación de estructura | Verificable pero verboso | Varía según framework |
| **Legibilidad** | Alta para estructuras complejas | Baja para UI complejas | Muy baja para estructuras anidadas | Alta |
| **Curva aprendizaje** | Media (reglas específicas) | Baja | Baja pero tedioso | Varía |
| **Herramientas** | Requiere transpilación | No requiere procesamiento | No requiere procesamiento | Requiere compilación |

---

## 🛠️ Creando tu primer proyecto React

### Opciones de configuración comparadas

| Método | Ventajas | Desventajas | Mejor para |
|--------|----------|-------------|------------|
| **CDN (script tags)** | Sin instalación, rápido inicio | No para producción, sin módulos, sin HMR | Prototipos, demos |
| **Create React App** | Configuración cero, todo incluido | Pesado, difícil personalizar a fondo | Principiantes, apps medianas |
| **Vite** | Extremadamente rápido, ligero | Menos plugins que webpack | La mayoría de proyectos nuevos |
| **Next.js** | SSR, SSG, optimizaciones | Framework completo, más que React | Sitios con SEO, producción |
| **Manual (webpack)** | Control total, personalizable | Complejo, mucha configuración | Necesidades muy específicas |

### 📊 Comparación de tamaños y rendimiento

```
Tiempo de arranque en desarrollo (segundos, MacBook Pro M1):
- CDN: ~0.5s
- Vite: ~0.3s 
- Create React App: ~5s
- Next.js: ~2s
- Webpack manual: ~3s (depende de configuración)

Tamaño de build (app "Hola Mundo"):
- CRA: ~200KB (gzipped)
- Vite: ~80KB (gzipped)
- Next.js: ~100KB (gzipped) 
- Manual: varía según configuración
```

### 🚀 Vite: Configuración moderna recomendada

```bash
# Crear nuevo proyecto
npm create vite@latest my-react-app -- --template react

# Navegar al proyecto
cd my-react-app

# Instalar dependencias
npm install

# Iniciar servidor de desarrollo
npm run dev
```

### 📁 Estructura del proyecto explicada (Vite)

```
my-react-app/
├── node_modules/        # Dependencias instaladas
├── public/              # Archivos estáticos que no requieren procesamiento
│   └── vite.svg         # Favicon y otros recursos
├── src/                 # Código fuente de la aplicación
│   ├── assets/          # Imágenes, fuentes, etc. que pasan por build
│   ├── App.css          # Estilos del componente App
│   ├── App.jsx          # Componente principal
│   ├── index.css        # Estilos globales
│   └── main.jsx         # Punto de entrada de la aplicación
├── .eslintrc.cjs        # Configuración de ESLint
├── .gitignore           # Archivos ignorados por git
├── index.html           # Plantilla HTML base
├── package.json         # Dependencias y scripts
├── package-lock.json    # Versiones exactas de dependencias
└── vite.config.js       # Configuración de Vite
```

### 🔄 Proceso de compilación y bundling

El proceso de construcción de una app React implica varias transformaciones:

1. **Transpilación de JSX y ES6+**: 
      - JSX → `React.createElement()`
      - ES6+ → JavaScript compatible con navegadores

2. **Resolución de módulos**:
      - `import` y `export` → Código compatible con navegadores
      - Dependencias NPM → Incluidas en el bundle

3. **Optimización y minificación**:
      - Eliminación de código no utilizado (tree-shaking)
      - Minificación de JavaScript y CSS
      - Optimización de imágenes y otros recursos

4. **División de código (code splitting)**:
      - Separar código en chunks para carga bajo demanda
      - Utiliza `import()` dinámico o React.lazy()

### ⚠️ Problemas comunes y soluciones

1. **"Module not found"**:
   ```
   ERROR: Cannot find module 'react'
   ```
   *Solución*: `npm install react react-dom`

2. **"Unexpected token" en JSX**:
   ```
   SyntaxError: Unexpected token '<'
   ```
   *Solución*: Asegúrate de que Babel está configurado con el preset de React

3. **CORS al cargar recursos**:
   ```
   Access to fetch at 'http://...' has been blocked by CORS policy
   ```
   *Solución*: Configura proxies en vite.config.js o usa herramientas como CORS Anywhere

4. **Hot Reload no funciona**:
   *Solución*: Verifica que tu componente exporta por defecto o usa HMR explícitamente

### 📋 Buenas prácticas de estructura de proyecto

```
src/
├── assets/              # Recursos estáticos (imágenes, fuentes)
├── components/          # Componentes reutilizables
│   ├── Button/
│   │   ├── Button.jsx   # Componente
│   │   ├── Button.css   # Estilos (o .module.css)
│   │   ├── Button.test.jsx  # Tests
│   │   └── index.js     # Archivo de barril (re-exporta)
│   └── Card/
│       └── ...
├── hooks/               # Custom hooks
├── pages/ o views/      # Componentes de página o vistas
├── services/            # Lógica de servicios (API, auth)
├── utils/               # Funciones utilitarias
├── contexts/            # Contextos de React
├── store/ o state/      # Estado global (Redux, Zustand)
├── styles/              # Estilos globales
├── types/               # Definiciones de tipos (TypeScript)
├── constants/           # Valores constantes
├── App.jsx              # Componente raíz
└── main.jsx             # Punto de entrada
```

---

## 🧩 Componentes: Los bloques de construcción

### Definición técnica completa

En React, un componente es:
- Una función o clase que acepta props (propiedades) como entrada
- Retorna elementos React que describen lo que debe renderizarse en la UI
- Puede mantener estado interno y efectos secundarios
- Sigue un ciclo de vida específico controlado por React

Hay dos tipos principales de componentes en React:

### 🔄 Comparación detallada: Componentes funcionales vs. de clase

| Característica | Componentes funcionales | Componentes de clase |
|----------------|-------------------------|----------------------|
| **Sintaxis** | Función que retorna JSX | Clase que extiende React.Component |
| **Estado** | Hooks (useState, useReducer) | this.state y this.setState() |
| **Ciclo de vida** | useEffect | componentDidMount, componentDidUpdate, etc. |
| **Acceso a contexto** | useContext | contextType o Consumer |
| **Referencias** | useRef | createRef() o callback refs |
| **Manejo de props** | Desestructuración directa | this.props |
| **Optimización** | React.memo, useMemo, useCallback | PureComponent, shouldComponentUpdate |
| **Tendencia actual** | Recomendados (enfoque moderno) | Legacy (no recomendados para nuevo código) |

### 📊 Código comparativo

```jsx
// COMPONENTE FUNCIONAL (moderno)
import React, { useState, useEffect } from 'react';

function UserProfile({ userId }) {
  // Estado
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);
  
  // Efectos (ciclo de vida)
  useEffect(() => {
    const fetchUser = async () => {
      setLoading(true);
      try {
        const response = await fetch(`/api/users/${userId}`);
        const data = await response.json();
        setUser(data);
      } catch (error) {
        console.error("Error fetching user:", error);
      } finally {
        setLoading(false);
      }
    };
    
    fetchUser();
    
    // Limpieza al desmontar
    return () => {
      console.log("Componente desmontado");
    };
  }, [userId]); // Dependencias
  
  // Renderizado condicional
  if (loading) return <div>Cargando...</div>;
  if (!user) return <div>Usuario no encontrado</div>;
  
  return (
    <div className="user-profile">
      <h2>{user.name}</h2>
      <p>Email: {user.email}</p>
      <p>Role: {user.role}</p>
    </div>
  );
}

// MISMO COMPONENTE CON CLASES (legacy)
import React, { Component } from 'react';

class UserProfile extends Component {
  constructor(props) {
    super(props);
    this.state = {
      user: null,
      loading: true
    };
  }
  
  componentDidMount() {
    this.fetchUser();
  }
  
  componentDidUpdate(prevProps) {
    if (prevProps.userId !== this.props.userId) {
      this.fetchUser();
    }
  }
  
  componentWillUnmount() {
    console.log("Componente desmontado");
  }
  
  async fetchUser() {
    this.setState({ loading: true });
    try {
      const response = await fetch(`/api/users/${this.props.userId}`);
      const data = await response.json();
      this.setState({ user: data });
    } catch (error) {
      console.error("Error fetching user:", error);
    } finally {
      this.setState({ loading: false });
    }
  }
  
  render() {
    const { loading, user } = this.state;
    
    if (loading) return <div>Cargando...</div>;
    if (!user) return <div>Usuario no encontrado</div>;
    
    return (
      <div className="user-profile">
        <h2>{user.name}</h2>
        <p>Email: {user.email}</p>
        <p>Role: {user.role}</p>
      </div>
    );
  }
}
```

### 🎯 Props en profundidad

Las props (propiedades) son la forma principal de pasar datos entre componentes en React:

#### 1. Sintaxis completa

```jsx
// Definición de componente con props
const Button = (props) => {
  return (
    <button 
      className={`btn ${props.variant}`} 
      onClick={props.onClick}
      disabled={props.disabled}
    >
      {props.icon && <span className="icon">{props.icon}</span>}
      {props.children}
    </button>
  );
};

// Uso del componente
<Button 
  variant="primary" 
  onClick={handleClick} 
  disabled={isLoading}
  icon={<FaSave />}
>
  Guardar cambios
</Button>
```

#### 2. Desestructuración de props

```jsx
// Más limpio con desestructuración
const Button = ({ variant, onClick, disabled, icon, children }) => {
  return (
    <button 
      className={`btn ${variant}`} 
      onClick={onClick}
      disabled={disabled}
    >
      {icon && <span className="icon">{icon}</span>}
      {children}
    </button>
  );
};
```

#### 3. Valores por defecto

```jsx
// Con valores por defecto
const Button = ({ 
  variant = "default", 
  onClick, 
  disabled = false, 
  icon, 
  children 
}) => {
  // ...igual que antes
};

// Alternativa (menos usada ahora)
Button.defaultProps = {
  variant: "default",
  disabled: false
};
```

#### 4. PropTypes (validación de props)

```jsx
import PropTypes from 'prop-types';

const Button = ({ variant, onClick, disabled, icon, children }) => {
  // ...igual que antes
};

Button.propTypes = {
  variant: PropTypes.oneOf(['default', 'primary', 'secondary', 'danger']),
  onClick: PropTypes.func.isRequired,
  disabled: PropTypes.bool,
  icon: PropTypes.element,
  children: PropTypes.node.isRequired
};
```

#### 5. TypeScript (mejor que PropTypes)

```tsx
type ButtonProps = {
  variant?: 'default' | 'primary' | 'secondary' | 'danger';
  onClick: () => void;
  disabled?: boolean;
  icon?: React.ReactNode;
  children: React.ReactNode;
};

const Button = ({ 
  variant = 'default', 
  onClick, 
  disabled = false, 
  icon, 
  children 
}: ButtonProps) => {
  // ...igual que antes
};
```

### 🔄 Ciclo de vida de los componentes

#### Ciclo de vida en componentes funcionales (Hooks)

```jsx
import React, { useState, useEffect, useLayoutEffect } from 'react';

function ComponentLifecycle() {
  console.log("1. Renderizado (o re-renderizado)");
  
  const [count, setCount] = useState(0);
  
  // Se ejecuta después de cada renderizado
  useEffect(() => {
    console.log("3. Efecto ejecutado (similar a componentDidMount + componentDidUpdate)");
    
    // Código de efecto
    document.title = `Contador: ${count}`;
    
    // Función de limpieza (opcional)
    return () => {
      console.log("4. Limpieza antes del próximo efecto o desmontaje");
      document.title = "React App";
    };
  }, [count]); // Array de dependencias
  
  // Se ejecuta una sola vez (montaje)
  useEffect(() => {
    console.log("Solo al montar el componente");
    
    return () => {
      console.log("Solo al desmontar el componente");
    };
  }, []); // Array de dependencias vacío
  
  // useLayoutEffect se ejecuta sincrónicamente después de las mutaciones del DOM
  useLayoutEffect(() => {
    console.log("2. LayoutEffect (antes de que el navegador pinte)");
  });
  
  return (
    <div>
      <p>Contador: {count}</p>
      <button onClick={() => setCount(count + 1)}>Incrementar</button>
    </div>
  );
}
```

#### Diagrama del ciclo de vida (Hooks)

```
Montaje:
1. Llamada a la función del componente
2. Se ejecutan los hooks (useState, useReducer, etc.)
3. Se renderiza el componente (JSX → DOM Virtual)
4. Se actualiza el DOM real
5. Se ejecutan los useLayoutEffect
6. El navegador pinta la pantalla
7. Se ejecutan los useEffect

Actualización:
1. Re-llamada a la función del componente
2. Se recuperan valores de Hooks
3. Se re-renderiza el componente
4. Se actualiza el DOM real
5. Se ejecutan funciones de limpieza de useLayoutEffect
6. Se ejecutan los nuevos useLayoutEffect
7. El navegador pinta la pantalla
8. Se ejecutan funciones de limpieza de useEffect
9. Se ejecutan los nuevos useEffect

Desmontaje:
1. Se ejecutan funciones de limpieza de useLayoutEffect
2. Se ejecutan funciones de limpieza de useEffect
```

### ⚠️ Errores comunes con componentes

1. **Modificar props**
   ```jsx
   // ❌ INCORRECTO
   function Form(props) {
     props.defaultValue = "Nuevo valor"; // Error: props son inmutables
     // ...
   }
   
   // ✅ CORRECTO
   function Form(props) {
     const [value, setValue] = useState(props.defaultValue);
     // ...
   }
   ```

2. **Olvidar dependencias en useEffect**
   ```jsx
   // ❌ INCORRECTO
   useEffect(() => {
     fetchData(userId); // Warning: 'userId' no está en las dependencias
   }, []); 
   
   // ✅ CORRECTO
   useEffect(() => {
     fetchData(userId);
   }, [userId]); 
   ```

3. **No manejar la asincronía correctamente**
   ```jsx
   // ❌ INCORRECTO (puede causar memory leak)
   useEffect(() => {
     fetchData().then(data => {
       if (data) setItems(data); // Error si el componente se desmontó
     });
   }, []);
   
   // ✅ CORRECTO
   useEffect(() => {
     let mounted = true;
     fetchData().then(data => {
       if (mounted && data) setItems(data);
     });
     return () => { mounted = false; };
   }, []);
   ```

4. **Renderizado condicional incorrecto**
   ```jsx
   // ❌ INCORRECTO
   function Component() {
     if (!user) return; // Devuelve undefined, no es válido
     return <div>{user.name}</div>;
   }
   
   // ✅ CORRECTO
   function Component() {
     if (!user) return null; // o <></> o <div></div>
     return <div>{user.name}</div>;
   }
   ```

### 🚀 Optimización de componentes

#### 1. Memorización para evitar re-renderizados innecesarios

```jsx
// Memorizar un componente completo
const MemoizedComponent = React.memo(MyComponent);

// Memorizar valores costosos de calcular
const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);

// Memorizar callbacks
const memoizedCallback = useCallback(() => {
  doSomething(a, b);
}, [a, b]);
```

#### 2. Lazy loading de componentes

```jsx
// Importación normal (carga inmediata)
import HeavyComponent from './HeavyComponent';

// Importación lazy (carga bajo demanda)
const HeavyComponent = React.lazy(() => import('./HeavyComponent'));

// Uso con Suspense
function App() {
  return (
    <Suspense fallback={<div>Cargando...</div>}>
      <HeavyComponent />
    </Suspense>
  );
}
```

#### 3. Renderizado condicional eficiente

```jsx
// Evitar renderizado innecesario de secciones grandes
function Dashboard({ user }) {
  // Extraer solo lo que necesitas del estado global
  const { isAdmin } = useSelector(state => ({
    isAdmin: state.auth.user?.role === 'admin'
  }));
  
  return (
    <div>
      <CommonHeader />
      <MainContent />
      
      {/* Sección pesada que solo se renderiza para admins */}
      {isAdmin && <AdminPanel />}
    </div>
  );
}
```

---

## 💾 Estado: Haciendo tu app interactiva

### Definición técnica completa

El **estado** en React es un sistema para mantener y actualizar datos que pueden cambiar con el tiempo dentro de un componente. Técnicamente:

- Se crea utilizando el hook `useState` o `useReducer` en componentes funcionales
- Está encapsulado dentro del componente (o compartido mediante Context/Redux)
- Es inmutable y solo debe modificarse mediante las funciones actualizadoras
- Cuando cambia, desencadena un re-renderizado del componente
- Es asíncrono por naturaleza (las actualizaciones se agrupan por rendimiento)

### 🔄 Tipos de estado en React

#### 1. Estado local (useState)

```jsx
import { useState } from 'react';

function Counter() {
  // Estado simple (número)
  const [count, setCount] = useState(0);
  
  // Estado como objeto
  const [user, setUser] = useState({
    name: '',
    email: '',
    isLoggedIn: false
  });
  
  // Actualizar estado simple
  const increment = () => setCount(count + 1);
  
  // Actualizar estado complejo
  const updateEmail = (newEmail) => {
    setUser(prevUser => ({
      ...prevUser,  // Mantener las demás propiedades
      email: newEmail  // Actualizar solo email
    }));
  };
  
  // Actualización basada en el estado anterior
  const incrementByTen = () => {
    // Forma segura cuando dependes del valor previo
    setCount(prevCount => prevCount + 10);
  };
  
  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={increment}>+1</button>
      <button onClick={incrementByTen}>+10</button>
      
      <input 
        value={user.email}
        onChange={(e) => updateEmail(e.target.value)}
      />
    </div>
  );
}
```

#### 2. Estado complejo (useReducer)

```jsx
import { useReducer } from 'react';

// Definir las acciones posibles
const ACTIONS = {
  DEPOSIT: 'deposit',
  WITHDRAW: 'withdraw',
  ADD_TRANSACTION: 'add_transaction'
};

// Reducer: función pura que actualiza el estado
function bankReducer(state, action) {
  switch (action.type) {
    case ACTIONS.DEPOSIT:
      return {
        ...state,
        balance: state.balance + action.payload,
      };
    case ACTIONS.WITHDRAW:
      if (state.balance < action.payload) {
        return {
          ...state,
          error: 'Fondos insuficientes'
        };
      }
      return {
        ...state,
        balance: state.balance - action.payload,
        error: null
      };
    case ACTIONS.ADD_TRANSACTION:
      return {
        ...state,
        transactions: [...state.transactions, action.payload]
      };
    default:
      return state;
  }
}

function BankAccount() {
  // Estado inicial
  const initialState = {
    balance: 0,
    transactions: [],
    error: null
  };
  
  // Crear estado con reducer
  const [state, dispatch] = useReducer(bankReducer, initialState);
  
  // Funciones para actualizar el estado
  const handleDeposit = (amount) => {
    dispatch({ 
      type: ACTIONS.DEPOSIT, 
      payload: amount 
    });
    
    dispatch({
      type: ACTIONS.ADD_TRANSACTION,
      payload: { type: 'deposit', amount, date: new Date() }
    });
  };
  
  const handleWithdraw = (amount) => {
    dispatch({ 
      type: ACTIONS.WITHDRAW, 
      payload: amount 
    });
    
    if (state.balance >= amount) {
      dispatch({
        type: ACTIONS.ADD_TRANSACTION,
        payload: { type: 'withdrawal', amount, date: new Date() }
      });
    }
  };
  
  return (
    <div>
      <h2>Saldo: ${state.balance}</h2>
      {state.error && <p className="error">{state.error}</p>}
      
      <div>
        <button onClick={() => handleDeposit(100)}>Depositar $100</button>
        <button onClick={() => handleWithdraw(50)}>Retirar $50</button>
      </div>
      
      <h3>Transacciones recientes</h3>
      <ul>
        {state.transactions.map((tx, index) => (
          <li key={index}>
            {tx.type}: ${tx.amount} - {tx.date.toLocaleDateString()}
          </li>
        ))}
      </ul>
    </div>
  );
}
```

#### 3. Estado compartido (Context API)

```jsx
import { createContext, useContext, useState } from 'react';

// 1. Crear el contexto
const ThemeContext = createContext();

// 2. Crear proveedor
function ThemeProvider({ children }) {
  const [theme, setTheme] = useState('light');
  
  const toggleTheme = () => {
    setTheme(prevTheme => prevTheme === 'light' ? 'dark' : 'light');
  };
  
  // Valores disponibles en el contexto
  const value = {
    theme,
    toggleTheme,
    isDark: theme === 'dark'
  };
  
  return (
    <ThemeContext.Provider value={value}>
      {children}
    </ThemeContext.Provider>
  );
}

// 3. Hook personalizado para acceder al contexto
function useTheme() {
  const context = useContext(ThemeContext);
  if (context === undefined) {
    throw new Error('useTheme debe usarse dentro de un ThemeProvider');
  }
  return context;
}

// 4. Componente que consume el contexto
function ThemedButton() {
  const { theme, toggleTheme } = useTheme();
  
  return (
    <button 
      className={`btn ${theme === 'dark' ? 'btn-light' : 'btn-dark'}`}
      onClick={toggleTheme}
    >
      Cambiar a modo {theme === 'light' ? 'oscuro' : 'claro'}
    </button>
  );
}

// 5. Aplicación con el contexto
function App() {
  return (
    <ThemeProvider>
      <div className="app">
        <Header />
        <MainContent />
        <Footer />
      </div>
    </ThemeProvider>
  );
}

function Header() {
  const { theme } = useTheme();
  return (
    <header className={`header header-${theme}`}>
      <h1>Mi Aplicación</h1>
      <ThemedButton />
    </header>
  );
}
```

### 🔄 useEffect para efectos secundarios

```jsx
import { useState, useEffect } from 'react';

function UserProfile({ userId }) {
  const [user, setUser] = useState(null);
  const [isLoading, setIsLoading] = useState(true);
  const [error, setError] = useState(null);
  
  // Efecto para cargar datos del usuario
  useEffect(() => {
    // Resetear estado al cambiar userId
    setIsLoading(true);
    setError(null);
    
    // Función asíncrona para fetch
    const fetchUser = async () => {
      try {
        const response = await fetch(`https://api.example.com/users/${userId}`);
        
        if (!response.ok) {
          throw new Error(`Error HTTP: ${response.status}`);
        }
        
        const data = await response.json();
        setUser(data);
      } catch (err) {
        setError(err.message || 'Error al cargar usuario');
        setUser(null);
      } finally {
        setIsLoading(false);
      }
    };
    
    // Iniciar fetch
    fetchUser();
    
    // Función de limpieza
    return () => {
      // Cancelar requests pendientes si es necesario
      // Esto es importante para evitar actualizaciones de estado
      // en componentes desmontados
    };
  }, [userId]); // Dependencias
  
  // Efecto para título del documento
  useEffect(() => {
    if (user) {
      document.title = `Perfil de ${user.name}`;
    } else {
      document.title = 'Perfil de Usuario';
    }
    
    // Restaurar título original al desmontar
    return () => {
      document.title = 'Mi Aplicación';
    };
  }, [user]);
  
  // Renderizado condicional basado en estado
  if (isLoading) return <div>Cargando...</div>;
  if (error) return <div>Error: {error}</div>;
  if (!user) return <div>Usuario no encontrado</div>;
  
  return (
    <div className="user-profile">
      <h2>{user.name}</h2>
      <p>Email: {user.email}</p>
      {/* Más información del usuario */}
    </div>
  );
}
```

### ⚠️ Errores comunes y limitaciones del estado

1. **Mutar el estado directamente**
   ```jsx
   // ❌ INCORRECTO - Mutación directa
   const [items, setItems] = useState([1, 2, 3]);
   
   const addItem = () => {
     items.push(4);  // ❌ Muta el array original
     setItems(items);  // React no detecta el cambio
   };
   
   // ✅ CORRECTO - Crear nueva referencia
   const addItem = () => {
     setItems([...items, 4]);  // Nuevo array
   };
   ```

2. **Actualizar estado en bucle infinito**
   ```jsx
   // ❌ INCORRECTO - Actualización en renderizado
   function Component() {
     const [count, setCount] = useState(0);
     
     // Esto causa un bucle infinito
     setCount(count + 1);
     
     return <div>{count}</div>;
   }
   
   // ✅ CORRECTO - Actualización en evento o efecto
   function Component() {
     const [count, setCount] = useState(0);
     
     useEffect(() => {
       // Solo se ejecuta una vez al montar
       setCount(1);
     }, []);
     
     return <div>{count}</div>;
   }
   ```

3. **Actualización asíncrona no sincronizada**
   ```jsx
   // ❌ INCORRECTO - Asumir actualización síncrona
   const handleClick = () => {
     setCount(count + 1);
     console.log(count);  // Muestra valor antiguo
   };
   
   // ✅ CORRECTO - Usar efecto o callback
   const handleClick = () => {
     setCount(prevCount => {
       const newCount = prevCount + 1;
       console.log(newCount);  // Valor correcto
       return newCount;
     });
   };
   
   // O con useEffect
   useEffect(() => {
     console.log(count);  // Se ejecuta después de la actualización
   }, [count]);
   ```

4. **Estado inicial costoso**
   ```jsx
   // ❌ INCORRECTO - Cálculo costoso en cada renderizado
   function Component() {
     // Esto se ejecuta en cada renderizado
     const [items, setItems] = useState(calculateExpensiveInitialState());
     
     // ...
   }
   
   // ✅ CORRECTO - Función inicializadora
   function Component() {
     // La función se ejecuta solo en el montaje inicial
     const [items, setItems] = useState(() => calculateExpensiveInitialState());
     
     // ...
   }
   ```

5. **Estructura de estado inadecuada**
   ```jsx
   // ❌ INCORRECTO - Demasiados estados relacionados
   const [firstName, setFirstName] = useState('');
   const [lastName, setLastName] = useState('');
   const [email, setEmail] = useState('');
   const [age, setAge] = useState(0);
   
   // ✅ CORRECTO - Agrupar estados relacionados
   const [formData, setFormData] = useState({
     firstName: '',
     lastName: '',
     email: '',
     age: 0
   });
   
   // Actualizar solo una propiedad
   const updateField = (field, value) => {
     setFormData({
       ...formData,
       [field]: value
     });
   };
   ```

6. **Sobrecarga de renderizado**
   ```jsx
   // ❌ PROBLEMA - Muchas actualizaciones de estado desencadenan múltiples renderizados
   const handleSubmit = () => {
     setIsLoading(true);
     setFormErrors({});
     setSubmitCount(c => c + 1);
     // Cada setX causa un renderizado separado
   };
   
   // ✅ MEJOR - Usar useReducer para actualizaciones relacionadas
   const [state, dispatch] = useReducer(formReducer, initialState);
   
   const handleSubmit = () => {
     // Una sola actualización
     dispatch({ type: 'SUBMIT_START' });
   };
   ```

### 🔄 Comparación: Enfoques de estado

| Enfoque | Ventajas | Desventajas | Mejor para |
|---------|----------|-------------|------------|
| **useState** | Simple, fácil de entender | No escala bien para lógica compleja | Estado simple, componentes pequeños |
| **useReducer** | Centraliza lógica, testeable | Más código, más complejo | Estados complejos, transiciones con lógica |
| **Context API** | Evita prop drilling, nativo | Causa re-renderizado completo, no optimizado | Datos globales de baja frecuencia (tema, auth) |
| **Redux** | Centralizado, DevTools, middleware | Boilerplate, curva aprendizaje | Aplicaciones grandes, estado complejo |
| **Zustand** | API simple, mínimo boilerplate | Biblioteca externa | Balance entre simplicidad y potencia |
| **Jotai/Recoil** | Atómico, optimizado | Nuevas APIs para aprender | Optimización de renderizado |

---

## 🎬 Fragmentos: Agrupando elementos

### Definición técnica completa

Los Fragments en React son una característica que permite agrupar múltiples elementos hijos sin añadir nodos adicionales al DOM. Técnicamente:

- Son representados internamente como `React.Fragment`
- No tienen representación en el DOM (no generan elementos HTML adicionales)
- Pueden recibir la prop `key` (en sintaxis explícita) para listas
- Permiten devolver múltiples elementos desde un componente
- Se renderizan como sus hijos directos

### 🔄 Sintaxis y uso avanzado

#### 1. Sintaxis completa (necesaria para props como key)

```jsx
import { Fragment } from 'react';

function ListItems({ items }) {
  return (
    <>
      {items.map(item => (
        // Necesita sintaxis explícita para usar key
        <Fragment key={item.id}>
          <dt>{item.term}</dt>
          <dd>{item.description}</dd>
        </Fragment>
      ))}
    </>
  );
}
```

#### 2. Sintaxis abreviada (no acepta props)

```jsx
function Component() {
  return (
    <>
      <h1>Título</h1>
      <p>Párrafo 1</p>
      <p>Párrafo 2</p>
    </>
  );
}
```

#### 3. Fragments anidados

```jsx
function NestedFragments() {
  return (
    <>
      <h1>Título Principal</h1>
      <>
        <h2>Subtítulo</h2>
        <p>Párrafo anidado</p>
      </>
      <p>Párrafo final</p>
    </>
  );
}
```

### 🔄 Comparación de enfoques para múltiples elementos

| Enfoque | DOM Resultante | Ventajas | Desventajas |
|---------|----------------|----------|-------------|
| **Fragments** | `<h1></h1><p></p>` | No añade nodos DOM, mantiene semántica | Puede complicar debugging |
| **Div** | `<div><h1></h1><p></p></div>` | Fácil de debuggear, CSS aplicable | Rompe semántica, interfiere con layouts |
| **Array** | `<h1></h1><p></p>` | Solución nativa de JS | Requiere keys manualmente |

#### Comparación en código

```jsx
// 1. Usando Fragment (recomendado)
function WithFragment() {
  return (
    <>
      <h1>Título</h1>
      <p>Párrafo</p>
    </>
  );
}

// 2. Usando div (no ideal)
function WithDiv() {
  return (
    <div>
      <h1>Título</h1>
      <p>Párrafo</p>
    </div>
  );
}

// 3. Usando array (menos común)
function WithArray() {
  return [
    <h1 key="title">Título</h1>,
    <p key="paragraph">Párrafo</p>
  ];
}
```

### 🎯 Casos de uso específicos

#### 1. Listas de elementos semánticamente relacionados

```jsx
function GlossaryList({ terms }) {
  return (
    <dl>
      {terms.map(term => (
        <Fragment key={term.id}>
          <dt>{term.term}</dt>
          <dd>{term.definition}</dd>
        </Fragment>
      ))}
    </dl>
  );
}
```

#### 2. Tablas con filas condicionales

```jsx
function Table({ data, showDetails }) {
  return (
    <table>
      <thead>
        <tr>
          <th>ID</th>
          <th>Nombre</th>
        </tr>
      </thead>
      <tbody>
        {data.map(item => (
          <Fragment key={item.id}>
            <tr>
              <td>{item.id}</td>
              <td>{item.name}</td>
            </tr>
            {showDetails && (
              <tr className="details-row">
                <td colSpan="2">
                  {item.details}
                </td>
              </tr>
            )}
          </Fragment>
        ))}
      </tbody>
    </table>
  );
}
```

#### 3. Evitar divs innecesarios en layouts flexbox/grid

```jsx
function FlexLayout() {
  return (
    <div className="flex-container">
      {/* Grupos lógicos sin divs extras que romperían el layout */}
      <>
        <h2>Sección 1</h2>
        <p>Contenido sección 1</p>
      </>
      
      <>
        <h2>Sección 2</h2>
        <p>Contenido sección 2</p>
      </>
    </div>
  );
}
```

### ⚠️ Limitaciones y consideraciones

1. **Debugging más difícil**
      - Los Fragments no aparecen en el DOM
      - Dificulta la inspección de elementos
      - Las herramientas de React DevTools sí los muestran

2. **No acepta atributos (excepto key)**
      - No puedes añadir clases, eventos o atributos
      - La única prop que acepta es `key` (y solo con sintaxis explícita)

3. **Problemas potenciales con CSS**
      - No puedes seleccionar los Fragments con CSS
      - Imposible aplicar estilos, márgenes o padding a grupos

4. **Compatibilidad con bibliotecas externas**
      - Algunas bibliotecas externas pueden tener problemas con Fragments
      - Especialmente las que manipulan el DOM directamente

---

## 🎨 Estilos en React

### Enfoque completo de estilos en React

React ofrece múltiples enfoques para aplicar estilos, cada uno con sus propias ventajas e inconvenientes. A continuación se detallan los principales métodos:

### 1. CSS Global

El método más tradicional, importando archivos CSS en los componentes:

```jsx
// index.css (archivo CSS global)
body {
  font-family: 'Segoe UI', sans-serif;
  margin: 0;
  padding: 0;
}

.button {
  background-color: #0070f3;
  color: white;
  border: none;
  padding: 10px 15px;
  border-radius: 4px;
  cursor: pointer;
}

.button:hover {
  background-color: #0051a2;
}

// App.jsx
import './index.css';

function App() {
  return (
    <div className="app">
      <button className="button">Click me</button>
    </div>
  );
}
```

**Ventajas:**
- Familiar para desarrolladores de HTML/CSS tradicional
- Soporte para todas las características CSS (media queries, pseudo-selectores)
- Cacheable y optimizable

**Desventajas:**
- Colisiones de nombres (namespace global)
- Difícil de mantener en aplicaciones grandes
- No hay encapsulación a nivel de componente

### 2. CSS Modules

Archivos CSS encapsulados a nivel de componente:

```jsx
// Button.module.css
.button {
  background-color: #0070f3;
  color: white;
  border: none;
  padding: 10px 15px;
  border-radius: 4px;
  cursor: pointer;
}

.primary {
  background-color: #0070f3;
}

.secondary {
  background-color: #6c757d;
}

// Button.jsx
import styles from './Button.module.css';

function Button({ children, variant = 'primary' }) {
  return (
    <button className={`${styles.button} ${styles[variant]}`}>
      {children}
    </button>
  );
}
```

**Ventajas:**
- Encapsulación a nivel de componente
- Evita colisiones de nombres (clases con hashing único)
- Soporte para todas las características CSS
- Ideal para aplicaciones medianas a grandes

**Desventajas:**
- Requiere configuración de build (aunque incluida en CRA/Vite)
- Aún no soluciona estilos dinámicos
- No es ideal para estilos altamente condicionales

### 3. Estilos en línea (Inline Styles)

Definición de estilos directamente como objetos JavaScript:

```jsx
function Button({ children, variant = 'primary' }) {
  // Objeto de estilos base
  const baseStyle = {
    backgroundColor: variant === 'primary' ? '#0070f3' : '#6c757d',
    color: 'white',
    border: 'none',
    padding: '10px 15px',
    borderRadius: '4px',
    cursor: 'pointer',
    // Nombres en camelCase, no kebab-case
    fontSize: '16px',
    fontWeight: 'bold'
  };
  
  // Estilos condicionales
  const dynamicStyles = {
    // Añadir estilos basados en props
    opacity: props.disabled ? 0.6 : 1,
    // Transformaciones basadas en estado
    transform: props.active ? 'scale(0.98)' : 'scale(1)'
  };
  
  return (
    <button 
      style={{ ...baseStyle, ...dynamicStyles }}
      disabled={props.disabled}
    >
      {children}
    </button>
  );
}
```

**Ventajas:**
- Altamente dinámico (basado en props/estado)
- No hay conflictos de nombres
- Integración perfecta con la lógica de componentes
- No requiere archivos adicionales

**Desventajas:**
- No soporta media queries, pseudo-selectores
- Problemas de rendimiento en listas grandes
- Sintaxis diferente al CSS normal (camelCase vs kebab-case)
- Difícil de mantener para estilos complejos

### 4. CSS-in-JS (Styled Components, Emotion)

Bibliotecas que permiten definir componentes con estilos incorporados:

```jsx
// Con styled-components
import styled from 'styled-components';

// Crear un componente estilizado
const StyledButton = styled.button`
  background-color: ${props => props.primary ? '#0070f3' : '#6c757d'};
  color: white;
  border: none;
  padding: 10px 15px;
  border-radius: 4px;
  cursor: pointer;
  
  &:hover {
    background-color: ${props => props.primary ? '#0051a2' : '#5a6268'};
  }
  
  ${props => props.large && `
    font-size: 18px;
    padding: 12px 20px;
  `}
  
  @media (max-width: 768px) {
    width: 100%;
  }
`;

// Usar el componente estilizado
function Button({ children, primary, large }) {
  return (
    <StyledButton primary={primary} large={large}>
      {children}
    </StyledButton>
  );
}
```

**Ventajas:**
- Estilos dinámicos basados en props
- Soporte para CSS completo (media queries, nesting, pseudo-selectores)
- Encapsulación completa
- Elimina CSS no utilizado automáticamente

**Desventajas:**
- Aumenta el tamaño del bundle
- Curva de aprendizaje adicional
- Posible impacto en rendimiento (aunque mínimo)
- Requiere biblioteca adicional

### 5. Utility-first CSS (Tailwind CSS)

Framework de utilidades CSS que permite aplicar clases predefinidas:

```jsx
// Requiere configuración de Tailwind
function Button({ children, variant = 'primary', size = 'md' }) {
  // Clases base
  const baseClasses = "font-bold rounded cursor-pointer focus:outline-none";
  
  // Clases condicionales
  const variantClasses = {
    primary: "bg-blue-500 hover:bg-blue-700 text-white",
    secondary: "bg-gray-500 hover:bg-gray-700 text-white",
    success: "bg-green-500 hover:bg-green-700 text-white"
  };
  
  const sizeClasses = {
    sm: "py-1 px-2 text-sm",
    md: "py-2 px-4 text-base",
    lg: "py-3 px-6 text-lg"
  };
  
  return (
    <button 
      className={`${baseClasses} ${variantClasses[variant]} ${sizeClasses[size]}`}
    >
      {children}
    </button>
  );
}
```

**Ventajas:**
- Desarrollo rápido sin escribir CSS personalizado
- Sistema de diseño consistente
- Excelente para prototipado y desarrollo inicial
- Altamente configurable

**Desventajas:**
- Clases largas en el HTML (posible desorden visual)
- Curva de aprendizaje para memorizar clases
- Puede sentirse restrictivo para diseños muy personalizados

### 🔄 Comparación técnica de enfoques

| Característica | CSS Global | CSS Modules | Inline Styles | CSS-in-JS | Utility CSS |
|----------------|------------|------------|---------------|-----------|-------------|
| **Encapsulación** | ❌ No | ✅ Sí | ✅ Sí | ✅ Sí | ❌ No |
| **Media Queries** | ✅ Sí | ✅ Sí | ❌ No | ✅ Sí | ✅ Sí |
| **Pseudo-selectores** | ✅ Sí | ✅ Sí | ❌ No | ✅ Sí | ✅ Sí |
| **Estilos dinámicos** | ❌ Limitado | ❌ Limitado | ✅ Sí | ✅ Sí | ⚠️ Parcial |
| **Rendimiento** | ✅ Excelente | ✅ Excelente | ⚠️ Puede impactar | ⚠️ Puede impactar | ✅ Excelente |
| **Tamaño bundle** | ✅ Pequeño | ✅ Pequeño | ✅ Pequeño | ⚠️ Aumenta | ⚠️ Grande inicialmente |
| **DevTools soporte** | ✅ Excelente | ✅ Bueno | ⚠️ Limitado | ⚠️ Varía | ✅ Bueno |
| **Temas/Theming** | ⚠️ Complejo | ⚠️ Complejo | ⚠️ Manual | ✅ Excelente | ✅ Bueno |
| **Integración React** | ⚠️ Básica | ✅ Buena | ✅ Nativa | ✅ Excelente | ✅ Buena |

### ⚠️ Errores comunes y limitaciones

1. **Mezclar demasiados enfoques**
   ```jsx
   // ❌ ANTI-PATRÓN: Mezcla inconsistente
   function Component() {
     return (
       <div className="container" style={{ marginTop: '20px' }}>
         <StyledHeading>Título</StyledHeading>
         <p className={styles.paragraph}>Contenido</p>
       </div>
     );
   }
   ```

2. **Sobrecarga de estilos en línea**
   ```jsx
   // ❌ INCORRECTO: Inline styles masivos
   function Card() {
     return (
       <div style={{
         display: 'flex',
         flexDirection: 'column',
         backgroundColor: '#fff',
         borderRadius: '8px',
         boxShadow: '0 2px 8px rgba(0,0,0,0.1)',
         padding: '20px',
         margin: '10px',
         maxWidth: '400px',
         // ...20 propiedades más
       }}>
         {/* Contenido */}
       </div>
     );
   }
   ```

3. **No reutilizar estilos comunes**
   ```jsx
   // ❌ INCORRECTO: Repetición constante
   function App() {
     return (
       <div>
         <button style={{ color: 'white', bgColor: 'blue', padding: '10px' }}>Botón 1</button>
         <button style={{ color: 'white', bgColor: 'blue', padding: '10px' }}>Botón 2</button>
         <button style={{ color: 'white', bgColor: 'blue', padding: '10px' }}>Botón 3</button>
       </div>
     );
   }
   ```

4. **Animar con inline styles**
   ```jsx
   // ❌ INCORRECTO: Animaciones con inline styles
   function AnimatedButton() {
     const [isHovered, setIsHovered] = useState(false);
     
     return (
       <button
         style={{
           backgroundColor: isHovered ? 'darkblue' : 'blue',
           transition: 'background-color 0.3s' // No funcionará bien
         }}
         onMouseEnter={() => setIsHovered(true)}
         onMouseLeave={() => setIsHovered(false)}
       >
         Hover me
       </button>
     );
   }
   ```

### 🎯 Recomendaciones específicas

1. **Para proyectos pequeños o aprendizaje**:
      - CSS Modules o CSS Global

2. **Para proyectos medianos a grandes**:
      - CSS Modules + componentes de diseño
      - CSS-in-JS (styled-components/emotion)
      - Utility CSS (Tailwind) si el equipo está familiarizado

3. **Para aplicaciones empresariales**:
      - Sistema de diseño con componentes estilizados reutilizables
      - Enfoque consistente en todo el equipo
      - Documentación de componentes (Storybook)

4. **Para rendimiento óptimo**:
      - CSS Modules
      - Evitar exceso de estilos en línea
      - Minimizar cambios de estilo dinámicos

---

## 🚀 Resumen y próximos pasos

### Lo que hemos aprendido:

1. **React**: Una biblioteca declarativa para interfaces de usuario que utiliza Virtual DOM para optimizar actualizaciones.

2. **JSX**: Sintaxis que combina JavaScript y XML, permitiendo escribir estructuras de UI con todo el poder de JavaScript.

3. **Componentes**: Bloques reutilizables que pueden ser funcionales (recomendados) o de clase (legacy), con ciclos de vida y propiedades.

4. **Props**: Mecanismo para pasar datos a componentes de forma inmutable, permitiendo comunicación padre-hijo.

5. **Estado**: Sistema para manejar datos mutables dentro de componentes, con hooks como useState y useReducer.

6. **Fragments**: Forma de agrupar elementos sin añadir nodos extra al DOM, mejorando la semántica y rendimiento.

7. **Estilos**: Múltiples enfoques (CSS Modules, CSS-in-JS, inline styles), cada uno con ventajas para casos de uso específicos.

### Conceptos avanzados para explorar:

1. **Hooks personalizados**: Extraer lógica reutilizable en funciones con nombre use*.
2. **Context API**: Compartir estado entre componentes sin prop drilling.
3. **Optimización de rendimiento**: useMemo, useCallback, React.memo.
4. **Suspense y React.lazy**: Carga diferida de componentes.
5. **Manejo de formularios**: Formularios controlados vs no controlados.
6. **Testing en React**: Jest, React Testing Library.
7. **Enrutamiento**: React Router para navegación.
8. **Manejo de estado global**: Redux, Zustand, Jotai, Recoil.
9. **Server Components**: Nueva arquitectura con React 18+.
10. **Server-Side Rendering (SSR)**: Next.js, Remix.

### 💡 Proyecto de práctica sugerido

Crea una aplicación de lista de tareas (Todo App) con React que incluya:

- Componentes reutilizables (Botón, Card, Input)
- Estado local con useState para tareas
- Estado global con Context para preferencias de usuario
- Estilos con CSS Modules o styled-components
- Almacenamiento en localStorage
- Filtrado y búsqueda de tareas
- Animaciones básicas
- Modo oscuro/claro con Context

Este proyecto te permitirá aplicar todos los conceptos aprendidos en un caso práctico y será una excelente adición a tu portafolio.
