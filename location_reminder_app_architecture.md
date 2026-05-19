# Arquitectura y Requerimientos — App de Recordatorios por Ubicación para Android

# Objetivo del Proyecto

Desarrollar una aplicación Android que permita crear recordatorios inteligentes basados en ubicación.

La aplicación debe:

1. Detectar automáticamente cuándo el usuario entra o sale de una zona geográfica.
2. Mostrar recordatorios contextuales.
3. Funcionar con bajo consumo de batería.
4. Operar incluso si la aplicación está cerrada.
5. Permitir configurar múltiples tipos de activadores.

---

# Problema que Resuelve

Muchas tareas dependen del lugar donde se encuentra el usuario.

Ejemplos:

- Recordar comprar algo al llegar al supermercado.
- Recordar sacar basura al salir de casa.
- Recordar llamar a alguien al llegar al trabajo.
- Mostrar listas específicas según ubicación.
- Activar rutinas automáticamente.

Las aplicaciones tradicionales dependen de alarmas por tiempo, no por contexto físico.

---

# Casos de Uso

## Supermercado

```text
Cuando llegue al supermercado:
- Comprar arroz
- Comprar huevos
- Comprar café
```

## Trabajo

```text
Al llegar al trabajo:
- Enviar correo
- Revisar inventario
```

## Casa

```text
Al salir de casa:
- Llevar llaves
- Apagar luces
```

---

# Restricciones Técnicas Android

Android limita:

- Acceso constante al GPS.
- Ejecución continua en segundo plano.
- Servicios permanentes.
- Escaneo frecuente de ubicación.

La aplicación debe minimizar consumo de batería.

---

# Estrategia Recomendada

## Geofencing API

Android ofrece APIs oficiales para geocercas.

Permite:

- Detectar entrada/salida de zonas.
- Bajo consumo energético.
- Procesamiento optimizado por el sistema.

---

# Arquitectura General

```text
┌─────────────────────────┐
│ Usuario crea recordatorio│
└─────────────┬───────────┘
              │
              ▼
┌─────────────────────────┐
│ Base de datos local     │
│ Guarda geocercas        │
└─────────────┬───────────┘
              │
              ▼
┌─────────────────────────┐
│ Geofencing Service      │
│ Monitorea ubicación     │
└─────────────┬───────────┘
              │
              ▼
┌─────────────────────────┐
│ Trigger Engine          │
│ Evalúa eventos          │
└─────────────┬───────────┘
              │
              ▼
┌─────────────────────────┐
│ Sistema de notificaciones│
└─────────────────────────┘
```

---

# Tecnologías Recomendadas

| Componente | Tecnología |
|---|---|
| Lenguaje | Kotlin |
| UI | Jetpack Compose |
| Persistencia | Room |
| Ubicación | Fused Location Provider |
| Geofencing | Android Geofencing API |
| Arquitectura | Clean Architecture + MVVM |
| DI | Hilt |
| Async | Coroutines |

---

# Flujo de Funcionamiento

## 1. Usuario crea recordatorio

Ejemplo:

```text
"Comprar café cuando llegue al Éxito"
```

---

## 2. Usuario selecciona ubicación

Opciones:

- Mapa
- Ubicación actual
- Buscar dirección

---

## 3. App crea geocerca

La app registra:

```json
{
  "latitude": 3.4372,
  "longitude": -76.5225,
  "radius": 150
}
```

---

## 4. Android monitorea ubicación

El sistema operativo detecta:

- Entrada
- Permanencia
- Salida

Sin usar GPS constantemente.

---

## 5. Se dispara el recordatorio

Ejemplo:

```text
📍 Estás cerca del supermercado

• Comprar café
• Comprar arroz
```

---

# Tipos de Activadores

## Entrada a zona

```text
Al llegar a:
- Casa
- Trabajo
- Tienda
```

## Salida de zona

```text
Al salir de:
- Casa
- Oficina
```

## Permanencia

```text
Después de permanecer 15 minutos en:
- Gimnasio
- Universidad
```

---

# Configuraciones Avanzadas

## Horarios

Ejemplo:

```text
Solo activar entre:
7:00 AM - 7:00 PM
```

---

## Días específicos

```text
Solo lunes a viernes
```

---

## Repetición

Opciones:

- Una sola vez
- Diario
- Semanal
- Permanente

---

# Estrategia de Batería

## NO usar GPS constante

Mala práctica:

```text
while(true){
    obtenerUbicacion();
}
```

Esto consume demasiada batería.

---

# Estrategia Correcta

Usar:

- Geofencing API
- Eventos del sistema
- Fused Location Provider

Android optimiza automáticamente:

- GPS
- WiFi
- Torres celulares
- Sensores

---

# Arquitectura Recomendada

## Event Driven

La app debe reaccionar únicamente cuando:

- El sistema detecta movimiento relevante.
- Se entra a una geocerca.
- Se sale de una geocerca.

---

# Estructura del Proyecto

```text
app/
 ├── geofence/
 ├── reminders/
 ├── notifications/
 ├── maps/
 ├── settings/
 ├── domain/
 ├── data/
 ├── ui/
 └── utils/
```

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
trigger_type
schedule
created_at
```

---

# Tipos de Trigger

```text
ENTER
EXIT
DWELL
```

---

# Notificaciones

La app debe soportar:

- Notificaciones push locales
- Sonido
- Vibración
- Pantalla flotante opcional

---

# Integración con Mapas

Opciones:

- Google Maps SDK
- OpenStreetMap

---

# Permisos Android

```xml
ACCESS_FINE_LOCATION
ACCESS_BACKGROUND_LOCATION
POST_NOTIFICATIONS
FOREGROUND_SERVICE
```

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

## Restricciones de batería

Algunos fabricantes limitan procesos en segundo plano:

- Xiaomi
- Huawei
- Oppo

La app debe incluir:

- Guías de optimización
- Exclusión de ahorro de batería

---

# MVP Recomendado

## Primera versión

Construir una app que:

- Permita crear recordatorios
- Cree geocercas
- Detecte entrada a zonas
- Muestre notificaciones

---

# Funciones Futuras

## Clasificación inteligente

Categorías:

- Compras
- Trabajo
- Personal
- Salud

---

## IA contextual

La app aprende:

- Lugares frecuentes
- Rutinas
- Horarios habituales

---

# Posibles Funciones Avanzadas

## Modo conducción

Ejemplo:

```text
Mostrar recordatorios relevantes mientras conduces
```

---

## Integración con voz

Ejemplo:

```text
"Recuérdame comprar pan cuando llegue al centro"
```

---

# Consideraciones de Privacidad

La app debe:

- Procesar datos localmente
- Evitar subir ubicaciones innecesariamente
- Permitir exportar/eliminar datos

---

# Roadmap

## Fase 1

- Geofencing básico
- Recordatorios simples
- Notificaciones

## Fase 2

- Mapas interactivos
- Horarios
- Repetición

## Fase 3

- IA contextual
- Integración con voz
- Automatizaciones

---

# Pipeline Recomendado

```text
User Input
    ↓
Location Selection
    ↓
Geofence Registration
    ↓
Android Geofencing API
    ↓
Trigger Detection
    ↓
Notification Engine
```

---

# Posibles Nombres del Proyecto

- GeoReminder
- PlacePing
- SmartReminder
- ContextNote
- GeoTasks
- LocationMemo
- PlaceAlert
- NearbyReminder
- ContextTrigger
- GeoMind
