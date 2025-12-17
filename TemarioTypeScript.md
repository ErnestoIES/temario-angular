# Módulo 0: Fundamentos de TypeScript (La base de Angular)
___
## 1. ¿Qué es TypeScript y por que lo necesitamos?

Imagina que `JavaScript` es como escribir en una hoja en blanco sin líneas. Puedes escribir en diagonal, en los márgenes o dibujar garabatos. Es muy libre, pero si tienes que escribir un documento oficial (una App profesional), esa libertad se convierte en un caos.

`TypeScript` (TS) es esa misma hoja, pero con `cuadrícula, márgenes y un corrector ortográfico automático.`

`No es un lenguaje nuevo:` TypeScript es "JavaScript con superpoderes".

`El navegador no lo entiende:` Tu navegador (Chrome, Firefox) solo habla JavaScript. TypeScript es una herramienta de desarrollo. Cuando terminas de programar, una herramienta "traduce" tu TS a JS normal (este proceso se llama transpilación, pero piensa en ello como una traducción).

`Su superpoder`: El `Tipado Estático`. Te obliga a definir qué tipo de dato es cada cosa (número, texto, fecha) antes de ejecutar el código.
___
## 2. El concepro de "Tipado"(Las etiquetas)

En JavaScript, tú creas una caja (variable) y puedes meter lo que quieras dentro. En TypeScript, cuando creas una caja, le pegas una `etiqueta` que dice: *"Aquí solo se guardan ZAPATOS"*. Si intentas guardar una CAMISETA, TypeScript te gritará (te dará un error en rojo) antes de que siquiera intentes correr la app.

`TIPOS DE DATOS BÁSICOS`
Vamos a ver los tipos que usaremos el 90% del tiempo en nuestra App de Tareas.

```TypeScript
/**
 * 1. STRING (Texto)
 * Se usa para nombres, descripciones, títulos.
 * Se puede usar comillas simples '' o dobles "".
 */
let nombre: string = 'Angular';
// nombre = 10; // ❌ Error: No puedes meter un número en una variable string.


/**
 * 2. NUMBER (Números)
 * Sirve para enteros (10), decimales (10.5), negativos (-5).
 * No hay diferencia entre "int" o "float" como en otros lenguajes, todos son number.
 */
let edad: number = 25;
let precio: number = 99.99;


/**
 * 3. BOOLEAN (Verdadero o Falso)
 * Fundamental para la lógica: ¿Está la tarea completada? Sí/No.
 */
let estaLogueado: boolean = true;
let tareaCompletada: boolean = false;


/**
 * 4. ANY (El comodín - EVITAR)
 * 'any' significa "cualquier cosa". Es como volver a JavaScript.
 * Úsalo solo cuando sea estrictamente necesario o no sepas qué dato vendrá.
 * En este curso intentaremos NO usarlo.
 */
let datoMisterioso: any = "Hola";
datoMisterioso = 500; // ✅ TypeScript te deja pasar esto, pero es peligroso.

```
___

## 3. Arrays (Listas de cosas)
En nuestra App de Tareas, tendremos una lista de tareas. TypeScript necesita saber qué hay dentro de esa lista.

```TypeScript
// Opción A: Decimos "Esto es una lista DE textos"
let listaDeCompras: string[] = ['Leche', 'Pan', 'Huevos'];

// Opción B: "Esto es una lista DE números"
let precios: number[] = [10, 20, 30];

// ¿Qué pasa si intento meter un número en la lista de compras?
// listaDeCompras.push(100); // ❌ Error: TypeScript protege tu lista.
```
___

## 4. Interfaces: El "Contrato" de tus datos (CRUCIAL!!)
Este es quizás el concepto más importante para Angular.

Imagina que vas a crear una `Tarea` en tu aplicación. Una tarea no es solo un texto; es un objeto complejo que tiene:

1.Un título.

2.Una descripción.

3.Un estado (si está terminada o no).

Para que no creemos tareas deformes o incompletas, creamos una `Interfaz`. Una interfaz es un `molde` o un contrato.

