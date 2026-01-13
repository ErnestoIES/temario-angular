# Guía de instalación de Angular en Windows

## 1. Requisitos previos

- Windows 10 o superior, mínimo 4 GB de RAM y 10 GB de espacio libre en disco.[web:0]
- Tener instalado Node.js y npm en el sistema (desde la web oficial de Node.js).[web:0]


# Instalación de Node.js y npm en Windows

## Requisitos previos

- Windows 10 o 11 de 64 bits actualizado. [web:25]
- Permisos de administrador en el equipo (para ejecutar instaladores). [web:25]

---

## 1. Descargar Node.js para Windows

1. Abre tu navegador en Windows.
2. Ve a la página oficial de descargas de Node.js: https://nodejs.org/en/download [web:26]
3. En la sección de Windows, haz clic en el instalador **Windows Installer (.msi)** de la versión **LTS** (recomendada para la mayoría de usuarios). [web:26][web:17]

---

## 2. Ejecutar el instalador de Node.js

1. Cuando termine la descarga, localiza el archivo `.msi` en tu carpeta de descargas. [web:21]
2. Haz doble clic sobre el archivo para abrir el asistente de instalación. [web:17][web:21]
3. Acepta los términos de la licencia y pulsa **Next** en las pantallas siguientes. [web:21]
4. Deja la ruta de instalación por defecto (por ejemplo, `C:\Program Files\nodejs\`) y pulsa **Next**. [web:21]
5. Asegúrate de que está marcada la opción para instalar también **npm** (viene marcada por defecto en el instalador oficial). [web:17][web:21]
6. Pulsa **Install** y espera a que termine el proceso. [web:21]
7. Al finalizar, pulsa **Finish** para cerrar el asistente. [web:21]

---

## 3. Verificar la instalación de Node.js y npm

1. Abre el **Símbolo del sistema** o **PowerShell** en Windows. [web:19]
2. Escribe y ejecuta el siguiente comando para comprobar la versión de Node.js:  
node -v <br>
Si todo está bien, verás un número de versión (por ejemplo `v20.x.x`). [web:19][web:27]
3. Escribe y ejecuta este comando para comprobar la versión de npm:  
npm -v <br>
Deberías ver otro número de versión, lo que confirma que npm se instaló correctamente. [web:15]  

## 4. Instalar Angular CLI globalmente

1. En **Símbolo del sistema**, ejecuta:  
   npm install -g @angular/cli
2. Verifica que Angular CLI está instalado:  
   ng --version

## 5. Crear un nuevo proyecto Angular

1. Navega a la carpeta donde quieres crear el proyecto:  
   cd ruta\de\tu\carpeta
2. Crea el espacio de trabajo y la aplicación (cambia `my-app` por el nombre que quieras):  
   ng new my-app

## 6. Ejecutar la aplicación en el navegador

1. Entra en el directorio del proyecto:  
   cd my-app
2. Inicia el servidor de desarrollo:  
   ng serve
3. Abre tu navegador y visita:  
   http://localhost:4200/
Deberías ver la aplicación Angular de inicio ejecutándose en Windows.[web:0]
