# TEMA 5: Inyección de Dependencias y Servicios

> **Resumen Ejecutivo:** Documentación técnica sobre el sistema de Inyección de Dependencias (DI) de Angular. Cubre desde los fundamentos del patrón de diseño, la creación y configuración de servicios, hasta el uso avanzado de `InjectionToken` y jerarquías de inyectores.

---

## 1. Filosofía: Servicios vs Componentes

Angular promueve una separación estricta de responsabilidades (SRP).
* **Componentes (UI):** Se encargan EXCLUSIVAMENTE de renderizar la vista y capturar eventos del usuario. Son efímeros y están atados al DOM.
* **Servicios (Lógica):** Contienen la lógica de negocio, acceso a datos y validaciones. Son clases reutilizables y desacopladas de la vista.

---

## 2. El Patrón: Inyección de Dependencias (DI)

La Inyección de Dependencias es un patrón de diseño donde las dependencias de una clase (ej: un servicio HTTP, un validador) son suministradas externamente en lugar de ser creadas por la propia clase.

### ❌ El Problema: Acoplamiento Fuerte
Sin DI, un componente crearía sus propias dependencias:
```typescript
class UserComponent {
    // Mal: El componente necesita saber cómo crear un UserService
    private userService = new UserService(new HttpClient()); 
}
```
Esto hace que el código sea difícil de probar (no se puede mockear) y difícil de mantener (si cambia el constructor del servicio, rompe el componente).

### ✅ La Solución Angular
Angular actúa como un contenedor IoC (Inversion of Control). Tú declaras qué necesita tu componente, y Angular se encarga de buscarlo o crearlo y entregártelo.

---

## 3. Creación y Configuración de Servicios

### El Decorador @Injectable
Para que una clase sea un servicio gestionado por Angular, debe tener el decorador `@Injectable`.

```typescript
import { Injectable } from '@angular/core';

@Injectable({
  providedIn: 'root' // 👈 Configuración de Disponibilidad
})
export class ProductService {
  getProducts() {
    return [{ id: 1, name: 'Laptop' }];
  }
}
```

### El Concepto de Singleton (`providedIn: 'root'`)
* Al usar `providedIn: 'root'`, Angular crea una **única instancia compartida** del servicio para toda la aplicación (Singleton).
* Esto permite compartir datos y estado entre componentes diferentes (ej: un carrito de compras).
* También habilita el "Tree Shaking": si el servicio no se usa, no se incluye en el bundle final.

---

## 4. Inyección (Consumo) de Servicios

Existen dos formas de solicitar una dependencia en un componente u otro servicio.

### A. Función `inject()` (Moderno - Recomendado)
Desde Angular 14+, podemos usar la función `inject` para obtener dependencias de forma más limpia, incluso fuera de clases (en funciones guard, interceptors, etc.).

```typescript
import { Component, inject, OnInit } from '@angular/core';
import { ProductService } from './services/product.service';

@Component({ ... })
export class ProductListComponent implements OnInit {
  // Solicitamos la dependencia
  private productService = inject(ProductService);
  
  products: any[] = [];

  ngOnInit() {
    this.products = this.productService.getProducts();
  }
}
```

### B. Inyección por Constructor (Clásico)
La forma tradicional es declarar la dependencia como parámetro del constructor. Angular infiere el token basado en el tipo.

```typescript
export class ProductListComponent {
  constructor(private productService: ProductService) {}
}
```

---

## 5. Jerarquía de Inyectores y Resolución

El sistema de DI de Angular es jerárquico. Cuando pides una dependencia, Angular la busca en el siguiente orden:

1.  **Node Injector:** El inyector del componente o directiva actual.
2.  **Parent Injector:** Sube por la estructura del DOM (componente padre, abuelo...).
3.  **EnvironmentInjector (Root):** El inyector global de la aplicación (donde viven los `providedIn: 'root'`).
4.  **NullInjector:** El final de la línea. Si no se encuentra, lanza `NullInjectorError`.

### Proveedores a Nivel de Componente
Si registras un servicio en el array `providers` de un componente, **se crea una nueva instancia exclusiva para ese componente y sus hijos**.

```typescript
@Component({
  selector: 'app-editor',
  providers: [ValidationService] // 👈 Nueva instancia por cada <app-editor>
})
export class EditorComponent { ... }
```
Esto es útil para servicios que deben manejar estado aislado (ej: un servicio para un formulario específico que puede aparecer múltiples veces en pantalla).

---

## 6. Nivel Avanzado: InjectionTokens

A veces la dependencia no es una clase, sino una configuración, una string (API URL), o una interfaz (que desaparece en tiempo de ejecución). Para esto usamos `InjectionToken`.

### Crear el Token
```typescript
import { InjectionToken } from '@angular/core';

export const API_CONFIG = new InjectionToken<string>('API_CONFIG');
```

### Proveer el Valor
En el `app.config.ts` o módulo raíz:
```typescript
providers: [
  { provide: API_CONFIG, useValue: 'https://api.miempresa.com' }
]
```

### Inyectar el Token
```typescript
export class DataService {
  private apiUrl = inject(API_CONFIG); // Recibe el string
}
```

### Factory Providers (`useFactory`)
Permite crear dependencias dinámicas o que dependen de otros servicios.

```typescript
{
  provide: API_CONFIG,
  useFactory: () => {
    const isDev = window.location.hostname === 'localhost';
    return isDev ? 'http://localhost:3000' : 'https://api.prod.com';
  }
}
```