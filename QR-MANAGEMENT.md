# Gestión de QR - WhatsApp Bot

## ⏱️ Sistema de Tiempo Mejorado

El bot ahora usa un **sistema basado en tiempo** en lugar de un contador de intentos:

- **Ventana total: 10 minutos** desde el primer QR generado
- **Renovación automática**: Los QR se renuevan automáticamente cada ~60 segundos
- **Sin límite de intentos**: Puedes dejar que se renueve cuantas veces necesites dentro de los 10 minutos
- **Timer visible**: El endpoint `/api/whatsapp/status` muestra el tiempo restante

## 📊 Endpoint de Status

```bash
GET /api/whatsapp/status
```

**Respuesta cuando hay QR activo:**
```json
{
  "status": "qr_received",
  "qrCode": "data:image/png;base64,...",
  "qrAttempts": 3,
  "qrTimeRemaining": {
    "remainingMs": 480000,
    "remainingMinutes": 8,
    "remainingSeconds": 0,
    "displayTime": "8m 0s",
    "elapsedMs": 120000
  },
  "message": "📱 QR generado - Escanéalo desde WhatsApp (8m 0s restantes)"
}
```

**Respuesta cuando el tiempo se agotó:**
```json
{
  "status": "disconnected",
  "qrTimeRemaining": {
    "expired": true,
    "message": "Tiempo agotado. Use /api/whatsapp/restart-qr-timer para reiniciar"
  },
  "message": "⏰ Tiempo agotado. Usa /api/whatsapp/restart-qr-timer para obtener 10 minutos nuevos"
}
```

## 🔄 Reiniciar Timer de QR (Nuevo Endpoint)

Si el tiempo se agotó o necesitas más tiempo para escanear:

```bash
POST /api/whatsapp/restart-qr-timer
Headers:
  X-Admin-Key: tu_admin_key
```

**Qué hace:**
- Reinicia el contador de tiempo (10 minutos nuevos)
- Cierra la conexión actual si existe
- Genera un nuevo QR inmediatamente
- NO borra la sesión guardada

**Respuesta:**
```json
{
  "success": true,
  "message": "Timer de QR reiniciado. Tendrás 10 minutos nuevos para escanear.",
  "info": "Espera 2-3 segundos y verifica /api/whatsapp/status para ver el nuevo QR"
}
```

## 🗑️ Limpiar Sesión Completa

Si necesitas empezar desde cero (eliminar sesión guardada):

```bash
POST /api/whatsapp/clear-session
Headers:
  X-Admin-Key: tu_admin_key
```

**Qué hace:**
- Borra TODOS los archivos de sesión
- Resetea todos los contadores
- Cierra la conexión si está activa

**Después debes llamar:**
```bash
POST /api/whatsapp/initialize
Headers:
  X-Admin-Key: tu_admin_key
```

## 🎯 Casos de Uso

### Escenario 1: El usuario se demora en escanear
```
1. Generas QR → tienes 10 minutos
2. Han pasado 9 minutos y no has escaneado
3. Llamas a /api/whatsapp/restart-qr-timer
4. Ahora tienes 10 minutos nuevos
```

### Escenario 2: El tiempo se agotó
```
1. Pasaron los 10 minutos sin escanear
2. El sistema dice "Tiempo agotado"
3. Llamas a /api/whatsapp/restart-qr-timer
4. Obtienes un nuevo QR con 10 minutos
```

### Escenario 3: Problemas con la sesión
```
1. La conexión se comporta raro
2. Llamas a /api/whatsapp/clear-session
3. Llamas a /api/whatsapp/initialize
4. Nuevo QR desde cero
```

### Escenario 4: Cambiar de número
```
1. Llamas a /api/whatsapp/clear-session (borra sesión del número anterior)
2. Llamas a /api/whatsapp/initialize
3. Escaneas QR con el nuevo número
```

## 🔐 Seguridad

Todos los endpoints de control requieren autenticación:

- **X-Admin-Key header**: Para API REST
- **Sesión de login**: Para QR viewer web

Configura en tu `.env`:
```env
ADMIN_API_KEY=tu_clave_segura_aquí
QR_ACCESS_KEY=tu_clave_para_login_aquí
```

## 💡 Ventajas del Nuevo Sistema

✅ **Más tiempo real**: 10 minutos continuos vs 10 intentos x 60s = potencialmente más tiempo  
✅ **Más flexible**: Puedes reiniciar cuando quieras  
✅ **Más claro**: Ves exactamente cuánto tiempo te queda  
✅ **Sin bloqueos**: Nunca te quedarás sin forma de generar QR  
✅ **Mejor UX**: El frontend puede mostrar countdown

## 🛠️ Integración en Frontend

**Mostrar countdown en tiempo real:**
```javascript
async function updateQRStatus() {
  const response = await fetch('/api/whatsapp/status');
  const data = await response.json();
  
  if (data.qrTimeRemaining) {
    if (data.qrTimeRemaining.expired) {
      // Mostrar botón "Obtener más tiempo"
      showRestartButton();
    } else {
      // Mostrar countdown
      const { remainingMinutes, remainingSeconds } = data.qrTimeRemaining;
      updateCountdown(remainingMinutes, remainingSeconds);
    }
  }
}

// Actualizar cada 5 segundos
setInterval(updateQRStatus, 5000);
```

**Botón para más tiempo:**
```javascript
async function requestMoreTime() {
  const response = await fetch('/api/whatsapp/restart-qr-timer', {
    method: 'POST',
    headers: {
      'X-Admin-Key': 'tu_admin_key'
    }
  });
  
  if (response.ok) {
    alert('✅ Tienes 10 minutos nuevos para escanear');
    // Esperar 3 segundos y actualizar
    setTimeout(updateQRStatus, 3000);
  }
}
```

## 📝 Logs Útiles

En la consola de Render verás:
```
⏱️ Iniciando ventana de 10 minutos para escanear QR
📱 QR #1 generado - Tiempo restante: 10m 0s
📱 QR #2 generado - Tiempo restante: 9m 2s
🔄 QR expirado (intento #3)
🔄 Renovando QR automáticamente... (7m restantes)
⏰ Han pasado 10 minutos sin escanear el QR
🛑 Deteniendo generación de QRs
💡 Para reiniciar: POST /api/whatsapp/clear-session y luego /api/whatsapp/initialize
```
