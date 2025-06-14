# Introducción a las Props en React

## ¿Qué son las Props?

En React, las **props** (abreviatura de *properties*) son un mecanismo fundamental para permitir la comunicación entre componentes. Se utilizan para pasar datos desde un componente padre hacia un componente hijo, promoviendo así la reutilización y modularidad del código.

---

## Estructura y Uso de las Props

Por convención, las props se definen como el primer argumento de una función que representa un componente. Aunque es común encontrar la palabra `props`, también es habitual usar la **desestructuración** del objeto para extraer únicamente los valores necesarios.

```jsx
function Greeting({ title, subtitle }) {
  return (
    <div>
      <h1>{title}</h1>
      <p>{subtitle}</p>
    </div>
  );
}
```

Este patrón permite mayor claridad y evita el uso redundante de `props.` antes de cada propiedad.

---

## Flujo de Datos desde el Componente Padre

El componente padre es responsable de definir los valores que serán enviados al componente hijo mediante atributos personalizados:

```jsx
function App() {
  return (
    <Greeting title="Bienvenidos a la Tienda" subtitle="Ofertas especiales por tiempo limitado" />
  );
}
```

Esto representa un flujo de datos unidireccional en el que `App` transmite los valores `title` y `subtitle` al componente `Greeting`.

---

## Visualización en Herramientas de Desarrollo

Usando herramientas como React Developer Tools, es posible inspeccionar el árbol de componentes y verificar las props que se están enviando. Esta visibilidad resulta útil para depuración y validación durante el desarrollo.

---

## Valores por Defecto y Validaciones

React permite establecer valores por defecto en caso de que alguna prop no haya sido enviada desde el componente padre:

```jsx
Greeting.defaultProps = {
  title: "Título genérico",
  subtitle: "Subtítulo por defecto"
};
```

Esto garantiza que el componente hijo funcione correctamente incluso si el padre omite alguna prop opcional.

---

## Tipado y Buenas Prácticas

Aunque React se puede usar con JavaScript puro, se recomienda emplear **TypeScript** o librerías como `PropTypes` para validar el tipo y la obligatoriedad de las props, especialmente en proyectos medianos o grandes.

Ejemplo con `PropTypes`:

```jsx
import PropTypes from 'prop-types';

Greeting.propTypes = {
  title: PropTypes.string.isRequired,
  subtitle: PropTypes.string
};
```

---

## Consideraciones Finales

- Las props deben ser **inmutables** dentro del componente hijo.
- Es preferible evitar pasar objetos o funciones directamente si no son necesarios, ya que pueden causar renderizados innecesarios.
- El uso de props permite crear componentes genéricos y reutilizables, lo que favorece una arquitectura más limpia y escalable.

---

## Conclusión

Las props son una de las piedras angulares del desarrollo con React. Comprender su funcionamiento, establecer valores por defecto y aplicar buenas prácticas en su uso permite construir aplicaciones robustas, modulares y fáciles de mantener.
