# Instructivo de Desarrollo — App de Recordatorios por Ubicación Inteligente

## Objetivo

Desarrollar una aplicación móvil que permita mostrar recordatorios automáticamente cuando el usuario llegue a ciertos lugares, pero SIN mantener el GPS activo permanentemente.

La aplicación debe priorizar:
- bajo consumo de batería,
- simplicidad,
- funcionamiento confiable,
- arquitectura escalable,
- y experiencia rápida para el usuario.

---

# Concepto Principal

La aplicación NO debe monitorear GPS continuamente.

En cambio:
- cada vez que el usuario desbloquee el teléfono,
- o ocurra un evento importante,
- la aplicación consulta la ubicación UNA sola vez,
- compara contra ubicaciones guardadas,
- y muestra recordatorios relevantes.

Esto reduce enormemente:
- consumo energético,
- procesos en segundo plano,
- uso excesivo de GPS.

---

# Eventos que Activan la Verificación

La aplicación debe revisar ubicación únicamente cuando ocurra alguno de estos eventos:

## Eventos principales
- desbloqueo del teléfono,
- pantalla encendida,
- conexión a Wi-Fi,
- desconexión/conexión Bluetooth,
- apertura manual de la app.

## Eventos opcionales
- movimiento detectado,
- conexión al Bluetooth del carro,
- carga del teléfono,
- cambio significativo de ubicación del sistema operativo.

---

# Flujo Principal

## Flujo general

1. Ocurre un evento.
2. La app obtiene ubicación aproximada.
3. Se comparan las coordenadas con las ubicaciones guardadas.
4. Si existe coincidencia:
   - mostrar notificación,
   - mostrar lista de recordatorios.
5. Guardar timestamp de última revisión.

---

# Estrategia de Ubicación

## Prioridad de precisión

La app debe intentar minimizar el uso de GPS.

Orden recomendado:

1. Torres celulares
2. Wi-Fi
3. GPS (solo si es necesario)

---

# Optimización de batería

NO consultar ubicación si:
- el usuario revisó hace pocos minutos,
- el dispositivo no cambió significativamente de ubicación,
- el teléfono tiene batería baja,
- el usuario está quieto.

Implementar:
- cooldown entre consultas,
- cache de ubicación reciente,
- distancia mínima de actualización.

---

# Arquitectura Recomendada

## Framework

Usar:

- Flutter

Razones:
- desarrollo rápido,
- Android + iPhone,
- excelente ecosistema,
- UI moderna.

---

# Stack Tecnológico

## Frontend
- Flutter

## Base de datos local
- SQLite
o
- Hive

## Manejo de estado
- Riverpod
o
- Bloc

## Ubicación
- geolocator
- background_fetch
- workmanager

## Notificaciones
- flutter_local_notifications

---

# Estructura de Datos

## Reminder

```json
{
  "id": "uuid",
  "title": "Comprar tornillos",
  "description": "Revisar tubos galvanizados",
  "latitude": 0.0,
  "longitude": 0.0,
  "radius": 150,
  "createdAt": "timestamp",
  "enabled": true
}
