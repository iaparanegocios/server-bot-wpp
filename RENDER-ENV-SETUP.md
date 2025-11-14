# 🚀 Configuración de Variables de Entorno en Render.com

## 📋 Instrucciones

1. Ve a tu servicio en Render: https://dashboard.render.com
2. Selecciona tu servicio `server-bot-wpp`
3. Ve a la pestaña **"Environment"**
4. Copia y pega **cada variable** de la lista de abajo
5. Click en **"Save Changes"**
6. Render redesplegará automáticamente

---

## ✅ Variables a Configurar (Copia esto):

```
NODE_ENV=production
OPENAI_API_KEY=sk-proj-TU_CLAVE_REAL_DE_OPENAI_AQUI
QR_ACCESS_KEY=fab4e7623de67bcf3d348bffd75f3de6cbb1711290fd97aa9a92d9eab9476495
ADMIN_API_KEY=72bc0b84eb58a0d6c82e2b8df5dad0ec63c1a116f213c6214c2c7e10c8a03433
BOT_MODE=openai
OPENAI_MODEL=gpt-4o-mini
OPENAI_ASSISTANT_ID=asst_bot0Cyt7i8x2oHQ4DcL9sAx8
OPENAI_SYSTEM_PROMPT=Eres un asistente útil de WhatsApp. Responde de forma amigable, concisa y profesional en español.
AUTO_BOT_ENABLED=true
AUTO_INIT=true
BOT_COOLDOWN_MS=3000
TYPING_DELAY_MS=1000
MESSAGE_GROUPING_DELAY=3000
MAX_GROUPED_MESSAGES=5
MAX_MESSAGES_PER_CHAT=10
ENABLE_QR_AUTH=true
ENABLE_ADMIN_AUTH=true
SESSION_SECRET=a7f3k8m2n9p4q6r1t5v8w3x7y2z9b4c6e1f8h3j7k2m5n9p3q8r2t6v1w5x9y4z7
LOG_LEVEL=silent
```

---

## 🔐 Seguridad

### Tus credenciales de acceso:

**Para acceder al QR Viewer:**
- URL: `https://tu-app.onrender.com/login`
- Access Key: `fab4e7623de67bcf3d348bffd75f3de6cbb1711290fd97aa9a92d9eab9476495`

**Para endpoints administrativos:**
- Admin API Key: `72bc0b84eb58a0d6c82e2b8df5dad0ec63c1a116f213c6214c2c7e10c8a03433`

⚠️ **IMPORTANTE**: Guarda estas claves en un lugar seguro.

---

## 📝 Notas Importantes

### ❌ Variables que NO debes usar en producción:
- `ALLOWED_ORIGINS` con localhost
- `BOT_IA_ENDPOINT` (solo si BOT_MODE=openai)
- `X-API-KEY` (no se usa en el código actual)
- `PORT` (Render lo asigna automáticamente)

### ✅ Lo que hace cada variable:

| Variable | Descripción |
|----------|-------------|
| `NODE_ENV=production` | Activa modo producción (seguridad mejorada) |
| `OPENAI_API_KEY` | Tu API key de OpenAI |
| `QR_ACCESS_KEY` | Contraseña para acceder al QR viewer |
| `ADMIN_API_KEY` | Key para endpoints administrativos |
| `BOT_MODE=openai` | Usa OpenAI directamente (sin backend externo) |
| `OPENAI_ASSISTANT_ID` | ID de tu asistente personalizado en OpenAI |
| `AUTO_INIT=true` | Inicia WhatsApp automáticamente al arrancar |
| `LOG_LEVEL=silent` | Sin logs (ahorra recursos y costos) |
| `SESSION_SECRET` | Firma las cookies de sesión de forma segura |

---

## 🎯 Verificación Post-Deploy

Después de configurar las variables:

1. ✅ Servicio debe mostrar "Deploy successful"
2. ✅ Logs deben mostrar: "Server running on port XXXX"
3. ✅ Accede a: `https://tu-app.onrender.com/login`
4. ✅ Ingresa el `QR_ACCESS_KEY`
5. ✅ Escanea el QR desde WhatsApp

---

## 🆘 Troubleshooting

**Error 500 al hacer login:**
- Verifica que `QR_ACCESS_KEY` esté configurada en Render
- Revisa que `SESSION_SECRET` no esté vacía

**WhatsApp no conecta:**
- Verifica que `AUTO_INIT=true`
- Checa los logs en Render para ver errores de Baileys

**Bot no responde:**
- Verifica que `OPENAI_API_KEY` sea válida
- Confirma que `AUTO_BOT_ENABLED=true`
- Revisa el saldo de tu cuenta de OpenAI

---

## 📞 Soporte

Si tienes problemas:
1. Revisa los logs en Render Dashboard
2. Verifica que todas las variables estén configuradas
3. Asegúrate de que tu API Key de OpenAI tenga saldo

