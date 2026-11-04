# Modelo de datos y seguridad

## Firestore

```
workspaces/{wsId}
  nombre, logoUrl, whatsappContacto, creadoEn

workspaces/{wsId}/clientes/{clienteId}
  nombre, giro, logoUrl
  adAccountIds: string[]        // sin prefijo act_
  pageIds: string[]
  igAccountIds: string[]
  presupuestoMensual: number    // MXN, acordado con la agencia
  metricaPrincipal: 'leads' | 'mensajes' | 'trafico' | 'alcance' | 'suscripciones'
  activo: boolean

workspaces/{wsId}/clientes/{clienteId}/nombresAmigables/{campaignId}
  nombre

workspaces/{wsId}/clientes/{clienteId}/notas/{YYYY-MM}
  texto, autorUid, actualizadoEn

workspaces/{wsId}/solicitudes/{solicitudId}
  folio            // "SOL-2026-001"
  tipo: 'nueva' | 'cambio' | 'pausa'
  clienteId, objetivo, presupuestoTotal, fechaInicio, fechaFin, fechaLimite
  creativos: [{ storagePath, tipo, ancho, alto, proporcion }]
  postExistenteUrl, copy, publico, notas
  estado: 'nueva' | 'dudas' | 'preparacion' | 'publicada' | 'descartada'
  campanaMetaId    // al publicarse
  creadoPor, creadoEn, actualizadoEn

workspaces/{wsId}/solicitudes/{solicitudId}/eventos/{eventoId}   // solo se agregan
  tipo: 'enviada' | 'pregunta' | 'respuesta' | 'estado' | 'publicada'
  texto, autorUid, creadoEn

workspaces/{wsId}/solicitudes/{solicitudId}/mensajes/{mensajeId}
  texto, adjuntos, autorUid, creadoEn

workspaces/{wsId}/campanas/{campaignId}/mensajes/{mensajeId}
  texto, autorUid, creadoEn

workspaces/{wsId}/alertas/{alertaId}         // las escribe la revisión diaria
  clienteId, tipo, detalle, creadoEn, resuelta

cacheMeta/{clave}                             // solo lo escriben las funciones
  datos, guardadoEn

snapshots/{adAccountId}/dias/{YYYY-MM-DD}     // solo lo escriben las funciones
```

## Usuarios y claims

- `rol`: `media_buyer` | `agencia_editor` | `agencia_consulta`
- `workspaces`: lista de `wsId` (el `media_buyer` tiene acceso a todos, aunque no estén en la lista)
- Se guarda además `usuarios/{uid}` con nombre, correo, WhatsApp y preferencia de tema, editable por el propio usuario.

## Reglas (resumen de lo que deben garantizar)

- Leer cualquier cosa bajo `workspaces/{wsId}` exige `rol == media_buyer` o que `wsId` esté en `workspaces`.
- Crear solicitudes: `media_buyer` o `agencia_editor` del espacio. Cambiar `estado` y `campanaMetaId`: solo `media_buyer`.
- `eventos`: se permite `create`; `update` y `delete` están prohibidos para todos.
- `mensajes`: crear lo puede cualquier usuario del espacio (incluido `agencia_consulta`); editar o borrar, nadie.
- `clientes`, `nombresAmigables`, `notas`: escribe solo `media_buyer`.
- `cacheMeta`, `snapshots`, `alertas`: ningún cliente escribe; solo las funciones con el Admin SDK.
- Storage: `workspaces/{wsId}/solicitudes/**` sigue las mismas reglas de acceso al espacio; archivos de máximo 500 MB, solo imagen o video.

Cada regla lleva una prueba en el emulador, incluida la de que un usuario de otro espacio recibe `permission-denied`.
