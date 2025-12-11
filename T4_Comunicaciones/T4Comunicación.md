# Tema 4: Comunicación entre Componentes

## Introducción: El problema del aislamiento

En Angular, cada componente está diseñado para ser una "isla" independiente. Esto es bueno porque evita que el código se mezcle y se vuelva un caos, pero genera un problema: **¿Cómo compartimos información?**

Imagina nuestra **App de Gestión de Tareas**:
1.  Tienes un componente **Padre** (`TaskList`) que posee la lista completa de tareas descargada de la base de datos.
2.  Tienes un componente **Hijo** (`TaskCard`) cuyo único trabajo es pintar bonita **una sola** tarea.

Por defecto, el Hijo no sabe que el Padre existe, y el Padre no sabe qué está pasando dentro del Hijo. Para conectar estas islas, Angular utiliza un patrón estricto llamado **Data Down, Events Up** (Datos hacia abajo, Eventos hacia arriba).

---

## 1. Concepto: La Arquitectura Padre e Hijo

Antes de tocar código, debemos entender los roles. En el desarrollo profesional, rara vez ponemos toda la lógica en un solo archivo. Dividimos:

* **El Componente Inteligente (Smart / Parent):**
    * Suele ser una página o un contenedor grande.
    * Tiene acceso a los servicios y a los datos reales.
    * **Su responsabilidad:** Orquestar y decidir qué pasa cuando un usuario hace algo.
* **El Componente de Presentación (Dumb / Child):**
    * Suele ser un elemento visual pequeño (un botón, una tarjeta, un ítem de lista).
    * No pide datos a la base de datos; espera a que se los den.
    * **Su responsabilidad:** Mostrar datos y avisar si el usuario toca algo.

---

## 2. `@Input()`: Enviando datos hacia abajo (Padre ➡ Hijo)

El decorador `@Input()` es la forma en que un componente hijo abre una "puerta" para que entren datos. Sin `@Input`, las variables de un componente son privadas y nadie desde fuera puede modificarlas.

### ¿Cómo funciona?
Piensa en `@Input()` como un parámetro de una función. Cuando defines una función `sumar(a, b)`, `a` y `b` son inputs. Aquí, las propiedades del componente actúan igual.

### Implementación Paso a Paso

#### A. En el Componente Hijo (`TaskCardComponent`)
Debemos importar `Input` y decorar la propiedad que queremos exponer.

```TypeScript
// task-card.component.ts
import { Component, Input } from '@angular/core';

@Component({
  selector: 'app-task-card',
  standalone: true,
  template: `
    <article class="task-card">
      <h3>{{ title }}</h3>
      <span class="status">{{ isDone ? '✅ Completada' : '⏳ Pendiente' }}</span>
    </article>
  `
})
export class TaskCardComponent {
  // @Input() marca esta propiedad como "rellenable" desde el padre.
  // Si el padre no envía nada, usará el valor por defecto ('Sin título').
  @Input() title: string = 'Sin título';

  // Podemos tener tantos inputs como necesitemos.
  @Input() isDone: boolean = false;
}
```

## B. En el Componente Padre (TaskListComponent)

El padre utiliza el **Property Binding** (los corchetes `[ ]`) para conectar sus datos con los inputs del hijo.

Regla de Oro: Los corchetes `[ ]` significan: "Angular, evalúa lo que hay dentro de las comillas como código (variable), no como texto plano".

```TypeScript 
// task-list.component.ts
import { Component } from '@angular/core';
import { TaskCardComponent } from './task-card.component';

@Component({
  selector: 'app-task-list',
  standalone: true,
  imports: [TaskCardComponent], // ¡Importante importar el componente hijo!
  template: `
    <section>
      <h2>Mis Tareas de Hoy</h2>

      <app-task-card title="Aprender Angular Básico"></app-task-card>

      <app-task-card 
        [title]="tareaActual" 
        [isDone]="estadoTarea">
      </app-task-card>
      
    </section>
  `
})
export class TaskListComponent {
  // Estas son las variables que tiene el padre
  tareaActual = 'Practicar Comunicación entre Componentes';
  estadoTarea = true;
}
```

___

## 3. `@Output()`: Emitiendo eventos hacia arriba (Hijo ➡ Padre)

Aquí es donde muchos principiantes se confunden. **Un componente hijo NUNCA debe modificar los datos del padre directamente.** Si un hijo borra una tarea por su cuenta, el padre no se entera y la aplicación se desincroniza.

En su lugar, el hijo **emite un evento** (como una señal de radio) y el padre, si quiere, lo escucha y actúa.

**Los protagonistas:**

1. `@Output()`: El decorador que expone el evento hacia afuera.

2. `EventEmitter`: La clase que nos permite "disparar" el evento.

**Implementación Paso a Paso**

