# 🧰 Guía de Instalación de React en Mac paso a paso

Esta guía cubre la instalación desde Node.js hasta la creación de un proyecto React con Vite en macOS.

---

## 1️⃣ Instalar Node.js

React requiere Node.js (que incluye `npm`, el gestor de paquetes). Aquí hay dos formas comunes de instalarlo:

### ✅ Opción A: Usar Node Version Manager (recomendado)

1. Abre la terminal.
2. Instala `nvm`:

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
```

3. Cierra y vuelve a abrir la terminal, luego verifica que `nvm` esté instalado:

```bash
command -v nvm
```

4. Instala la versión LTS de Node.js:

```bash
nvm install --lts
nvm use --lts
```

5. Verifica que `node` y `npm` están instalados:

```bash
node -v
npm -v
```

---

## 2️⃣ Crear un proyecto React con Vite

1. Usa `npm` para crear un nuevo proyecto con plantilla de React:

```bash
npm create vite@latest
```

2. Cuando el asistente pregunte:
   - Nombre del proyecto: `tarea-react` (o el que quieras).
   - Selecciona: `React`
   - Lenguaje: `JavaScript` (puedes elegir TypeScript si lo deseas).

3. Entra al directorio del proyecto:

```bash
cd tarea-react
```

4. Instala las dependencias:

```bash
npm install
```

5. Ejecuta el servidor de desarrollo:

```bash
npm run dev
```

6. Abre el navegador en la dirección que te muestra, usualmente:  
   [http://localhost:5173](http://localhost:5173)

---

## 3️⃣ Estructura de carpetas

```plaintext
tarea-react/
├── index.html
├── package.json
├── vite.config.js
└── src/
    ├── App.jsx
    └── main.jsx
```

---

## 4️⃣ Editar tu primer componente

Edita `App.jsx` para modificar el contenido que se muestra en pantalla. Por ejemplo:

```jsx
function App() {
  return <h1>Hola, React en mi Mac! 🎉</h1>;
}

export default App;
```

Guarda y verás los cambios automáticamente en el navegador.

---
