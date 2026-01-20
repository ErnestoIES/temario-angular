# TEMA 5: Inyección de Dependencias y Servicios

> **Resumen Ejecutivo:** Documentación técnica sobre el sistema de Inyección de Dependencias (DI) de Angular. Cubre desde los fundamentos del patrón de diseño, la creación y configuración de servicios, hasta el uso avanzado de `InjectionToken` y jerarquías de inyectores.

---

## 1. Filosofía: Servicios vs Componentes

Angular promueve una separación estricta de responsabilidades (SRP - Single Responsibility Principle). Esta arquitectura no es caprichosa, tiene un propósito claro:

* **Componentes (UI - La Cara Visible):** 
  * Su única misión es **presentar datos** al usuario y **capturar sus acciones** (clics, inputs). 
  * Son **efímeros**: Se crean y destruyen constantemente al navegar.
  * *¿Por qué separar?* Si mezclas lógica compleja aquí, tus tests de interfaz (que son lentos y frágiles) tendrán que probar también matemáticas o reglas de negocio.
  
* **Servicios (Lógica - El Cerebro Oculto):** 
  * Son clases "trabajadoras" que viven en segundo plano. Se encargan de llamar APIs, validar reglas de negocio complejas, o compartir datos entre pantallas.
  * Son **reutilizables**: Un mismo servicio de `AuthService` lo usa el Login, el Header (para mostrar avatar) y el Guard (para proteger rutas).

---

## 2. El Patrón: Inyección de Dependencias (DI)

La Inyección de Dependencias (DI) es un patrón de diseño fundamental que Angular implementa en su núcleo.

### ❌ El Problema: Acoplamiento Fuerte (Tight Coupling)
Imaginemos que tus clases son como piezas de Lego. Sin DI, si una pieza necesita otra, **la crea y la pega con pegamento extra fuerte**.

```typescript
class UserComponent {
    // 💀 MAL: El componente "sabe demasiado".
    // 1. Sabe que existe UserService.
    // 2. Sabe que UserService necesita HttpClient.
    // 3. Sabe cómo crear HttpClient.
    // Si cambia el constructor de UserService, ¡tienes que editar TODOS los componentes que lo usen!
    private userService = new UserService(new HttpClient()); 
}
```

### ✅ La Solución Angular: Inversión de Control (IoC)
Angular actúa como un **Contenedor Inteligente**. Tú no "creas" las cosas con `new`, tú **las pides**.

1. **Registras** la receta (Provider): "Angular, así se crea un UserService".
2. **Pides** la dependencia (Injection): "Angular, necesito un UserService para funcionar".
3. **Angular resuelve**: Busca si ya tiene uno creado. Si no, lo crea siguiendo la receta y te lo entrega.

> **Analogía:** Es la diferencia entre cocinar tu propia comida (crear dependencias) vs pedir a un restaurante para que te la traigan (inyección). Tú solo consumes, no te preocupas de los ingredientes internos.

---

## 3. Creación y Configuración de Servicios

### El Decorador @Injectable
Este decorador es la etiqueta que le dice a Angular: "Oye, esta clase no es normal, es un servicio y puede tener dependencias propias".

```typescript
import { Injectable } from '@angular/core';

@Injectable({
  providedIn: 'root' // 👈 La configuración clave
})
export class ProductService {
  constructor(private http: HttpClient) {} // El servicio también puede pedir dependencias
  
  getProducts() { ... }
}
```

### El Concepto de Singleton (`providedIn: 'root'`)
Esta es la configuración recomendada para el 95% de los casos.
* **Singleton (Único):** Al usar `'root'`, Angular crea **UNA SOLA INSTANCIA** de la clase para toda la aplicación. Da igual si 50 componentes piden `ProductService`, todos reciben *exactamente la misma instancia*.
  * *Beneficio:* Permite compartir estado. Si guardas una lista de productos en memoria en el servicio, todos los componentes ven la misma lista.
* **Tree Shaking:** Si creas un servicio pero nadie lo usa, Angular es lo suficientemente listo para **eliminarlo** del código final compilado, haciendo tu app más ligera.

---

## 4. Inyección (Consumo) de Servicios

Una vez que el servicio existe, los componentes necesitan acceder a él.

