# Ejercicio Práctico: Uso de Props en React

## Objetivo
Este ejercicio tiene como propósito reforzar el conocimiento sobre el uso de **props** en componentes de React mediante un ejemplo del mundo real. Implementaremos una tarjeta de perfil de usuario que reciba datos como propiedades para ser renderizados dinámicamente.

---

## Descripción de la Actividad

Vamos a construir una aplicación React sencilla que muestre la información de un usuario (nombre, cargo y descripción) a través de un componente `UserCard`. La información será pasada como propiedades desde el componente principal `App`.

---

## Paso a Paso

### 1. Crear la Carpeta del Proyecto
```bash
mkdir perfil-usuario-react
cd perfil-usuario-react
```

### 2. Inicializar un Proyecto con Vite
```bash
npm create vite@latest . -- --template react
npm install
```

### 3. Crear el Archivo de Estilos Globales (opcional)
Archivo: `src/index.css`
```css
body {
  font-family: Arial, sans-serif;
  background-color: #f5f5f5;
  padding: 2rem;
}
```
Asegúrate de importar este archivo en `main.jsx`:
```jsx
import './index.css';
```

### 4. Crear el Componente `UserCard`
Archivo: `src/components/UserCard.jsx`
```jsx
function UserCard({ name, position, description }) {
  return (
    <div style={{
      backgroundColor: 'white',
      padding: '1.5rem',
      borderRadius: '8px',
      boxShadow: '0 2px 6px rgba(0,0,0,0.1)',
      maxWidth: '400px'
    }}>
      <h2>{name}</h2>
      <h4 style={{ color: 'gray' }}>{position}</h4>
      <p>{description}</p>
    </div>
  );
}

export default UserCard;
```

### 5. Usar el Componente en `App.jsx`
Archivo: `src/App.jsx`
```jsx
import UserCard from './components/UserCard';

function App() {
  return (
    <div>
      <UserCard
        name="Laura Martínez"
        position="Desarrolladora Frontend"
        description="Apasionada por crear experiencias de usuario modernas y eficientes usando React."
      />
    </div>
  );
}

export default App;
```

---

## Resultado Esperado
La aplicación debe renderizar una tarjeta de presentación con la siguiente información:
- Nombre: Laura Martínez
- Cargo: Desarrolladora Frontend
- Descripción: Apasionada por crear experiencias de usuario modernas y eficientes usando React.

---

## Recomendaciones
- Cambia los valores para probar distintos datos.
- Crea más de una tarjeta para ver cómo las props permiten reutilizar el componente.
- Añade validación de props con `PropTypes` si estás usando JavaScript puro.

---

## Conclusión
Este ejercicio demuestra cómo los componentes en React pueden ser reutilizados y personalizados mediante el uso de props. A medida que desarrolles aplicaciones más complejas, dominar esta técnica te permitirá escribir componentes más flexibles y mantenibles.