```TypeScript
// Definimos el molde de cómo DEBE lucir una Tarea
interface Tarea {
  id: number;
  titulo: string;
  completada: boolean;
  descripcion?: string; // El signo '?' significa que este dato es OPCIONAL
}

// Ahora creamos una tarea real usando ese molde
const miPrimeraTarea: Tarea = {
  id: 1,
  titulo: "Aprender TypeScript",
  completada: false
  // Como 'descripcion' es opcional, no pasa nada si no la pongo aquí.
};

/*
const tareaInvalida: Tarea = {
  titulo: "Falta el ID",
  completada: "Si" // ❌ Error: 'completada' debe ser boolean, no string.
};
// ❌ Error: Falta la propiedad 'id' que es obligatoria.
*/
```
`Nota de Mentor`: En Angular, usamos Interfaces todo el tiempo para definir cómo son los datos que vienen de una base de datos o API.
___

## 5. Funciones Tipadas

Las funciones son máquinas que reciben datos, hacen algo y (a veces) devuelven un resultado. En TS, debemos etiquetar lo que entra y lo que sale.

```TypeScript
// Función que suma dos números
// (a: number, b: number) -> Son los "inputs" etiquetados
// : number (después del paréntesis) -> Es el tipo de dato que la función "devuelve"
function sumar(a: number, b: number): number {
  return a + b;
}

const resultado = sumar(10, 20); // Correcto
// sumar("hola", 20); // ❌ Error: "hola" no es un número.


// Función que no devuelve nada (Void)
// Muy común en Angular cuando hacemos una acción como "Guardar" o "Click"
function saludar(nombre: string): void {
  console.log("Hola " + nombre);
  // Aquí no hay 'return', por eso es 'void' (vacío).
}
```
___

## 6. Clases (La Fábrica de Objetos)

Angular está construido sobre Clases. Cada componente (página, botón, menú) es una clase. Una clase es como un plano de arquitectura para construir objetos que tienen datos y comportamientos.

```TypeScript
// El plano
class GestorDeTareas {
  // 1. Propiedades (Variables dentro de la clase)
  usuario: string;
  totalTareas: number = 0; // Podemos dar valores iniciales

  // 2. Constructor (Lo primero que se ejecuta al crear la clase)
  constructor(nombreUsuario: string) {
    this.usuario = nombreUsuario;
    console.log("¡Gestor iniciado para " + this.usuario + "!");
  }

  // 3. Métodos (Funciones dentro de la clase)
  agregarTarea(): void {
    this.totalTareas = this.totalTareas + 1;
    console.log("Tarea agregada. Total: " + this.totalTareas);
  }
}

// Usando la clase
const miGestor = new GestorDeTareas("Carlos"); // Imprime: "¡Gestor iniciado para Carlos!"
miGestor.agregarTarea(); // Imprime: "Tarea agregada. Total: 1"
```
___

# Módulo 0: TypeScript Nivel Intermedio
Aquí están las herramientas que distinguen a un novato de alguien que escribe código limpio y moderno.

## 7. Union Types (Tipos de Unión)
A veces, una variable no es solo una cosa, puede ser una de varias opciones. En lugar de usar `string` (que permite cualquier texto), podemos ser más específicos.

Imagina que en nuestra App de Tareas, una tarea puede tener una prioridad.

```TypeScript
// Definimos un tipo personalizado
// La variable solo puede valer EXACTAMENTE una de estas tres palabras.
type Prioridad = 'ALTA' | 'MEDIA' | 'BAJA';

let prioridadTarea: Prioridad;

prioridadTarea = 'ALTA'; // ✅ Correcto
// prioridadTarea = 'URGENTE'; // ❌ Error: 'URGENTE' no está en la lista permitida.
// prioridadTarea = 'alta'; // ❌ Error: TypeScript distingue mayúsculas (case-sensitive).

// También sirve para permitir dos tipos de datos
let id: string | number; // El ID puede ser "123" o 123.
id = 10;    // ✅
id = "A-1"; // ✅
```
___

## 8. Optional Chaining (El Operador Elvis ?.)
Este es el salvavidas de Angular. Imagina que pides los datos de un usuario a un servidor. El dato puede llegar... o quizás tarda un segundo y la variable es null o undefined momentáneamente.

