# Arquitectura y Requerimientos — App de Recordatorios Inteligentes de Bajo Consumo

# Objetivo del Proyecto

Desarrollar una aplicación Android de recordatorios contextuales enfocada en:

- Consumo mínimo de batería
- Funcionamiento pasivo
- Recordatorios basados en ubicación aproximada
- Activación únicamente cuando el usuario use el teléfono

La aplicación NO debe utilizar GPS constantemente.

---

# Filosofía Principal del Proyecto

## NO usar rastreo continuo

La aplicación NO funcionará como:

```text
while(true){
   obtenerGPS();
}
```

Eso consumiría demasiada batería.

---

# Estrategia Correcta

La aplicación debe aprovechar eventos naturales del sistema Android.

Especialmente:

```text
Cuando el usuario encienda o desbloquee el teléfono
```

En ese momento:

1. Android ya suele tener ubicación reciente.
2. El sistema ya activó sensores.
3. El costo energético adicional es mínimo.

---

# Idea Central

## Recordatorios contextuales "al despertar el teléfono"

La app funcionará así:

```text
Usuario desbloquea celular
        ↓
La app consulta ubicación reciente
        ↓
Se comparan recordatorios cercanos
        ↓
Se muestran notificaciones relevantes
```

Esto evita:

- GPS permanente
- Servicios agresivos
- Escaneo constante
- Alto consumo de batería

---

# Ventajas de Este Enfoque

## Muy bajo consumo energético

Porque:

- No se usa GPS continuo.
- No hay polling constante.
- Se aprovecha la ubicación cacheada del sistema.

---

# Casos de Uso

## Supermercado

```text
Cuando abra el celular cerca del supermercado:
- Comprar arroz
- Comprar café
```

---

## Trabajo

```text
Cuando desbloquee el teléfono en el trabajo:
- Enviar reporte
```

---

## Casa

```text
Al usar el celular cerca de casa:
- Sacar basura
- Comprar comida para el perro
```

---

# Arquitectura Recomendada

```text
┌──────────────────────────┐
│ Usuario desbloquea móvil │
└─────────────┬────────────┘
              │
              ▼
┌──────────────────────────┐
│ Broadcast Receiver       │
│ Detecta SCREEN_ON        │
└─────────────┬────────────┘
              │
              ▼
┌──────────────────────────┐
│ Location Provider        │
│ Obtiene última ubicación │
└─────────────┬────────────┘
              │
              ▼
┌──────────────────────────┐
│ Reminder Engine          │
│ Busca recordatorios      │
└─────────────┬────────────┘
              │
              ▼
┌──────────────────────────┐
│ Notification System      │
│ Muestra recordatorios    │
└──────────────────────────┘
```

---

# Eventos Android Recomendados

## SCREEN_ON

Detecta cuando la pantalla se enciende.

## USER_PRESENT

Detecta cuando el usuario desbloquea el teléfono.

---

# Estrategia de Ubicación

## Usar última ubicación conocida

NO solicitar GPS activo constantemente.

Usar:

```text
FusedLocationProviderClient.getLastLocation()
```

Esto aprovecha:

- GPS reciente
- WiFi
- Torres celulares
- Datos cacheados del sistema

Con costo energético extremadamente bajo.

---

# Precisión Esperada

## Ubicación aproximada suficiente

La app NO necesita precisión exacta de centímetros.

Ejemplo:

```text
"Estás cerca del supermercado"
```

No requiere:

```text
"Estás exactamente en la puerta"
```

---

# Estrategia de Distancia

Usar radios relativamente amplios:

| Lugar | Radio recomendado |
|---|---|
| Casa | 100 m |
| Supermercado | 150 m |
| Centro comercial | 250 m |
| Trabajo | 150 m |

Esto permite usar ubicación de bajo consumo.

---

# Tecnologías Recomendadas

| Componente | Tecnología |
|---|---|
| Lenguaje | Kotlin |
| UI | Jetpack Compose |
| Persistencia | Room |
| Ubicación | Fused Location Provider |
| Arquitectura | Clean Architecture + MVVM |
| Async | Coroutines |
| DI | Hilt |

---

# Flujo de Funcionamiento

