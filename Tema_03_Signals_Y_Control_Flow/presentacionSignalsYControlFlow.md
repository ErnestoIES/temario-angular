# Angular Moderno: Signals & Control Flow

## 1. ¿Qué son los Signals?

Los **Signals** son una primitiva reactiva que envuelve un valor y notifica automáticamente a quienes lo usan cuando ese valor cambia.

Los Signals permiten que Angular sepa **exactamente qué parte de la pantalla debe actualizarse**.

### Tipos 
### 1.Signals de Escritura)
Son las "señales base". Tú tienes el control total sobre ellas: puedes leer su valor, pero también cambiarlo directamente.

Cómo se crean: miSignal = signal(valorInicial);

Sus herramientas:

.set(nuevoValor): Reemplaza el valor por completo.

.update(v => v + 1): Cambia el valor basándose en el anterior.


### 2. Computed Signals (Señales Computadas)
Son señales que dependen de otras. Su valor se calcula automáticamente a partir de otros Signals.
+1

Cómo se crean: doble = computed(() => miSignal() * 2);

### 3. Los "Efectos" (effect)
Aunque técnicamente no son un "tipo de dato" que guarde un valor para mostrar en el HTML, son parte esencial del ecosistema.

Un effect es una función que se ejecuta cada vez que uno o más Signals dentro de él cambian.

¿Para qué sirven?

Sincronizar datos con el localStorage.

Hacer logs (imprimir en consola) para depurar.

### Eficiencia
Angular **no revisa toda la aplicación**, solo el componente que *escucha* el Signal.

###  Ejemplo: Funcionamiento de un Signal

```ts
import { Component, signal, ChangeDetectionStrategy } from '@angular/core';

@Component({
  selector: 'app-ejemplo-signal',
  standalone: true,
  template: `
    <h1>Contador: {{ contador() }}</h1> 
    <button (click)="incrementar()">Incrementar</button>
  `,
  
})
export class EjemploComponent {
  // Creamos el Signal (como un "timbre" inteligente)
  contador = signal(0);

  incrementar() {
    // .update() cambia el valor y dispara la notificación automáticamente
    this.contador.update(valor => valor + 1); 
  }
}
```

## 2. ¿Qué es el Control Flow?

El Control Flow es la nueva sintaxis nativa de Angular para manejar la lógica del HTML (condicionales y bucles).

## Sustitución

Reemplaza a las antiguas directivas:

*ngIf

*ngFor

## Rendimiento

Hasta un 90% más rápido en el renderizado de listas.

## Simplicidad

Usa bloques @

Más legible

No requiere imports adicionales

## Ejemplo: Uso de @if y @for
```ts
import { Component, signal } from '@angular/core';

@Component({
  selector: 'app-ejemplo-flow',
  standalone: true,
  template: `
    <div>
      <h3>Gestión de Usuarios</h3>

      @if (cargando()) {
        <p>Cargando datos...</p>
      } @else {
        <ul>
          @for (user of usuarios(); track user.id) {
            <li>{{ user.nombre }}</li>
          } @empty {
            <li>No hay usuarios registrados</li>
          }
        </ul>
      }
    </div>
  `
})
export class ControlFlowComponent {
  cargando = signal(false);
  usuarios = signal([
    { id: 1, nombre: 'Angular Expert' },
    { id: 2, nombre: 'GDE Student' }
  ]);
}

```

## Tabla de diferencias entre Angular Antiguo y Angular Moderno

| Característica | Antes (Legacy)           | Ahora (Modern Angular)         |
| -------------- | ------------------------ | ------------------------------ |
| Reactividad    | Zone.js (revisión total) | Signals (notificación directa) |
| Sintaxis HTML  | `*ngIf`, `*ngFor`        | `@if`, `@for`                  |
| Rendimiento    | Estándar                 | Optimizado / Granular          |
| Boilerplate    | Alto (imports manuales)  | Mínimo (nativo en el motor)    |
