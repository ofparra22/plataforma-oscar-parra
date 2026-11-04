# Integración con Meta (Marketing API)

## Acceso

- App de Meta tipo Negocio, en el portafolio comercial de Oscar, con el producto Marketing API.
- **Usuario del sistema** en ese portafolio, con acceso a las cuentas publicitarias, páginas e Instagram de cada cliente (compartidos como socio por su dueño).
- Token del usuario del sistema con `ads_read` (y `ads_management` solo para la etapa 8), guardado en Secret Manager como `META_SYSTEM_USER_TOKEN`.
- La versión de la Graph API va en una sola constante (`META_API_VERSION`) dentro de `functions/`, no en la interfaz.
- Mientras no exista el token, `META_MODO=mock` hace que todas las funciones respondan con `functions/src/meta/fixtures/`.

## Qué se lee y de dónde

| Dato | Fuente |
|---|---|
| Estado de la cuenta (activa, pago pendiente) | `act_{id}` campo `account_status` (1 = activa, 3 = pago pendiente, 2 = deshabilitada; ver la documentación para el resto) |
| Campañas y conjuntos | `act_{id}/campaigns` y `act_{id}/adsets`: nombre, estado efectivo, objetivo, presupuesto diario o total, presupuesto restante, inicio y fin |
| Métricas | Insights de la cuenta, campaña o anuncio: gasto, alcance, impresiones, clics, CTR, CPM, acciones y costo por acción, con `time_increment=1` para la gráfica diaria |
| Creativos | `adcreatives`: cuerpo, título, tipo, `image_url` o `thumbnail_url`, `effective_object_story_id`, `effective_instagram_media_id` |
| Link al post de Facebook | `https://www.facebook.com/{effective_object_story_id}` |
| Link al post de Instagram | Consulta `/{effective_instagram_media_id}?fields=permalink` |
| Vista previa del anuncio | `/{ad_id}/previews?ad_format=...` (feed de Facebook, feed de Instagram, historias, reels) |
| Historial de cambios | Registro de actividad de la cuenta publicitaria (`act_{id}/activities`) |

## Reglas de interpretación (aprendidas con las cuentas reales)

1. **Un conjunto con estado `ACTIVE` y `end_time` en el pasado está terminado.** Hay cuentas con decenas de conjuntos viejos marcados como activos. Para "campañas activas" exige: estado efectivo `ACTIVE` y (`end_time` vacío o en el futuro).
2. **Las URLs de imágenes de Meta caducan en días.** La revisión diaria copia cada imagen a Storage y la interfaz usa la copia.
3. **Algunos creativos regresan sin contenido** (`object_type: PRIVACY_CHECK_FAIL` y cuerpo vacío). Para esos, muestra nombre, fechas, métricas y el link al post.
4. **Los posts creados solo para pauta** pueden pedir inicio de sesión para verse; "Ver como anuncio" (previews) es el respaldo.
5. Si una cuenta tiene `account_status` de pago pendiente, no consultes insights recientes en bucle: muestra el aviso y los últimos datos en caché.
6. Respeta los límites de llamadas: caché de 15 minutos por consulta y un máximo de llamadas por usuario por minuto.

## Datos que Meta NO entrega (no los construyas)

- Saldo, deuda exacta o datos de la tarjeta de la cuenta.
- Resultados por colonia o fraccionamiento (como mucho por región o estado).
- Tasa de respuesta o de conversión dentro del WhatsApp del negocio.
- ROAS, si el cliente no tiene un pixel o un dataset registrando compras con valor.
- Métricas de "estabilidad algorítmica", "ahorro vs benchmark" o "salud de API" como las de los diseños de Stitch.
