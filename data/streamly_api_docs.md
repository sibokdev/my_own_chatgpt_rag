# Streamly API v2 — Documentación para desarrolladores

> Documento ficticio creado para el curso "Crea tu propio ChatGPT: construye un RAG desde cero". Streamly no es una plataforma real. Todos los endpoints, precios y límites son inventados.

Versión de la documentación: 2.3 — Última actualización: febrero de 2026.

## 1. Introducción

Streamly es una plataforma de streaming de video y música. La API v2 permite a desarrolladores externos consultar el catálogo, administrar listas de reproducción, leer el historial de reproducción de un usuario (con su consentimiento) y recibir notificaciones mediante webhooks.

URL base de la API:

```
https://api.streamly.example/v2
```

Todas las respuestas se devuelven en formato JSON con codificación UTF-8. Las fechas usan el formato ISO 8601 en zona horaria UTC.

## 2. Autenticación

La API v2 admite dos mecanismos de autenticación.

### 2.1 Clave de API (API key)

Para endpoints públicos del catálogo se usa una clave de API. La clave se envía en el encabezado HTTP `X-Streamly-Key`. Las claves se generan en el Portal de Desarrolladores, en la sección "Mis aplicaciones". Cada aplicación puede tener hasta 3 claves activas al mismo tiempo, lo que permite rotarlas sin interrumpir el servicio.

Nunca incluyas la clave de API en código del lado del cliente (por ejemplo, JavaScript del navegador o aplicaciones móviles), porque cualquier persona podría extraerla.

### 2.2 OAuth 2.0

Para acceder a datos de un usuario (listas de reproducción, historial, preferencias) se usa OAuth 2.0 con el flujo Authorization Code con PKCE. Los tokens de acceso expiran después de 3600 segundos (1 hora). Los tokens de actualización (refresh tokens) expiran después de 30 días sin uso.

Permisos (scopes) disponibles:

| Scope              | Permite                                           |
|--------------------|---------------------------------------------------|
| `catalog:read`     | Leer el catálogo público                          |
| `playlists:read`   | Leer las listas de reproducción del usuario       |
| `playlists:write`  | Crear, modificar y eliminar listas de reproducción |
| `history:read`     | Leer el historial de reproducción del usuario     |
| `profile:read`     | Leer nombre, país e idioma del usuario            |

## 3. Endpoints principales

### 3.1 Buscar en el catálogo

`GET /catalog/search?q={texto}&type={video|music}&limit={n}`

Busca títulos por texto. El parámetro `limit` acepta valores de 1 a 50 y su valor por defecto es 20.

Ejemplo con curl:

```bash
curl -H "X-Streamly-Key: TU_CLAVE" \
  "https://api.streamly.example/v2/catalog/search?q=documental%20oceano&type=video&limit=5"
```

Ejemplo con Python:

```python
import requests

resp = requests.get(
    "https://api.streamly.example/v2/catalog/search",
    headers={"X-Streamly-Key": "TU_CLAVE"},
    params={"q": "documental oceano", "type": "video", "limit": 5},
    timeout=10,
)
resp.raise_for_status()
for item in resp.json()["items"]:
    print(item["id"], item["title"])
```

### 3.2 Obtener un título

`GET /titles/{title_id}`

Devuelve el detalle de un título: nombre, duración en segundos, géneros, clasificación por edad, idiomas de audio y subtítulos disponibles.

### 3.3 Listas de reproducción

- `GET /users/me/playlists` — lista las playlists del usuario autenticado. Requiere `playlists:read`.
- `POST /users/me/playlists` — crea una playlist. Requiere `playlists:write`.
- `DELETE /users/me/playlists/{playlist_id}` — elimina una playlist. Requiere `playlists:write`.

Una playlist puede contener como máximo 500 elementos. Cada usuario puede tener como máximo 200 playlists.

Ejemplo para crear una playlist:

```python
resp = requests.post(
    "https://api.streamly.example/v2/users/me/playlists",
    headers={"Authorization": f"Bearer {access_token}"},
    json={"name": "Para correr", "public": False, "items": ["trk_8812", "trk_9930"]},
    timeout=10,
)
```

### 3.4 Historial de reproducción

`GET /users/me/history?since={fecha}`

Devuelve los últimos elementos reproducidos por el usuario. Requiere el scope `history:read`. El historial disponible cubre los últimos 90 días.

## 4. Límites de uso (rate limits)

Para proteger la plataforma, cada aplicación tiene un límite de solicitudes por minuto. El límite depende del plan de desarrollador contratado:

| Plan de desarrollador | Solicitudes por minuto | Solicitudes por día | Webhooks |
|-----------------------|------------------------|---------------------|----------|
| Gratis                | 60                     | 10,000              | No       |
| Startup               | 600                    | 250,000             | Sí       |
| Empresa               | 3,000                  | Ilimitadas          | Sí       |

