# temario-angular
# 📘 Documentación Colaborativa - Programación Distribuida

Bienvenido al repositorio de la asignatura. El objetivo es crear un "Libro de Texto" digital entre todos.

## 🗺️ Tabla de Asignaciones (El "Índice")

**Consulta esta tabla antes de empezar.** Solo puedes trabajar en el tema que se te ha asignado.

| Tema / Carpeta a crear | Título del Contenido | Grupo Asignado |
| :--- | :--- | :--- |
| `Tema_00_Fundamentos` | Fundamentos Ts y Angular | **TODOS** |
| `Tema_01_Instalación_y_Estructura` | Instalación y estructura | **Grupo 03** |
| `Tema_02_Componentes` | Componentes | **Grupo 02** |
| `Tema_03_Signals_Y_Control_Flow` | Signals y control flow | **Grupo 04** |
| `Tema_04_Comunicacion` | Comunicación (Inputs/Outputs - Mover datos entre componentes). | **Grupo 01** |
| `Tema_05_Inyeccion_de_dependencias` | Inyección de Dependencias y Servicios | **Grupo 03** |
| `Tema_06_Formularios` | Formularios | **Grupo 02** |
| `Tema_07_Enrutamiento` | Enrutamiento | **Grupo 04** |
| `Tema_08_HTTP` | HTTP |  **Grupo 01** |


---

## ⚠️ Normas de Estructura y Estilo

1.  **Exclusividad:** Cada carpeta de tema es "propiedad" de un único grupo. Nadie más debe editar ahí salvo correcciones puntuales.
2.  **Limpieza ("Clean Code"):**
    * **NO** creéis carpetas con el nombre de vuestro grupo (`/Grupo_05/`).
    * El autor se identifica automáticamente mediante el historial de Git (los autores del commit). No firméis los archivos de texto.
3.  **Formato de Archivos:**
    * El archivo principal de vuestro tema debe llamarse `README.md` o `index.md`.
    * Las imágenes deben ir en una subcarpeta llamada `imagenes` dentro de vuestro tema.

---

## 📂 Así debe quedar el Repositorio

Fíjate que **no existen carpetas de grupos**. La estructura es plana, como un libro.

```text
/ (Raíz del Repositorio)
├── README.md (Este archivo)
├── Tema_01_Instalación_y_Estructura/      <-- (Creado por el Grupo asignado)
│   ├── index.md              <-- Desarrollo del temario
│   └── imagenes/              <-- Diagramas de este tema
├── `Tema_02_Componentes`/           <-- (Creado por el siguiente Grupo)
│   ├── index.md
│   ├── codigo_ejemplo.md
│   └── imagenes/
└── ...
