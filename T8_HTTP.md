# Tema 8: Comunicación con el Servidor (HTTP)

## 1. Introducción: Conectando con el Mundo Exterior
Hasta ahora, nuestra **App de Gestión de Tareas** vive en la memoria del navegador. Si refrescamos, los datos desaparecen. Para que las tareas sean persistentes, necesitamos comunicarnos con un servidor o API REST.

En Angular, cada componente suele ser una "isla" aislada[cite: 5, 6], pero para obtener datos externos utilizamos el servicio **HttpClient**.

- **Concepto Clave:** A diferencia de la API `fetch` nativa, `HttpClient` de Angular está integrado en el sistema de Inyección de Dependencias y permite gestionar las respuestas de forma reactiva.

---

## 2. Configuración: `provideHttpClient` (Angular 18+)
En las aplicaciones modernas (Standalone), ya no utilizamos `HttpClientModule`. Ahora configuramos el cliente HTTP en el archivo de configuración global.

```typescript
// app.config.ts
import { ApplicationConfig } from '@angular/core';
import { provideHttpClient } from '@angular/common/http';

export const appConfig: ApplicationConfig = {
  providers: [
    provideHttpClient(), // Habilita el servicio HTTP en toda la aplicación
  ]
};

```

---

## 3. El Servicio de Datos (Data Service)

Siguiendo las buenas prácticas, dividimos responsabilidades: el componente se encarga de la presentación y el Servicio se encarga de la lógica de datos y la conexión a la API.

```typescript
// task.service.ts
import { Injectable, inject, signal } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Task } from '../models/task.model';

@Injectable({
  providedIn: 'root'
})
export class TaskService {
  private http = inject(HttpClient); // Inyección de dependencia moderna
  private apiUrl = '[https://api.ejemplo.com/tasks](https://api.ejemplo.com/tasks)';

  // Usamos Signals para que los componentes reaccionen automáticamente
  tasks = signal<Task[]>([]);

  // 1. GET: Obtener todas las tareas
  getTasks() {
    this.http.get<Task[]>(this.apiUrl).subscribe({
      next: (data) => this.tasks.set(data),
      error: (err) => console.error('Error al descargar tareas', err)
    });
  }

  // 2. POST: Crear una nueva tarea
  addTask(title: string) {
    const newTask = { title, completed: false };
    this.http.post<Task>(this.apiUrl, newTask).subscribe(task => {
      // Actualizamos el Signal añadiendo la nueva tarea al array existente
      this.tasks.update(prev => [...prev, task]);
    });
  }

  // 3. DELETE: Eliminar una tarea
  deleteTask(id: number) {
    this.http.delete(`${this.apiUrl}/${id}`).subscribe(() => {
      // Filtramos el Signal para eliminarla de la vista inmediatamente
      this.tasks.update(prev => prev.filter(t => t.id !== id));
    });
  }
}
```

---

## 4. Consumo en el Componente (Smart Component)

[cite_start]El componente **"Padre" o Inteligente** es quien inyecta el servicio y decide cuándo pedir los datos[cite: 16, 19]. [cite_start]En una arquitectura profesional, este componente no conoce los detalles de la API, solo llama a los métodos del servicio[cite: 18].



```typescript
// task-list.component.ts
import { Component, OnInit, inject } from '@angular/core';
import { TaskService } from './task.service';

@Component({
  selector: 'app-task-list',
  standalone: true,
  template: `
    <section>
      <h2>Panel de Control de Tareas</h2>
      
      @if (taskService.tasks().length === 0) {
        <p>Cargando datos desde el servidor...</p>
      }

      @for (task of taskService.tasks(); track task.id) {
        <div class="task-item">
          <span>{{ task.title }}</span>
          <button (click)="onDelete(task.id)">Eliminar</button>
        </div>
      }
    </section>
  `
})
export class TaskListComponent implements OnInit {
  // Inyectamos el servicio para acceder a sus métodos y Signals
  public taskService = inject(TaskService);

  ngOnInit() {
    // Al inicializar el componente, disparamos la petición HTTP inicial
    this.taskService.getTasks();
  }

  onDelete(id: number) {
    // El componente orquestador decide ejecutar la acción de borrado
    this.taskService.deleteTask(id);
  }
}
```

--- 

## 5. Resumen Visual de Métodos HTTP

Para no perderte nunca en las peticiones al servidor, recuerda esta tabla de equivalencias:

| Símbolo | Método | Dirección | Significado | Mnemotecnia |
| :---: | :--- | :--- | :--- | :--- |
| **GET** | Lectura | Servidor ➔ App | "Dame los datos que tienes guardados" | Una lupa 🔍 para buscar datos. |
| **POST** | Escritura | App ➔ Servidor | "Crea este nuevo elemento en la base de datos" | Un sobre ✉️ que envías para guardar algo nuevo. |
| **PUT** | Actualización | App ➔ Servidor | "Cambia estos datos por los nuevos" | Una llave inglesa 🔧 para reparar/cambiar algo. |
| **DELETE** | Borrado | App ➔ Servidor | "Elimina este registro para siempre" | Una papelera 🗑️ para quitar lo que sobra. |

---

## 6. Reglas de Oro de HTTP en Angular

Para trabajar como un profesional y evitar errores comunes, sigue estas tres reglas:

* **Suscripción Obligatoria:** Las peticiones HTTP en Angular son "frías". Si no haces `.subscribe()`, la petición **no se envía** al servidor. Es como marcar un número de teléfono pero no darle a la tecla de llamar.
* **Tipado Siempre:** Usa siempre interfaces (ej. `http.get<Task[]>(...)`) para que TypeScript sepa exactamente qué datos estás recibiendo. Esto evita el error de "propiedad no encontrada" en tiempo de ejecución.
* **No Mutes el Estado:** Al recibir datos de la API, utiliza siempre los métodos `.set()` o `.update()` de los **Signals**. Nunca hagas un `.push()` al array original, ya que Angular no detectará el cambio y la pantalla no se actualizará.