## A. En el Componente Hijo (`TaskCardComponent`)
Vamos a crear un botón para eliminar la tarea.

```TypeScript
// task-card.component.ts
import { Component, Input, Output, EventEmitter } from '@angular/core';

@Component({
  selector: 'app-task-card',
  standalone: true,
  template: `
    <article>
      <h3>{{ title }}</h3>
      <button (click)="onDeleteClick()">Eliminar</button>
    </article>
  `
})
export class TaskCardComponent {
  @Input() title: string = '';

  // 1. Creamos el "canal" de comunicación.
  // <string> indica que enviaremos un texto (el ID o título) al padre.
  @Output() deleteRequest = new EventEmitter<string>();

  onDeleteClick() {
    console.log('Hijo: El usuario quiere borrar. Avisando al padre...');
    
    // 2. EMITIR: Lanzamos el evento hacia arriba.
    // Lo que pongamos dentro de emit() es el dato que recibirá el padre ($event).
    this.deleteRequest.emit(this.title);
  }
}

```
___

## B.En el Componente padre (`TaskListComponent`)
El padre utiliza el **Event Binding** (los paréntesis `( )`) para escuchar el evento personalizado que acabamos de crear.

El objeto `$event`: Es una palabra reservada en los templates de Angular. Representa el dato exacto que el hijo envió dentro del `.emit()`.
```TypeScript
// task-list.component.ts
import { Component } from '@angular/core';
import { TaskCardComponent } from './task-card.component';

@Component({
  selector: 'app-task-list',
  standalone: true,
  imports: [TaskCardComponent],
  template: `
    <section>
      <app-task-card 
        [title]="'Hacer la compra'"
        (deleteRequest)="borrarTarea($event)">
      </app-task-card>
      
    </section>
  `
})
export class TaskListComponent {

  // Este método se ejecuta SOLO cuando el hijo emite el evento
  borrarTarea(tituloRecibido: string) {
    console.log('Padre: Mensaje recibido.');
    console.log('Elemento a borrar:', tituloRecibido);
    
    // Aquí iría la lógica real para borrarlo de la lista o llamar a la API
    alert('Tarea eliminada: ' + tituloRecibido);
  }
}
```
___ 

## 4. Resumen Visual y Reglas

Para no perderte nunca, recuerda la sintaxis en el HTML del componente Padre:

<table style="width: 100%; border-collapse: collapse; margin: 20px 0; font-family: Arial, sans-serif; font-size: 14px;">
  <thead>
    <tr style="background-color: #005cbb; color: #ffffff; text-align: left;">
      <th style="padding: 12px 15px; border: 1px solid #dddddd;">Símbolo</th>
      <th style="padding: 12px 15px; border: 1px solid #dddddd;">Nombre</th>
      <th style="padding: 12px 15px; border: 1px solid #dddddd;">Dirección</th>
      <th style="padding: 12px 15px; border: 1px solid #dddddd;">Significado</th>
      <th style="padding: 12px 15px; border: 1px solid #dddddd;">Mnemotecnia</th>
    </tr>
  </thead>
  <tbody>
    <tr style="border-bottom: 1px solid #dddddd;">
      <td style="padding: 12px 15px; border: 1px solid #dddddd; text-align: center;">
        <code style="background-color: #f4f4f4; padding: 2px 5px; border-radius: 4px; font-weight: bold; font-size: 1.2em;">[ ]</code>
      </td>
      <td style="padding: 12px 15px; border: 1px solid #dddddd; font-weight: bold;">Property Binding</td>
      <td style="padding: 12px 15px; border: 1px solid #dddddd;">Padre ➡ Hijo</td>
      <td style="padding: 12px 15px; border: 1px solid #dddddd;">"Mete este valor en la propiedad del hijo"</td>
      <td style="padding: 12px 15px; border: 1px solid #dddddd;">Una <strong>Caja [ ]</strong> donde guardas datos.</td>
    </tr>
    <tr style="background-color: #f9f9f9; border-bottom: 1px solid #dddddd;">
      <td style="padding: 12px 15px; border: 1px solid #dddddd; text-align: center;">
        <code style="background-color: #f4f4f4; padding: 2px 5px; border-radius: 4px; font-weight: bold; font-size: 1.2em;">( )</code>
      </td>
      <td style="padding: 12px 15px; border: 1px solid #dddddd; font-weight: bold; color: #333;">Event Binding</td>
      <td style="padding: 12px 15px; border: 1px solid #dddddd; color: #333">Hijo ➡ Padre</td>
      <td style="padding: 12px 15px; border: 1px solid #dddddd; color: #333">"Escucha cuando el hijo grite esto"</td>
      <td style="padding: 12px 15px; border: 1px solid #dddddd; color: #333">Una <strong>Oreja ( )</strong> para escuchar.</td>
    </tr>
  </tbody>
</table>