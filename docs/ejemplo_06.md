
# Uso de Default Props en React

## Introducción
En el desarrollo de aplicaciones React, es común que ciertos componentes requieran valores predeterminados para sus propiedades (props). Esto garantiza un comportamiento predecible cuando los props no son proporcionados por el componente padre. En esta sesión, se exploró el uso adecuado de los **default props** y su relación con `PropTypes` para mejorar la robustez y mantenibilidad del código.

## Contexto de Aplicación
Considere un componente `EventCard` que muestra información sobre un evento próximo. Este componente puede recibir propiedades como `title`, `subtitle` y `organizer`. En escenarios donde algunas de estas props no se proporcionen, es recomendable establecer valores predeterminados.

## Ejemplo

### Componente: `EventCard.jsx`
```jsx
import PropTypes from 'prop-types';

function EventCard({ title, subtitle, organizer }) {
  return (
    <div className="event-card">
      <h2>{title}</h2>
      <p>{subtitle}</p>
      <small>Organizado por: {organizer}</small>
    </div>
  );
}

EventCard.propTypes = {
  title: PropTypes.string.isRequired,
  subtitle: PropTypes.string,
  organizer: PropTypes.string,
};

EventCard.defaultProps = {
  subtitle: 'Detalles próximamente disponibles.',
  organizer: 'Equipo de Eventos',
};

export default EventCard;
```

### Uso del componente en `App.jsx`
```jsx
import EventCard from './components/EventCard';

function App() {
  return (
    <div>
      <EventCard title="Conferencia React 2025" />
    </div>
  );
}
```

## Ventajas de Default Props
- Proporcionan valores sensatos en ausencia de props requeridas.
- Mejoran la experiencia de desarrollo al prevenir errores por `undefined`.
- Ayudan a documentar el comportamiento esperado del componente.

## Consideraciones Técnicas
- Los `defaultProps` se evalúan **antes** que las validaciones de `PropTypes`.
- La definición de `defaultProps` se recomienda al final del archivo, después de la declaración del componente y de `propTypes`.
- Aunque `defaultProps` es muy útil en proyectos con JavaScript puro, su uso disminuye cuando se adopta **TypeScript**, ya que el tipado y los valores por defecto pueden declararse directamente en la firma de la función.

## Conclusión
El uso de `defaultProps` en React es una práctica esencial para el desarrollo de componentes más robustos, predecibles y mantenibles. Combinado con `PropTypes`, permite establecer contratos claros y detectar problemas en tiempo de desarrollo, contribuyendo a la calidad general del código.
