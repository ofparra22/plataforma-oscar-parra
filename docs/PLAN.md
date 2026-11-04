# Plan de construcción por etapas

Trabaja **una etapa por sesión** y detente al terminarla. Cada etapa termina con una lista de lo que Oscar debe probar.

## Etapa 0 · Base del proyecto
- [ ] Vite + React + TypeScript + Tailwind en `web/`; Cloud Functions en TypeScript en `functions/`.
- [ ] `firebase.json`, `.firebaserc` con un alias `default` de ejemplo y emuladores de Auth, Firestore, Functions, Storage y Hosting.
- [ ] Tokens de diseño (colores, tipografía Inter y Plus Jakarta Sans, radios) tomados de `diseno/stitch/`, con modo oscuro por defecto y modo claro.
- [ ] Layout base: sidebar con la marca "Oscar Parra · Consultor de Marketing", selector de espacio de trabajo y navegación; en celular, barra inferior.
- [ ] Scripts: `npm run dev` (emuladores + web), `npm run lint`, `npm run test`.

**Oscar prueba:** `npm run dev` abre la app vacía con el layout en escritorio y en celular.

## Etapa 1 · Login, espacios de trabajo y permisos
- [ ] Login con Google y con link por correo (pantalla `01-login`). Variante "Esta cuenta no tiene acceso todavía" con botón de WhatsApp a Oscar.
- [ ] Función `adminAsignarAcceso` (solo `media_buyer`): asigna `rol` y `workspaces` a un correo.
- [ ] Reglas de Firestore y Storage según `docs/DATOS.md`, con pruebas en el emulador (un usuario de un espacio no puede leer otro).
- [ ] Script `scripts/seed` que crea el espacio "Jaque Marketing" y sus clientes desde `clientes-jaque.json`, y le da a Oscar el rol `media_buyer`.
- [ ] Pantalla de invitar usuario (`12-invitar-usuario`) y lista de usuarios en Ajustes (`11-ajustes`).

**Oscar prueba:** entra con su cuenta, invita un correo de prueba como `agencia_consulta` y confirma que ese usuario solo ve Jaque.

## Etapa 2 · Lectura de Meta: clientes, panel y campañas
- [ ] Funciones `metaResumenCliente`, `metaCampanas`, `metaCreativos` y `metaPreviewAnuncio` según `docs/META.md`, con caché de 15 minutos en Firestore y modo mock.
- [ ] Vista "Todos los clientes" (`02-todos-los-clientes`), agrupada por Pago pendiente, Con campañas activas y Sin campañas recientes.
- [ ] Panel de un cliente (`03-panel-cliente`): barra de presupuesto del mes con proyección al cierre, métricas principales, tabla de campañas y nombres amigables.
- [ ] Estados `03a` a `03d`: pago pendiente, error con datos en caché, sin campañas y cargando.
- [ ] Detalle de campaña (`04-detalle-campana`): métricas, gráfica diaria, creativos con preview, "Ver en Facebook", "Ver en Instagram" y "Ver como anuncio".

**Oscar prueba:** en modo mock, navega de Todos los clientes al detalle de una campaña. Cuando ya exista el token, cambia a modo real y compara contra Ads Manager.

## Etapa 3 · Solicitudes de campaña
- [ ] Formulario `07-solicitar-campana`: campos obligatorios, aviso de formatos faltantes (1:1, 4:5, 9:16), carga de creativos a Storage, opción de impulsar un post existente y fecha límite. No se puede enviar incompleto.
- [ ] "Mis solicitudes" para la agencia (`08-mis-solicitudes`), con las que tienen preguntas de Oscar hasta arriba.
- [ ] Bandeja kanban para Oscar (`09-bandeja-solicitudes`): Nuevas, Faltan materiales o dudas, En preparación, Publicadas y Descartadas. Panel de detalle con línea de tiempo, hilo de mensajes, "Pedir información", "Marcar en preparación" y "Marcar como publicada" (pide el ID o link de la campaña de Meta).
- [ ] Enlaces de WhatsApp con el mensaje armado en cada acción.

**Oscar prueba:** Jacky (usuario de prueba) envía una solicitud, Oscar pregunta, Jacky responde y Oscar la publica. Toda la línea de tiempo queda registrada.

## Etapa 4 · Pendientes, conversación e historial
- [ ] Vista "Pendientes" (`10-pendientes`), con versión para la agencia y para Oscar.
- [ ] Pestaña "Conversación" en el detalle de campaña.
- [ ] Pestaña "Historial de cambios" con el registro de actividad de la cuenta publicitaria (función `metaHistorialCambios`).
- [ ] "Solicitar pausa o cambio" desde una campaña: crea una solicitud de tipo cambio.
- [ ] Nota mensual de Oscar por cliente, visible en el panel.

## Etapa 5 · Explorar y reportes
- [ ] Explorar (`05-explorar`): rango de fechas, comparación con el periodo anterior, filtro por campaña, gráfica, tabla y mejores anuncios.
- [ ] Generador de prompt de reporte (`06-reportes-ia`, `06b-reportes-ia-movil`): arma el texto con los datos reales del periodo, botón "Copiar" y botones para abrir ChatGPT, Claude o Gemini. **No llama a ninguna API de IA.**
- [ ] PDF descargable del resumen del mes.

## Etapa 6 · Revisión diaria automática
- [ ] Función programada a las 7:00 (America/Mexico_City): guarda la foto diaria de métricas, copia las imágenes de los creativos a Storage (las URLs de Meta caducan) y calcula alertas: termina en menos de 48 h, cuenta con pago pendiente y costo por resultado más de 30% arriba del promedio de 7 días.
- [ ] Las alertas aparecen en Todos los clientes y en Pendientes.

## Etapa 7 · Publicación
- [ ] Desplegar Hosting, Functions y reglas en el proyecto real.
- [ ] Guía para conectar un dominio propio.
- [ ] Alerta de presupuesto de Google Cloud y límite de llamadas por usuario en las funciones.
- [ ] Invitar a Jacky.

## Etapa 8 (opcional) · Crear campaña en pausa desde una solicitud
- [ ] Desde la bandeja, Oscar arma la campaña en Meta **en estado pausado** con los datos de la solicitud, para revisarla y activarla en Ads Manager.
