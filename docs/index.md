# Introducción a React

## ¿Qué es React?

**React** es una **librería declarativa y eficiente de JavaScript**, orientada a la construcción de interfaces de usuario interactivas. Su diseño permite crear aplicaciones de diversa complejidad, desde soluciones simples hasta sistemas robustos y escalables. Al ser una librería, React puede integrarse de forma directa en archivos HTML, aunque también admite implementaciones modernas mediante herramientas de desarrollo avanzadas.

### Características clave de React:

- **Declaratividad**: Permite describir el estado deseado de la interfaz y React se encarga de actualizar el DOM de manera eficiente.
- **Actualizaciones selectivas del DOM**: Solo se renderizan los elementos que cambian, lo que mejora notablemente el rendimiento de la aplicación.
- **Flujo de datos unidireccional**: La información fluye en una sola dirección, evitando mutaciones impredecibles y facilitando la depuración.
- **Componentes reutilizables**: Las aplicaciones se construyen a partir de módulos encapsulados que pueden mantener su propio estado o ser completamente funcionales.

## Versatilidad de React

React no se limita al desarrollo web en el navegador. Gracias a su ecosistema, también permite:

- Renderizado en el **servidor** utilizando Node.js.
- Creación de **aplicaciones móviles** con React Native.
- Desarrollo de **aplicaciones de escritorio** mediante Electron.

Esta versatilidad permite a los desarrolladores utilizar el mismo enfoque y base de código para distintas plataformas.

## Primer ejemplo: Hola Mundo con React

Se presenta un ejemplo básico para demostrar la sintaxis de React y la utilización de JSX:

```jsx
const divRoot = document.querySelector('#root');

ReactDOM.render(
  <h1>Hola Mundo</h1>,
  divRoot
);
```

- Se obtiene una referencia al elemento HTML con `id="root"`.
- Se utiliza `ReactDOM.render()` para insertar un elemento JSX (`<h1>Hola Mundo</h1>`) en dicho nodo del DOM.

Este ejemplo permite visualizar de forma inmediata el resultado de una aplicación React renderizada en el navegador.

## JSX: JavaScript + XML

JSX es una extensión de sintaxis que combina JavaScript con una sintaxis similar a XML. Permite escribir código de forma declarativa y legible, facilitando la construcción de interfaces.

El siguiente ejemplo en JavaScript puro logra lo mismo que el anterior en JSX:

```javascript
const h1 = document.createElement('h1');
h1.textContent = 'Hola Mundo';
divRoot.appendChild(h1);
```

Este contraste demuestra la eficiencia y simplicidad que ofrece JSX al compararlo con la manipulación directa del DOM.

---

# Primer Proyecto React: Configuración y Primeros Pasos

## Introducción

En esta sección se abordará la creación de una aplicación React desde sus fundamentos, sin el uso de herramientas automatizadas como Vite o Create React App. Este enfoque permite comprender en profundidad el ciclo de vida de una aplicación React y las bibliotecas esenciales involucradas.

## Estructura del Proyecto

1. Crear una carpeta en el escritorio denominada `01-intro-product-app`. Se recomienda mantener una nomenclatura numérica secuencial (01, 02, 03, etc.) para organizar los proyectos de forma ordenada y facilitar su seguimiento.

2. Abrir dicha carpeta en un editor de código (por ejemplo, Visual Studio Code).

3. Generar un archivo `index.html` con la siguiente estructura inicial:

```html
<!DOCTYPE html>
<html lang="es">
  <head>
    <meta charset="UTF-8" />
    <title>React App</title>
  </head>
  <body>
    <div id="root"></div>

    <!-- Inclusión de bibliotecas necesarias -->
    <script src="https://unpkg.com/react@18/umd/react.development.js"></script>
    <script src="https://unpkg.com/react-dom@18/umd/react-dom.development.js"></script>
    <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>

    <!-- Código React personalizado -->
    <script type="text/babel">
      const productName = "Zapatos deportivos";
      const h1Tag = <h1>Hola, este es el producto: {productName}</h1>;

      const divRoot = document.querySelector("#root");
      ReactDOM.render(h1Tag, divRoot);
    </script>
  </body>
</html>
```

## Desglose del Código

- **Babel**: Esta herramienta transforma código JSX en JavaScript convencional comprensible por cualquier navegador. Aunque no se utiliza así en entornos productivos, resulta muy útil para fines educativos.
- **React** y **ReactDOM**: Son las bibliotecas clave para construir interfaces y renderizarlas eficientemente en el DOM.
- **Elemento **``** con id **``: Actúa como el contenedor principal donde se montará la interfaz de usuario React.

## Renderizado de Variables con JSX

React permite incorporar variables dentro del código JSX utilizando llaves `{}`. Por ejemplo:

```jsx
const productName = "Zapatos deportivos";
<h1>Hola, este es el producto: {productName}</h1>
```

Durante el renderizado, React reemplazará automáticamente `productName` por su valor correspondiente.

## Recomendaciones de Buenas Prácticas

- Se sugiere finalizar todas las sentencias con punto y coma `;` para evitar ambigüedades durante la interpretación del código.
- Familiarizarse con los atajos del navegador para acceder a las herramientas de desarrollo (por ejemplo: `Ctrl + Shift + I` o `F12`) facilita la detección de errores.

## Resultado Esperado

Al abrir el archivo `index.html` en el navegador, se debería visualizar el siguiente mensaje en pantalla:

```
Hola, este es el producto: Zapatos deportivos
```

La correcta visualización del mensaje indica que el entorno React ha sido configurado adecuadamente.

---


