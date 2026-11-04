# Guía de diseño

Los archivos de `diseno/stitch/` son maquetas exportadas de Google Stitch (HTML con Tailwind por CDN). Úsalos como **referencia visual y de estructura**: no se copian tal cual al proyecto. En `diseno/capturas/` está la captura de cada uno.

## Reglas generales para todas las pantallas

- Marca: "Oscar Parra · Consultor de Marketing" en el sidebar, el login y el footer. Jaque Marketing aparece solo como **espacio de trabajo** en el selector del sidebar.
- **Ignora en todas:** etiquetas como "Meta Graph API v20.0", "Sync v20.0", "CONFIG V2.4" o "Cifrado punto a punto"; botones "Sincronizar datos" (se reemplazan por el texto "Actualizado hace X min"); interruptores de encendido en tablas de campañas; personas y clientes inventados; fechas de 2024.
- Sí se muestran métricas técnicas (CTR, CPM, costo por resultado): los usuarios son de marketing.
- Los botones flotantes repetidos de "Solicitar nueva campaña" se dejan solo en el header.

## Pantalla por pantalla

| Vista | Archivo | Tomar | Ignorar o cambiar |
|---|---|---|---|
| Login | `01-login` | Layout, botón de Google, link por correo, bloque "no tiene acceso" | "Performance Engine", la etiqueta de API y "cifrado punto a punto"; el año del footer va dinámico |
| Todos los clientes | `02-todos-los-clientes` | Contadores de arriba, filtros, agrupación por estado, tarjetas por cliente con alertas | "ROAS Blended" (solo si hay pixel de compra); "100% integradas a Meta API" |
| Panel de un cliente | `03-panel-cliente` | Pacing mensual, tarjetas de métricas, tabla de campañas con "Solicitar pausa o cambio" y "Comentar", bitácora | **"Distribución regional" por colonia** (Meta no da ese nivel; si se hace, solo por estado o región); "Relevancia de anuncio"; "Tasa de respuesta de admisiones"; "Configurar números" |
| Estado pago pendiente | `03a-estado-pago-pendiente` | El banner rojo y la tabla con motivo de detención | Monto de deuda, terminación de tarjeta, Roberto G., interruptores y logo de Jaque |
| Estado error | `03b-estado-error-datos` | Banner "Mostramos los datos de hace X" y el sello de hora en cada tarjeta | Códigos de error técnicos, logo de Jaque, Carlos Mendoza |
| Estado sin campañas | `03c-estado-sin-campanas` | Mensaje central y botones | "Pixel y CAPI sincronizados", "Método de pago al día" y las sugerencias inventadas |
| Estado cargando | `03d-estado-cargando` | Esqueletos | Logo de Jaque |
| Detalle de campaña | `04-detalle-campana` | Header con "Ver solicitud original", "Solicitar pausa o cambio", métricas, gráfica, pestañas Creativos/Conversación/Historial | "Optimización CBO: fase activa" y la etiqueta de sync |
| Explorar | `05-explorar` | Selector de periodo, comparación, gráfica, tabla y mejores anuncios | "Ahorro total generado", "Estabilidad de CPL", "Exportar CSV raw" y la marca Jaque |
| Reportes con IA | `06-reportes-ia`, `06b-reportes-ia-movil` | Parámetros a la izquierda, prompt a la derecha, botón grande de copiar, botones para abrir ChatGPT, Claude y Gemini; en celular, botón de copiar fijo | "Modelo sugerido", la marca Jaque y "Account Manager Senior de Jaque" en el texto del prompt |
| Solicitar campaña | `07-solicitar-campana` | Los 4 pasos, objetivos en tarjetas, "Que Oscar decida", aviso de formato faltante, "Impulsar post existente", fecha límite, resumen fijo abajo | "Meta Ads Graph v20.0", "Sincronizar datos", Roberto G., los clientes inventados del header |
| Mis solicitudes | `08-mis-solicitudes` | "Requieren tu respuesta" arriba, tabla de historial con estados | "Protocolo de entrega" con tiempo promedio prometido; clientes inventados |
| Bandeja (Oscar) | `09-bandeja-solicitudes` | Kanban, panel de detalle con línea de tiempo, hilo, botones y campo de campaña vinculada | "Account Managers", "Mis asignadas", clientes inventados |
| Pendientes | `10-pendientes` | Cambio entre vista de agencia y de media buyer, bloques por tipo, botones directos | "Sincronizar Meta Ads", "Exportar status", la versión de API del footer |
| Ajustes | `11-ajustes` | Mi perfil (nombre, correo, WhatsApp), usuarios, tema claro/oscuro | Contraseña y 2FA, "Sincronizar con Meta Business", el logo de Jaque en el sidebar |
| Invitar usuario | `12-invitar-usuario` | Correo, WhatsApp, 3 roles, asignación de clientes, vista previa del mensaje, "Enviar por WhatsApp" y "Copiar enlace" | Clientes inventados |
