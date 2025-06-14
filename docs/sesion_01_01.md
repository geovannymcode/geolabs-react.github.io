# Inicio Profesional de Proyectos React: Vite vs Create React App

## Introducción

Esta sección marca el comienzo del desarrollo práctico de aplicaciones con React, enfocándose en la creación de proyectos utilizando dos enfoques populares: **Vite** y **Create React App (CRA)**. El objetivo es proporcionar una visión clara de sus procesos de inicialización, configuración y rendimiento comparativo.

---

## Configuración del Entorno

1. Abrir la terminal o consola de comandos.
2. Navegar al directorio de trabajo con `cd`.
3. Se parte de una estructura que incluye carpetas como `01-intro-javascript`.

---

## Creación de Proyecto con Vite

Para iniciar un nuevo proyecto con **Vite**, ejecutar el siguiente comando:

```bash
npm create vite@latest
```

A continuación:
- Asignar como nombre del proyecto: `counter-app-vite`.
- Seleccionar la plantilla de desarrollo: **Vanilla JavaScript**.
- Renombrar el directorio generado como `02-counter-app-vite`.
- Ingresar al directorio del proyecto:

```bash
cd 02-counter-app-vite
```

- Instalar las dependencias utilizando:

```bash
yarn install
```

- Abrir el proyecto en un entorno de desarrollo como Visual Studio Code.

### Beneficios de Vite

- Compilación significativamente más rápida.
- Flujo de trabajo más moderno y eficiente.
- Ideal para proyectos de cualquier escala con necesidad de rendimiento óptimo.

---

## Creación de Proyecto con Create React App (CRA)

Para comparar con la alternativa tradicional, se utiliza **Create React App** mediante el comando:

```bash
npx create-react-app counter-app-cra
```

Una vez finalizado:
- Renombrar el proyecto a `02-counter-app-cra`.
- Abrirlo en el editor de código.

### Consideraciones

- CRA instala múltiples herramientas y configuraciones por defecto (testing, ESLint, etc.).
- Tiempos de instalación y arranque inicial más elevados.
- Proporciona una estructura predefinida sólida para nuevos desarrolladores.

---

## Comparación Técnica

| Característica           | Vite                        | Create React App              |
|--------------------------|-----------------------------|-------------------------------|
| Tiempo de configuración  | Inmediato                   | Proceso más extenso           |
| Dependencias iniciales   | Mínimas                     | Amplia preconfiguración       |
| Compilador               | nativo ESBuild              | Webpack                       |
| Performance              | Óptimo desde el inicio      | Puede requerir ajustes        |
| Ideal para               | Proyectos modernos y ágiles | Entornos más tradicionales    |

---

## Conclusión

Tanto **Vite** como **Create React App** son herramientas válidas para iniciar un proyecto con React. Sin embargo, **Vite se destaca por su velocidad, simplicidad y eficiencia**, mientras que **CRA ofrece un entorno más completo, aunque más pesado**, orientado a usuarios que prefieren una configuración más tradicional y robusta.

En las próximas sesiones se realizará un análisis detallado de la estructura de carpetas y archivos generados por ambas herramientas, con el fin de comprender su funcionamiento interno y mejores prácticas de uso.
