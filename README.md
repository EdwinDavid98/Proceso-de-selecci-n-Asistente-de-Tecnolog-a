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

<img width="1400" height="908" alt="image" src="https://github.com/user-attachments/assets/a33600fc-6cdf-499f-98d1-a871b536bef7" />


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

<img width="1408" height="913" alt="image" src="https://github.com/user-attachments/assets/f0b26e4e-f2b8-4372-9ab6-61638f9d3967" />


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


<img width="1403" height="907" alt="image" src="https://github.com/user-attachments/assets/464add29-aa68-4b29-9391-c3df1b2cc7e9" />


------------------------------------------------------------------------

### 🔹 4. **Condición (solo si es lunes)**

-   **Expresión:**

    ``` text
    dayOfWeek(utcNow()) == 1
    ```

    *(0 = domingo, 1 = lunes, 2 = martes, etc.)*

> 🔎 *Evalúa el número del día actual en formato UTC.*
<img width="1410" height="908" alt="image" src="https://github.com/user-attachments/assets/5fe80cc4-b4cb-49e4-a6e1-285b4a806777" />

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

<img width="1408" height="907" alt="image" src="https://github.com/user-attachments/assets/ffb75628-55b3-4b02-affc-f8e5d2c3ef84" />


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

## Resultado teams
<img width="1402" height="915" alt="image" src="https://github.com/user-attachments/assets/b5792ec0-db11-4c5b-bd51-3b1c3e7c2783" />
## Resultado SharePoint
<img width="1400" height="914" alt="image" src="https://github.com/user-attachments/assets/a7f78812-52b6-427f-9f29-7b890fa6f1e0" />