## 1. Usuario desbloquea el teléfono

Android emite:

```text
USER_PRESENT
```

---

## 2. App obtiene ubicación reciente

Ejemplo:

```json
{
  "latitude": 3.4372,
  "longitude": -76.5225
}
```

---

## 3. Motor compara recordatorios

La app evalúa:

```text
¿Hay recordatorios cerca?
```

---

## 4. Se muestran recordatorios relevantes

Ejemplo:

```text
📍 Cerca del supermercado

• Comprar café
• Comprar huevos
```

---

# Motor de Comparación

## Fórmula básica

```text
Distancia usuario ↔ recordatorio
```

Si:

```text
distancia <= radio permitido
```

Entonces:

```text
Mostrar recordatorio
```

---

# Estrategia de Optimización

## Cache temporal

La app puede:

- Evitar múltiples consultas seguidas
- Reusar ubicación reciente por varios minutos

Ejemplo:

```text
No volver a consultar ubicación durante 5 minutos
```

---

# Sistema Inteligente

## Priorización

Mostrar:

- Solo recordatorios cercanos
- Máximo 3 simultáneamente
- Los más relevantes primero

---

# Arquitectura Event-Driven

La app NO debe:

- Ejecutarse constantemente
- Escuchar GPS continuamente
- Mantener servicios permanentes

La app SOLO reacciona a:

- Encendido de pantalla
- Desbloqueo
- Uso normal del teléfono

---

# Estructura del Proyecto

```text
app/
 ├── receivers/
 ├── reminders/
 ├── location/
 ├── notifications/
 ├── settings/
 ├── domain/
 ├── data/
 ├── ui/
 └── utils/
```

---

# Componentes Principales

## receivers/

Detecta eventos Android:

- SCREEN_ON
- USER_PRESENT

---

## location/

Obtiene última ubicación conocida.

---

## reminders/

Motor de evaluación contextual.

---

## notifications/

Muestra alertas inteligentes.

---

# Base de Datos

## Tabla: reminders

```sql
id
title
description
latitude
longitude
radius
cooldown_minutes
created_at
```

---

# Cooldown Inteligente

Evitar spam.

Ejemplo:

```text
No volver a mostrar el mismo recordatorio
durante 30 minutos
```

---

# Permisos Android

```xml
ACCESS_COARSE_LOCATION
POST_NOTIFICATIONS
RECEIVE_BOOT_COMPLETED
```

## Importante

Intentar evitar:

```xml
ACCESS_BACKGROUND_LOCATION
```

Porque:

- Consume más batería
- Genera más restricciones
- Reduce aceptación del usuario

---

# Compatibilidad

## Android mínimo recomendado

```text
Android 10+
```

Ideal:

```text
Android 12+
```

---

# Riesgos Técnicos

## Fabricantes agresivos

Algunos dispositivos cierran procesos:

- Xiaomi
- Huawei
- Oppo

La app debe:

- Reiniciar receptores al boot
- Mantener arquitectura liviana

---

# MVP Recomendado

## Primera versión

Construir una app que:

- Permita crear recordatorios
- Detecte desbloqueo del teléfono
- Consulte última ubicación
- Muestre recordatorios cercanos

Sin GPS permanente.

---

# Funciones Futuras

## IA contextual

La app aprende:

- Lugares frecuentes
- Horarios comunes
- Rutinas del usuario

---

# Posibles Mejoras

## Modo híbrido opcional

Solo si el usuario lo activa:

- Geofencing ligero
- Mayor precisión
- Recordatorios automáticos sin desbloqueo

---

# Filosofía Final

## La batería es prioridad absoluta

La app debe sentirse:

```text
Invisible
```

Pero útil exactamente en el momento adecuado.

---

# Pipeline Final Recomendado

```text
SCREEN_ON / USER_PRESENT
          ↓
Last Known Location
          ↓
Nearby Reminder Search
          ↓
Smart Filtering
          ↓
Notification
```

---

# Posibles Nombres del Proyecto

- WakeReminder
- ContextWake
- GeoWake
- SmartWakeNotes
- NearbyMemo
- UnlockReminder
- WakeTrigger
- PassiveReminder
