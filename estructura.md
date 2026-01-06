# Guía de Estructura de Archivos en Angular

En Angular, todo tiene un lugar específico. Esta organización permite que los proyectos sean escalables y fáciles de mantener. A continuación, desglosamos las carpetas y archivos más importantes.

---

## 1. La Raíz del Proyecto (Configuración)
Estos archivos controlan el comportamiento global y las herramientas de desarrollo.

* **`angular.json`**: El archivo de configuración maestro. Aquí se define cómo se construye la app, qué archivos CSS son globales y dónde se guardan los archivos finales.
* **`package.json`**: El "inventario" de tu proyecto. Contiene todas las librerías (dependencias) instaladas y los comandos (`scripts`) para arrancar o probar la aplicación.
* **`tsconfig.json`**: Configuración de TypeScript. Establece las reglas sobre cómo se traduce el código TS a JavaScript para que el navegador lo entienda.
* **`.editorconfig` / `.gitignore`**: Archivos auxiliares para mantener el código limpio y decidir qué archivos no se deben subir a GitHub (como la pesada carpeta `node_modules`).

---

## 2. La Carpeta `/src` (Código Fuente)
Es donde vive el 100% de tu código de aplicación.

* **`main.ts`**: Es el **punto de entrada**. Es el primer archivo que se ejecuta; se encarga de "encender" Angular y cargar el componente inicial.
* **`index.html`**: La única página HTML real. Es un archivo muy simple que contiene una etiqueta especial (ej. `<app-root></app-root>`) donde se inyectará toda tu aplicación de forma dinámica.
* **`styles.css`**: Aquí se definen los estilos globales que afectarán a toda la aplicación (fuentes, colores de fondo, márgenes base).

---

## 3. La Carpeta `/src/app` (El Corazón)
Aquí es donde creas la lógica de tu negocio. En las versiones modernas de Angular (Standalone), los archivos clave son:

### El Componente Raíz (`app.component.*`)
Es el padre de todos los demás componentes. Se divide en:
* **`app.component.ts`**: La lógica (clase de TypeScript, variables, funciones).
* **`app.component.html`**: La estructura visual de la página principal.
* **`app.component.css`**: Estilos que **solo** afectan a este componente.
* **`app.component.spec.ts`**: Archivo para pruebas unitarias automatizadas.

### Configuración y Rutas
* **`app.routes.ts`**: El "mapa" de navegación. Aquí defines qué componente se muestra según la URL (ej: `/home` -> `HomeComponent`).
* **`app.config.ts`**: Configuración global de servicios (como el envío de datos por HTTP o animaciones).

---

## 4. Carpetas de Recursos Estáticos
* **`/public` (o `/assets` en versiones anteriores)**: Aquí guardas fotos, iconos, fuentes personalizadas o archivos JSON que tu aplicación necesite cargar tal cual, sin ser procesados por Angular.

---

## 5. Resumen de Flujo
1. El navegador carga el **`index.html`**.
2. **`main.ts`** arranca Angular.
3. Angular busca la etiqueta `<app-root>` y mete dentro el contenido de **`app.component.html`**.
4. El **Router** decide qué otros componentes mostrar dentro de la página principal según la dirección URL.