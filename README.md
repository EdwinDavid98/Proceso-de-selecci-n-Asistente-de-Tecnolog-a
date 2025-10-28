# 🧠 Flujo de Integración Diaria en Power Automate**

## 📋 **Enunciado**

Diseñar un flujo automático en **Microsoft Power Automate** que se
ejecute **diariamente a las 07:00** y cumpla las siguientes acciones:

1.  **Publicar en Teams** un mensaje con la hora exacta de ejecución:\
    `"Control diario de integración completado: [fecha/hora]"`.
2.  **Registrar la ejecución en SharePoint**, creando un ítem con los
    campos:
    -   `FechaEjecucion = utcNow()`\
    -   `Estado = 'OK'`
3.  **(Opcional)**: si es **lunes**, enviar un correo electrónico con el
    asunto\
    `"Resumen semanal iniciado"`.

------------------------------------------------------------------------

## ⚙️ **Diseño del Flujo**

### 🔹 1. **Trigger -- Recurrence**

-   **Acción:** `Recurrence`
-   **Frecuencia:** 1 día\
-   **Hora de ejecución:** 07:00 (hora local)
-   **Objetivo:** Programar el inicio automático del flujo cada mañana.



> 💡 *Permite garantizar la ejecución diaria sin intervención humana.*

------------------------------------------------------------------------

### 🔹 2. **Publicar mensaje en Teams**

-   **Acción:** `Microsoft Teams → Publicar mensaje en un chat o canal`

-   **Mensaje:**

    ``` text
    Control diario de integración completado: @{formatDateTime(utcNow(),'yyyy-MM-dd HH:mm:ss')} UTC
    ```

-   **Publicar como:** Bot de Flow\

-   **Destino:** Canal de notificaciones del equipo.

> 💬 *Proporciona trazabilidad inmediata del flujo a todos los miembros
> del equipo.*

------------------------------------------------------------------------

### 🔹 3. **Registrar ejecución en SharePoint**

-   **Acción:** `SharePoint → Crear elemento`
-   **Dirección del sitio:**\
    `https://<organización>.sharepoint.com/sites/ControldeIntegraciones`
-   **Lista:** `ControlIntegraciones`
-   **Campos:**
| Campo            | Valor         |
| ---------------- | ------------- |
| `FechaEjecucion` | `@{utcNow()}` |
| `Estado`         | `OK`          |


> 📊 *Este registro sirve como bitácora automatizada de ejecución
> diaria.*

------------------------------------------------------------------------

### 🔹 4. **Condición (solo si es lunes)**

-   **Expresión:**

    ``` text
    dayOfWeek(utcNow()) == 1
    ```

    *(0 = domingo, 1 = lunes, 2 = martes, etc.)*

> 🔎 *Evalúa el número del día actual en formato UTC.*

------------------------------------------------------------------------

### 🔹 5. **Enviar correo si es lunes**

-   **Acción:** `Outlook → Enviar un correo electrónico (V2)`
-   **Condición:** Rama **True** de la expresión anterior.
-   **Detalles:
  
  Campo       | Valor                             |
| ----------- | --------------------------------- |
| **Para:**   | `emontenegrob@alumni.usfq.edu.ec` |
| **Asunto:** | `Resumen semanal iniciado`        |
| **Cuerpo:** |                                   |

> 📧 *Activa una notificación automatizada de apertura de semana.*

------------------------------------------------------------------------

## 🧩 **Estructura final del flujo**

``` plaintext
Recurrence (07:00 diario)
    ↓
Publicar mensaje en Teams
    ↓
Crear elemento en SharePoint
    ↓
Condición: dayOfWeek(utcNow()) = 1 ?
      ├── True → Enviar correo "Resumen semanal iniciado"
      └── False → (sin acción)
```
<img width="1407" height="910" alt="image" src="https://github.com/user-attachments/assets/1d22f675-1554-4619-b1c7-a99fcb1ccf3f" />

------------------------------------------------------------------------

## ✅ **Resultados esperados**

-   Teams recibe un mensaje diario con fecha y hora de ejecución.\
-   SharePoint guarda automáticamente un nuevo registro con: \|
  
| FechaEjecucion       | Estado |
| -------------------- | ------ |
| 2025-10-28T07:00:00Z | OK     |