Cada respuesta incluye los encabezados `X-RateLimit-Limit`, `X-RateLimit-Remaining` y `X-RateLimit-Reset`. Cuando una aplicación excede su cuota, la API responde con el código HTTP 429 y el encabezado `Retry-After` indica cuántos segundos esperar antes de reintentar.

Se recomienda implementar reintentos con espera exponencial (exponential backoff): esperar 1, 2, 4 y 8 segundos entre intentos, con un máximo de 4 reintentos.

## 5. Paginación

Los endpoints que devuelven listas usan paginación por cursor. La respuesta incluye el campo `next_cursor`; para obtener la siguiente página se envía ese valor en el parámetro `cursor`. Cuando `next_cursor` es `null`, ya no hay más resultados.

## 6. Errores

| Código HTTP | Código de error         | Significado                                          |
|-------------|-------------------------|------------------------------------------------------|
| 400         | `invalid_request`       | Falta un parámetro o tiene un formato incorrecto      |
| 401         | `invalid_credentials`   | La clave o el token no son válidos o expiraron        |
| 403         | `insufficient_scope`    | El token no tiene el permiso necesario                |
| 404         | `not_found`             | El recurso no existe                                  |
| 409         | `conflict`              | Ya existe un recurso con ese nombre                   |
| 429         | `rate_limited`          | Se excedió el límite de solicitudes                   |
| 500         | `internal_error`        | Error del servidor; se puede reintentar               |

Ejemplo de cuerpo de error:

```json
{
  "error": {
    "code": "insufficient_scope",
    "message": "Este endpoint requiere el scope playlists:write",
    "request_id": "req_7f3a9c"
  }
}
```

Incluye siempre el `request_id` cuando contactes a soporte.

## 7. Webhooks

Los webhooks notifican a tu servidor cuando ocurren eventos. Solo están disponibles en los planes Startup y Empresa. Eventos disponibles:

- `playlist.created`
- `playlist.deleted`
- `subscription.changed`
- `title.available` (un título que el usuario marcó como "recordarme" ya está disponible)

Cada notificación incluye el encabezado `X-Streamly-Signature`, una firma HMAC-SHA256 del cuerpo usando tu secreto de webhook. Tu servidor debe validar la firma y responder con un código 2xx en menos de 5 segundos. Si no responde, Streamly reintenta el envío hasta 6 veces durante 24 horas.

## 8. SDKs oficiales

Existen SDKs oficiales para Python (`pip install streamly-sdk`), JavaScript/TypeScript (`npm install @streamly/sdk`) y Go. Los SDKs gestionan automáticamente la paginación, la renovación de tokens y los reintentos ante errores 429.

## 9. Planes de suscripción para usuarios finales (FAQ de facturación)

Estos son los planes que pagan los usuarios de Streamly (no confundir con los planes de desarrollador de la sección 4):

| Plan     | Precio mensual | Pantallas simultáneas | Calidad máxima | Descargas offline |
|----------|----------------|-----------------------|----------------|-------------------|
| Básico   | $99 MXN        | 1                     | HD (720p)      | No                |
| Plus     | $169 MXN       | 2                     | Full HD (1080p)| Sí, 25 títulos    |
| Premium  | $249 MXN       | 4                     | 4K             | Sí, 100 títulos   |

**¿Cuándo se cobra la suscripción?** El cobro se realiza el mismo día de cada mes en que el usuario contrató el plan.

**¿Puedo cancelar en cualquier momento?** Sí. Al cancelar, el usuario conserva el acceso hasta el final del periodo ya pagado. No hay reembolsos por periodos parciales.

**¿Qué pasa si cambio de plan?** Al subir de plan el cambio es inmediato y se cobra la diferencia proporcional. Al bajar de plan, el cambio aplica en el siguiente periodo de facturación.

**¿Hay periodo de prueba?** Los usuarios nuevos tienen 14 días de prueba gratuita en el plan Plus.

## 10. Versionado y API v1 (obsoleta)

La API v1 está obsoleta (deprecated) desde enero de 2025 y dejará de funcionar el 30 de junio de 2026. Se recomienda migrar a la v2 lo antes posible.

Notas de la API v1, conservadas como referencia histórica:

- La autenticación de la v1 se hacía con el parámetro `?api_key=` en la URL.
- En la v1 el límite era de 100 solicitudes por minuto para todas las aplicaciones, sin importar el plan.
- La v1 usaba paginación por número de página (`page` y `per_page`).

## 11. Soporte

- Portal de Desarrolladores: developers.streamly.example
- Correo de soporte técnico: api-support@streamly.example
- Tiempo de respuesta: 24 horas hábiles en plan Gratis, 4 horas hábiles en Startup y 1 hora en Empresa.
- Estado del servicio: status.streamly.example