### A. Función `inject()` (Moderno - Recomendado)
Introducida recientemente, esta función permite inyectar dependencias sin usar el constructor.
* **Ventaja 1:** Es más limpia y legible.
* **Ventaja 2:** Funciona fuera de clases (en funciones `guard` de rutas, `interceptors`, o funciones utilitarias que corren en contexto de inyección).
* **Tipado:** TypeScript infiere automáticamente el tipo.

```typescript
import { Component, inject, OnInit } from '@angular/core';

@Component({ ... })
export class ProductListComponent {
  // "Dame la instancia activa de ProductService"
  private productService = inject(ProductService);
  
  ngOnInit() {
    // Usamos el servicio como si fuera una propiedad normal
    this.products = this.productService.getProducts();
  }
}
```

### B. Inyección por Constructor (Clásico)
El método tradicional. Angular analiza los tipos de los argumentos del constructor para saber qué inyectar.

```typescript
export class ProductListComponent {
  // Angular ve "ProductService" y busca quién provee esa clase
  constructor(private productService: ProductService) {}
}
```

---

## 5. Jerarquía de Inyectores y Resolución

El sistema de inyección de Angular es un árbol que refleja la estructura de tus componentes. Cuando pides algo, Angular empieza a buscar **desde abajo hacia arriba** (bubbling).

1.  **Node Injector (Local):** "¿Este componente tiene el servicio declarado en sus `providers: []`?"
2.  **Parent Injector (Padre):** "¿El componente padre tiene el servicio?"
3.  **... (Abuelos, Bisabuelos):** Sube hasta la raíz del árbol de componentes.
4.  **EnvironmentInjector (Root):** "¿Está declarado globalmente (`providedIn: 'root'`)?"
5.  **NullInjector (Error):** Si llega aquí y no lo encontró, explota: `NullInjectorError`.

### Proveedores a Nivel de Componente vs Root
¿Por qué querrías proveer un servicio en un componente y no en root? **Para aislar estados**.

```typescript
@Component({
  selector: 'app-editor',
  providers: [ValidationService] // 👈 Crea una instancia NUEVA y EXCLUSIVA
})
export class EditorComponent { ... }
```
**Caso de uso:** Imagina que tienes múltiples pestañas de chat abiertas (<app-chat-tab>).
* Si `ChatService` fuera Singleton (`root`), todos los chats compartirían los mensajes. ¡Caos!
* Si proves `ChatService` en `ChatTabComponent`, cada pestaña tiene **su propia instancia virgen** del servicio. Lo que pasa en Las Vegas (Chat A) se queda en Las Vegas.

---

## 6. Nivel Avanzado: InjectionTokens

A veces necesitas inyectar cosas que **no son clases**.
* Un string de configuración (API URL).
* Una interfaz (que TypeScript borra al compilar).
* Una función externa.

Como no hay "clase" para usar como identificador, creamos un **Token** (una ficha única).

### 1. El Problema de las Interfaces
No puedes hacer `inject(MiInterfaz)`. En JavaScript (tiempo de ejecución), las interfaces no existen. Angular necesita un objeto real (el Token) para usar como clave en su "mapa de dependencias".

### 2. Implementación con Tokens
```typescript
import { InjectionToken } from '@angular/core';

// Creamos la "ficha" unica
export const API_URL = new InjectionToken<string>('La URL de la API');
```

En la configuración (`app.config.ts`):
```typescript
providers: [
  // "Cuando alguien pida la ficha API_URL, dale este string"
  { provide: API_URL, useValue: 'https://api.google.com' }
]
```

En el componente:
```typescript
export class UserComp {
  // "Dame lo que sea que esté asociado a la ficha API_URL"
  apiUrl = inject(API_URL); 
}
```

### Factory Providers (`useFactory`)
A veces el valor no es fijo, hay que calcularlo.
* *Ejemplo:* ¿Qué idioma muestro? Depende de la configuración del navegador del usuario.

```typescript
{
  provide: LANGUAGE_TOKEN,
  useFactory: () => {
    // Lógica que se ejecuta justo antes de inyectar
    const browserLang = navigator.language;
    return browserLang.includes('es') ? 'es-ES' : 'en-US';
  }
}
```