Si intentas leer algo que no existe, tu app explota (pantalla en blanco). El ?. pregunta cortésmente antes de acceder.

```TypeScript
interface Usuario {
  nombre: string;
  direccion?: { // La dirección es opcional
    ciudad: string;
  };
}

const usuario1: Usuario = { nombre: "Ana" }; // No tiene dirección

// Forma peligrosa (JavaScript antiguo)
// console.log(usuario1.direccion.ciudad);
// 💥 CRASH: Intenta leer 'ciudad' de algo que es undefined.

// Forma segura (TypeScript moderno)
console.log(usuario1.direccion?.ciudad);
// ✅ No explota. Simplemente devuelve 'undefined' si dirección no existe.
// Angular ama esto en el HTML: {{ usuario.direccion?.ciudad }}
```
___

## 9. Arrow Functions (Funciones Flecha)
En Angular moderno verás muchas flechas =>. Es una forma más corta de escribir funciones, muy usada cuando filtramos listas o nos suscribimos a datos.

```TypeScript
// Función tradicional
function sumar(a: number, b: number): number {
  return a + b;
}

// Arrow Function (Lo mismo, más corto)
const sumarFlecha = (a: number, b: number): number => {
  return a + b;
};

// Si la función solo tiene una línea, ¡se puede simplificar aún más!
// Se elimina las llaves {} y el 'return' es implícito.
const sumarExpress = (a: number, b: number) => a + b;
```
___

## 10. Manipulación de Arrays (Map y Filter)
En tu Gestor de Tareas, vas a querer:

1.Ver solo las tareas pendientes (Filter).

2.Obtener solo los títulos de las tareas (Map).

Olvídate del bucle `for` tradicional. Esto es programación funcional.

```TypeScript
const tareas = [
  { id: 1, titulo: 'Aprender Angular', completada: true },
  { id: 2, titulo: 'Hacer ejercicio', completada: false },
  { id: 3, titulo: 'Leer un libro', completada: false }
];

// FILTER: Crea un NUEVO array solo con los elementos que cumplan la condición
// "Dame las tareas donde completada sea false"
const tareasPendientes = tareas.filter( t => t.completada === false );
// Resultado: Solo las tareas 2 y 3.

// MAP: Transforma cada elemento del array
// "Dame una lista solo con los TÍTULOS de las tareas"
const soloTitulos = tareas.map( t => t.titulo );
// Resultado: ['Aprender Angular', 'Hacer ejercicio', 'Leer un libro']
```
___

## 11. Spread Operator (Los tres puntos `...`)
Este concepto es **CRÍTICO** para Angular Signals. En el mundo moderno, evitamos modificar los objetos originales (mutabilidad). Preferimos crear copias con cambios (inmutabilidad).

El operador `...` "esparce" las propiedades de un objeto dentro de uno nuevo.

```TypeScript
const tareaOriginal = {
  id: 1,
  titulo: "Aprender TS",
  completada: false
};

// Queremos marcarla como completada, pero SIN tocar la original por seguridad.
// Creamos una copia:
const tareaActualizada = {
  ...tareaOriginal, // 1. Copia todo lo que había en tareaOriginal
  completada: true  // 2. Sobrescribe la propiedad 'completada'
};

/*
tareaActualizada ahora es:
{
  id: 1,
  titulo: "Aprender TS",
  completada: TRUE
}
*/
```

___ 

## 12. Destructuring (Desestructuración)
Una forma elegante de extraer datos de un objeto. Lo verás mucho al importar cosas en Angular.

```TypeScript
const configuracion = {
  url: 'https://api.miservidor.com',
  puerto: 8080,
  timeout: 5000
};

// Forma antigua
// const url = configuracion.url;
// const puerto = configuracion.puerto;

// Forma Pro (Destructuring)
// "Extrae 'url' y 'puerto' del objeto configuracion y crea variables con ese nombre"
const { url, puerto } = configuracion;

console.log(url); // 'https://api.miservidor.com'
```

