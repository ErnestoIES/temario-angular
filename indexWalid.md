# TypeScript y Angular Moderno --- Explicación Extendida

## 1. Tipado Estático e Inferencia en TypeScript

TypeScript cambia el juego: introduce tipado estático, es decir, el tipo de cada variable se mantiene a lo largo de todo el programa. Esto permite:

	•	Detectar errores antes de ejecutar la app.
	•	Autocompletado más inteligente en el editor.
	•	Mayor seguridad, porque tu código será más predecible.
	•	Mejor lectura y mantenimiento, ya que cada dato tiene un propósito definido.

### Inferencia de tipos

No siempre necesitas decirle a TS “esto es un string o un number”.
TypeScript analiza tu código y deduce el tipo automáticamente.

Esto se llama *type inference*

``` ts
// TS infiere automáticamente que userName es string
const userName = "Carlos";

// Si luego intento hacer:
userName = 10; // ❌ Error: userName debe ser string
```

❗ Regla de Oro

Evita el tipo any.

any desactiva la seguridad de TypeScript.
Es literalmente volver a JavaScript.

``` ts
let data: any = "Hola";
data = 123; // TS no se entera: ❌ mala práctica total
```

✔ Cuando sí declarar el tipo

Cuando declaras una variable sin valor inicial, TS no puede inferir:

``` ts
let totalAmount: number;
```

------------------------------------------------------------------------

## 2. Interfaces para Modelos de Datos

Una interfaz define la estructura exacta que debe tener un objeto.
En Angular, esto es vital por varias razones:

	•	Evitas objetos “misteriosos” que no sabes qué contienen.
	•	El componente padre e hijo hablan el mismo idioma gracias a interfaces.
	•	Ayuda al autocompletado e incluso a documentar tu aplicación de forma natural.
	•	Previene bugs en servicios HTTP al saber exactamente qué devuelve la API.

``` ts
// models/user.interface.ts
export interface User {
  id: number;
  name: string;
  email: string;
  isActive?: boolean; // El ? indica que es opcional
}
```

Ahora, cualquier componente o servicio que use un User tiene garantizado que esos campos existen.

Sin interfaces → caos.

Con interfaces → arquitectura profesional.

------------------------------------------------------------------------

## 3. Genéricos en Signals y Inputs

Los genéricos son uno de los conceptos más poderosos de TypeScript.
Son como “plantillas de tipos”: puedes crear estructuras flexibles sin perder seguridad.

En Angular moderno:

	•	signal<T>() necesita saber qué tipo maneja para darte autocompletado.
	•	input.required<T>() garantiza que un padre pasará un dato EXACTAMENTE del tipo correcto.

``` ts
const users = signal<User[]>([]); 
```

Aquí:

	•	User[] significa “array de usuarios”.
	•	Si intentas añadir un producto o un número → TS te frena.

```ts 
const profile = input.required<User>();
```

Esto le dice a Angular:

	•	Este input debe existir siempre.
	•	Debe ser un User válido.
	•	Si falta o el tipo está mal → error en compilación.

------------------------------------------------------------------------

## 4. Inyección de Dependencias con inject()

Angular 16+ introdujo inject(), reemplazando la inyección por constructor.

Antes (manera verbosa):

``` ts
constructor(private userService: UserService) {}
```

Ahora:

``` ts
private readonly userService = inject(UserService);
```
Ventajas:

	•	Código más limpio.
	•	Más fácil de leer.
	•	Permite usar servicios fuera de clases (factories o funciones puras).
	•	Menos líneas y más claridad.

Además, TypeScript sabe que userService es exactamente un UserService, dándote:

	•	Autocompletado.
	•	Tipos estrictos.
	•	Seguridad en tiempo de compilación.
------------------------------------------------------------------------

## 5. Control de Flujo Tipado (@if, @for)

Angular 17 introdujo un nuevo sistema de control de flujo:
•	@if
•	@for
•	@switch

Estos funcionan mejor que nunca con tipos estrictos.

Si una variable puede ser null, Angular te obliga a manejar ese caso, evitando errores del tipo:

“Cannot read properties of undefined”.

Ejemplo:

``` ts
@if (user()) {
  <p>{{ user().name }}</p>
}
```
Angular sabe que dentro del @if, user() NO es null.

------------------------------------------------------------------------

## Ejemplo Completo: UserCardComponent

❌ Mentalidad antigua (JavaScript)

``` ts
@Input() data; // Puede venir cualquier cosa
```
Esto rompe la arquitectura

✅ Mentalidad Angular 18+ profesional
```ts 
interface UserProfile {
  id: string;
  username: string;
  avatarUrl: string;
  role: 'admin' | 'user' | 'guest';
}
```
Componente:
```ts
@Component({
  ...
})
export class UserCardComponent {
  user = input.required<UserProfile>();

  isAdmin = computed(() => this.user().role === 'admin');
}
```

Beneficios:

	•	Si el padre pasa un objeto incompleto → error.
	•	Si accedes a una propiedad inexistente → error.
	•	Reutilizable, escalable, mantenible.

------------------------------------------------------------------------

## Arrow Functions y Tipado de Funciones

1. Diferencia JS vs TS

JS permite funciones sin definir tipos:

``` ts
const sumar = (a, b) => a + b;
```

TS exige claridad:

``` ts
const sumar = (a: number, b: number): number => a + b;
```

Ventajas:

	•	Sabes qué puede entrar.
	•	Sabes qué va a salir.
	•	Previene comportamientos inesperados.

2. Por qué son vitales con Signals

Las señales de Angular usan muchísimo funciones flecha:

```ts 
count = signal<number>(0);

double = computed(() => this.count() * 2);
```
Las arrow functions:

	•	Capturan this automáticamente.
	•	Son más cortas.
	•	Evitan bugs con el contexto del componente.

3. Buenas prácticas con el tipo de retorno

Ser explícito en funciones importantes:

```ts
const formatName = (user: User): string => {
  return `${user.firstName} ${user.lastName}`;
};
```
Esto evita que un cambio interno rompa tu aplicación.

------------------------------------------------------------------------

## Resumen General

TypeScript + Angular moderno (17/18+) se basa en:

	•	Tipado estricto → código más seguro.
	•	Interfaces → contratos claros.
	•	Genéricos → flexibilidad sin perder seguridad.
	•	Signals → reactividad más intuitiva.
	•	inject() → inyección más limpia.
	•	Nuevo control de flujo → plantillas más inteligentes.
	•	Funciones flecha → contexto automático y tipado robusto.

Angular moderno + TypeScript bien aplicado → código robusto, mantenible
y profesional.
