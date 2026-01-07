# Código de Ejemplo - Tema: Componentes

En este documento se detallan los conceptos fundamentales y la implementación moderna de los componentes en Angular (v17+).

## Introducción: El Enfoque "Modern Angular"

El concepto de componente ha evolucionado para reducir el "boilerplate" (código repetitivo) y mejorar el rendimiento:
* **Standalone por defecto:** El componente es autosuficiente y gestiona sus propios imports.
* **Signals:** Reemplazamos los decoradores antiguos por funciones reactivas (`input()`, `output()`, `model()`).
* **Control Flow:** Usamos la nueva sintaxis (`@if`, `@for`) integrada en el lenguaje.

---

## 1. Estructura de un Componente Moderno

Un componente se define mediante el decorador `@Component`. A continuación, un ejemplo completo de un componente de "Tarjeta de Usuario".

### Archivo: `user-card.component.ts`

```typescript
import { Component, input, output, computed } from '@angular/core';

@Component({
  selector: 'app-user-card',
  standalone: true, // 👈 OBLIGATORIO: Define el componente como independiente
  imports: [],      // Aquí importamos otros componentes, directivas (como RouterLink) o Pipes necesarios
  template: `
    <article class="card">
      <h3>{{ fullName() }}</h3>
      
      <div class="status">
        @if (isActive()) {
          <span class="badge active">Activo</span>
        } @else {
          <span class="badge inactive">Inactivo</span>
        }
      </div>

      <button (click)="onAction()">Ver Detalles</button>
    </article>
  `,
  styles: `
    /* Estilos encapsulados por defecto (Shadow DOM Emulation) */
    .card { border: 1px solid #ddd; padding: 1rem; border-radius: 8px; }
    .badge { padding: 4px 8px; border-radius: 4px; font-size: 0.8em; }
    .active { background: #e6fffa; color: #047857; }
    .inactive { background: #fff5f5; color: #c53030; }
  `
})
export class UserCardComponent {
  // --- INPUTS (Entradas) ---
  // Nueva sintaxis basada en Signals. Reemplaza a @Input()
  firstName = input.required<string>(); 
  lastName = input.required<string>();
  isActive = input(false); // Valor por defecto: false

  // --- COMPUTED (Lógica derivada) ---
  // Reemplaza getters complejos. Se recalcula solo si las señales internas cambian.
  fullName = computed(() => `${this.firstName()} ${this.lastName()}`);

  // --- OUTPUTS (Salidas/Eventos) ---
  // Nueva función output(). Reemplaza a @Output() y EventEmitter
  detailsClicked = output<void>();

  onAction() {
    console.log(`Usuario seleccionado: ${this.fullName()}`);
    this.detailsClicked.emit();
  }
}
```

## 2. Desglose de Metadatos

El decorador `@Component` configura cómo Angular instancia la clase.

| Propiedad | Descripción |
| :--- | :--- |
| **`standalone`** | **Debe ser `true`.** Elimina la necesidad de declarar el componente en un `NgModule`. |
| **`selector`** | El nombre de la etiqueta HTML (ej: `<app-user-card>`). Usa siempre `kebab-case`. |
| **`imports`** | **Contexto de compilación.** Si usas directivas (`ngClass`), `RouterLink` o imágenes optimizadas (`NgOptimizedImage`), debes importarlas aquí. |
| **`template`** | Define la vista HTML. Se recomienda `template` (inline) si es corto, y `templateUrl` (archivo externo) si es complejo. |
| **`styles`** | Define el CSS. Los estilos son **encapsulados**, no afectan al resto de la aplicación. |

---

## 3. Funcionamiento y Data Binding (Sincronización)

La comunicación entre la lógica (`.ts`) y la vista (`.html`) en Angular moderno se basa en **Signals**.

* **Interpolación `{{ signal() }}`:**
    * Para leer una señal en el HTML, **siempre** debemos usar paréntesis (invocar la función).
    * *Ejemplo:* `{{ count() }}`.

* **Property Binding `[prop]="signal()"`**:
    * Pasa valores dinámicos a atributos HTML o inputs de otros componentes.
    * *Ejemplo:* `[disabled]="isLoading()"`.

* **Event Binding `(event)="metodo()"`**:
    * Captura eventos del DOM. No cambia con Signals.
    * *Ejemplo:* `(click)="guardar()"`.

---

## 4. Ciclo de Vida del Componente (Lifecycle)

El ciclo de vida gestiona la creación, renderizado y destrucción del componente.

| Hook | Estado Moderno | Uso Recomendado |
| :--- | :--- | :--- |
| **`ngOnInit`** | Activo | Ideal para inicializar datos que no dependen de la vista (ej. llamadas a API). |
| **`ngOnChanges`** | Desaconsejado | Con Signals, usa `computed()` para datos derivados o `effect()` si necesitas efectos secundarios. |
| **`ngOnDestroy Activo | Limpieza de recursos (timers, websockets). *Nota: En v16+ existe `DestroyRef` como alternativa inyectable.* |
| **`ngAfterViewInit`** | Ocasional | Úsalo solo si necesitas manipular el DOM manualmente o inicializar librerías JS externas. |

> **Arquitectura:** Piensa en la aplicación como un árbol de componentes donde los datos fluyen hacia abajo (Inputs) y los eventos fluyen hacia arriba (Outputs).

## 5. Generación Eficiente vía Angular CLI
Aunque comprender el código es vital, en un entorno profesional utilizamos la **Angular CLI** para crear componentes. Esto garantiza consistencia, nomenclatura correcta y estructura de carpetas automática.

**Comando Básico:**
Para generar un componente estándar (con archivos `.html`, `.css` y `.ts` separados):

```bash
ng generate component nombre-del-componente
# O la versión corta (alias):
ng g c nombre-del-componente