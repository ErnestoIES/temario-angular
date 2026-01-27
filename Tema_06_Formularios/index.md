# Tutorial de Formularios en Angular

Angular ofrece dos estrategias fundamentales para trabajar con formularios. Elegir la correcta depende de la complejidad de tu caso de uso.

## 1. Template-driven Forms (Formularios dirigidos por plantillas)

Son la forma más "natural" si vienes de AngularJS (Angular 1) o si solo necesitas un formulario simple (ej. un login básico). La lógica reside en el HTML.

### ¿Cómo funcionan?
Angular crea automáticamente el modelo del formulario basándose en las directivas que pones en tu HTML.

### Ejemplo Rápido
```html
<!-- La variable #f nos da acceso al objeto NgForm -->
<form #f="ngForm" (ngSubmit)="onSubmit(f)">
  
  <!-- 'ngModel' registra este input en el formulario. 'name' es OBLIGATORIO. -->
  <input type="text" name="nombre" ngModel required>
  
  <button type="submit" [disabled]="!f.valid">Enviar</button>
</form>
```

---

## 2. Reactive Forms (Formularios Reactivos)

Aquí tomas el control total. Defines la estructura del formulario en tu código TypeScript (la clase) y luego la enlazas al HTML. Son ideales para formularios complejos, dinámicos o que requieren validaciones cruzadas.

### Ejemplo Rápido
**TypeScript:**
```typescript
// Creas el control explícitamente
emailControl = new FormControl('', Validators.required);
```

**HTML:**
```html
<!-- Enlazas el control creado en TS al input -->
<input type="text" [formControl]="emailControl">
```

---

## 3. Conceptos Clave de UX (Experiencia de Usuario)

Esta sección explica la terminología vital para manejar cuándo mostrar errores.

### Los Estados del Control (The State Flags)
Angular rastrea el estado de cada campo con propiedades booleanas.

| Estado | Significado | ¿Cuándo usarlo? |
| :--- | :--- | :--- |
| **`touched`** | El usuario puso el foco y salió. | Para no mostrar errores antes de que el usuario interactúe. |
| **`dirty`** | El usuario modificó el valor. | Para habilitar botones de "Guardar cambios". |
| **`valid`** | Cumple todas las reglas. | Para habilitar el botón de envío. |

> **Regla de Oro:** Muestra errores así: `*ngIf="control.invalid && (control.dirty || control.touched)"`.

---

## 4. Arquitectura Moderna y Avanzada (Angular 14+ / 17+)

Aquí entramos en lo que realmente diferencia a un junior de un senior en Angular. Estos conceptos son obligatorios en versiones modernas.

### A. Typed Forms (Formularios Tipados)
**El problema:** Antes de Angular 14, los formularios eran de tipo `any`. Podías escribir `form.value.passwrd` (con error tipográfico) y Angular no te avisaba, causando errores en tiempo de ejecución.

**La solución:** Ahora los formularios son estrictamente tipados. Debes definir qué tipo de dato contiene cada control.

#### Nullable vs NonNullable
Por defecto, un control puede ser `null` si se resetea (`reset()`). Si quieres evitar `null`, usa `nonNullable`.

```typescript
// Definición de una interfaz para el formulario
interface PerfilForm {
  nombre: FormControl<string | null>; // Puede ser string o null
  edad: FormControl<number>;          // Nunca será null (nonNullable)
}

// Implementación
const perfilForm = new FormGroup<PerfilForm>({
  nombre: new FormControl(''), // Infiere string | null
  edad: new FormControl(18, { nonNullable: true }) // Infiere number (sin null)
});

// BENEFICIO:
// perfilForm.value.nombre -> TypeScript sabe que es string | null
// perfilForm.value.edad   -> TypeScript sabe que es number
// perfilForm.value.coche  -> ERROR DE COMPILACIÓN (¡Te salva de bugs!)
```

### B. FormArray (Formularios Dinámicos)
Este es un clásico en pruebas técnicas. Se usa cuando no sabes cuántos campos habrá. Ejemplo: "¿Añadir otro teléfono?".

**Concepto:** Es un array de controles que crece o decrece dinámicamente.

```typescript
// 1. Crear el FormArray (vacío al inicio)
telefonos = new FormArray([]);

// 2. Método para añadir dinámicamente
agregarTelefono() {
  const nuevoTelefono = new FormControl('', Validators.required);
  this.telefonos.push(nuevoTelefono);
}

// 3. Método para eliminar
borrarTelefono(indice: number) {
  this.telefonos.removeAt(indice);
}
```

**En el HTML (Iteración):**
```html
<div *ngFor="let tel of telefonos.controls; let i = index">
  <!-- Importante: usar [formControl] con el índice -->
  <input [formControl]="telefonos.at(i)" placeholder="Teléfono {{i + 1}}">
  <button (click)="borrarTelefono(i)">Eliminar</button>
</div>
<button (click)="agregarTelefono()">+ Añadir Teléfono</button>
```

### C. Validaciones Personalizadas (Custom Validators)
A veces `Validators.required` no basta. ¿Cómo validas un DNI o que una palabra no sea ofensiva?

**Concepto:** Un validador es simplemente una función que recibe un control y devuelve:
*   `null`: Si todo está **BIEN**.
*   `Objeto`: Si algo está **MAL** (ej. `{ dniInvalido: true }`).

```typescript
// Ejemplo: Validador que prohíbe la palabra "admin"
function noAdminValidator(control: AbstractControl): ValidationErrors | null {
  const valor = control.value;
  if (valor === 'admin') {
    return { usuarioProhibido: true }; // Error
  }
  return null; // Todo OK
}

// Uso:
this.miForm = this.fb.group({
  usuario: ['', [Validators.required, noAdminValidator]]
});
```

#### Validación Cruzada (Cross-Field Validation)
El clásico "Repetir Contraseña". Aquí no validas un campo aislado, sino la relación entre dos. **Se aplica al FormGroup entero**, no a un control individual.

```typescript
const form = this.fb.group({
  pass: [''],
  confirmPass: ['']
}, { validators: passwordMatchValidator }); // <-- Se pasa como 2º argumento del grupo

function passwordMatchValidator(group: AbstractControl) {
  const pass = group.get('pass')?.value;
  const confirm = group.get('confirmPass')?.value;
  
  return pass === confirm ? null : { noCoinciden: true };
}
```

---

## Comparación Resumida

| Característica | Template-driven | Reactive |
| :--- | :--- | :--- |
| **Lógica** | En el HTML | En el TypeScript |
| **Tipado (Typed Forms)** | Débil | **Estricto y seguro** |
| **Dinamismo (FormArray)** | Muy difícil | **Nativo y sencillo** |
| **Testing** | Complicado | Muy fácil |

---

## Ejemplo Práctico Completo

Para ver cómo se unen todas estas piezas (validaciones, estados `touched`/`dirty`, y `FormBuilder`) en un código real:

[Ver Ejemplo de Código Explicado](./ejemplo_codigo_formularios.md)
