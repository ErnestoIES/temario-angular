# Ejemplo de Código: Formulario Reactivo en Angular

Este ejemplo muestra cómo crear un formulario de registro completo utilizando **Reactive Forms**. Incluye validaciones personalizadas y manejo de errores.

## 1. Configuración del Componente (`registro.component.ts`)

Primero, definimos la lógica en nuestro componente.

```typescript
import { Component } from '@angular/core';
import { CommonModule } from '@angular/common';
import { ReactiveFormsModule, FormBuilder, FormGroup, Validators, AbstractControl } from '@angular/forms';

@Component({
  selector: 'app-registro',
  standalone: true,
  imports: [CommonModule, ReactiveFormsModule],
  templateUrl: './registro.component.html',
  styleUrls: ['./registro.component.css']
})
export class RegistroComponent {
  registroForm: FormGroup;

  constructor(private fb: FormBuilder) {
    // Creamos el grupo de formulario con sus controles y validaciones
    this.registroForm = this.fb.group({
      nombre: ['', [Validators.required, Validators.minLength(3)]],
      email: ['', [Validators.required, Validators.email]],
      password: ['', [Validators.required, Validators.minLength(6)]],
      confirmPassword: ['', [Validators.required]]
    }, { validators: this.passwordMatchValidator }); // Validación a nivel de grupo
  }

  // Validador personalizado para verificar que las contraseñas coincidan
  passwordMatchValidator(control: AbstractControl) {
    const password = control.get('password')?.value;
    const confirmPassword = control.get('confirmPassword')?.value;

    if (password !== confirmPassword) {
      control.get('confirmPassword')?.setErrors({ passwordMismatch: true });
      return { passwordMismatch: true };
    } else {
      return null;
    }
  }

  // Método para enviar el formulario
  onSubmit() {
    if (this.registroForm.valid) {
      console.log('Formulario enviado:', this.registroForm.value);
      alert('¡Registro exitoso!');
    } else {
      console.log('Formulario no válido');
      this.registroForm.markAllAsTouched(); // Marca todos los campos para mostrar errores
    }
  }

  // Getters para facilitar el acceso en el HTML
  get nombre() { return this.registroForm.get('nombre'); }
  get email() { return this.registroForm.get('email'); }
  get password() { return this.registroForm.get('password'); }
  get confirmPassword() { return this.registroForm.get('confirmPassword'); }
}
```

### Explicación del Código TypeScript:
- **FormBuilder**: Servicio que facilita la creación de instancias de `FormGroup` y `FormControl`.
- **Validators**: Utilizamos validadores integrados como `required`, `minLength` y `email`.
- **passwordMatchValidator**: Una función personalizada que compara los valores de `password` y `confirmPassword`.
- **Getters**: Métodos cortos para acceder a los controles desde la plantilla HTML de forma más limpia.

---

## 2. Plantilla HTML (`registro.component.html`)

Ahora conectamos el modelo con la vista.

```html
<div class="container">
  <h2>Registro de Usuario</h2>
  
  <form [formGroup]="registroForm" (ngSubmit)="onSubmit()">
    
    <!-- Campo Nombre -->
    <div class="form-group">
      <label for="nombre">Nombre:</label>
      <input id="nombre" type="text" formControlName="nombre" class="form-control">
      
      <!-- Mensajes de Error -->
      <div *ngIf="nombre?.invalid && (nombre?.dirty || nombre?.touched)" class="error">
        <div *ngIf="nombre?.errors?.['required']">El nombre es obligatorio.</div>
        <div *ngIf="nombre?.errors?.['minlength']">El nombre debe tener al menos 3 caracteres.</div>
      </div>
    </div>

    <!-- Campo Email -->
    <div class="form-group">
      <label for="email">Email:</label>
      <input id="email" type="email" formControlName="email" class="form-control">
      
      <div *ngIf="email?.invalid && (email?.dirty || email?.touched)" class="error">
        <div *ngIf="email?.errors?.['required']">El email es obligatorio.</div>
        <div *ngIf="email?.errors?.['email']">Introduce un email válido.</div>
      </div>
    </div>

    <!-- Campo Contraseña -->
    <div class="form-group">
      <label for="password">Contraseña:</label>
      <input id="password" type="password" formControlName="password" class="form-control">
      
      <div *ngIf="password?.invalid && (password?.dirty || password?.touched)" class="error">
        <div *ngIf="password?.errors?.['required']">La contraseña es obligatoria.</div>
        <div *ngIf="password?.errors?.['minlength']">Mínimo 6 caracteres.</div>
      </div>
    </div>

    <!-- Campo Confirmar Contraseña -->
    <div class="form-group">
      <label for="confirmPassword">Confirmar Contraseña:</label>
      <input id="confirmPassword" type="password" formControlName="confirmPassword" class="form-control">
      
      <div *ngIf="confirmPassword?.invalid && (confirmPassword?.dirty || confirmPassword?.touched)" class="error">
        <div *ngIf="confirmPassword?.errors?.['required']">Confirma tu contraseña.</div>
        <div *ngIf="confirmPassword?.errors?.['passwordMismatch']">Las contraseñas no coinciden.</div>
      </div>
    </div>

    <button type="submit" [disabled]="registroForm.invalid">Registrarse</button>
  </form>
</div>
```

### Explicación del HTML:
- **[formGroup]**: Vincula el elemento `<form>` con la propiedad `registroForm` de la clase.
- **formControlName**: Vincula cada `<input>` con su control correspondiente en el `FormGroup`.
- **Validación Visual**: Usamos `*ngIf` para mostrar mensajes de error solo si el campo es inválido y ha sido tocado (`touched`) o modificado (`dirty`).
- **Botón Submit**: Se deshabilita si el formulario es inválido (`registroForm.invalid`).
