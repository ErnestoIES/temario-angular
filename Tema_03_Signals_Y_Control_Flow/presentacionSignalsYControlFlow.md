# 🚀 Angular Moderno: Signals & Control Flow

## 1. ¿Qué son los Signals?

Los **Signals** son una primitiva reactiva que envuelve un valor y notifica automáticamente a quienes lo usan cuando ese valor cambia.

Los Signals permiten que Angular sepa **exactamente qué parte de la pantalla debe actualizarse**.

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
