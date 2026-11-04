# Plataforma Oscar Parra · Consultor de Marketing

Plataforma web donde agencias y negocios clientes de Oscar Parra (media buyer freelance) consultan sus campañas de Meta Ads, solicitan campañas nuevas y resuelven dudas con él, sin que nada quede fuera del registro. El primer espacio de trabajo es la agencia **Jaque Marketing** (Torreón, Coahuila).

Todo el producto (interfaz, textos, commits y comentarios de código) va en **español de México**.

## Documentos que debes leer

- `docs/PLAN.md`: etapas de construcción. **Trabaja una etapa a la vez** y detente al terminarla para que Oscar la revise.
- `docs/DISENO.md`: qué pantalla de `diseno/stitch/` corresponde a cada vista, qué copiar y qué ignorar.
- `docs/DATOS.md`: modelo de Firestore, roles y reglas de seguridad.
- `docs/META.md`: qué datos se leen de Meta, cómo, y las limitaciones reales de la API.

## Stack

- Frontend: Vite + React + TypeScript + Tailwind CSS (toma los tokens de color y tipografía de los HTML de Stitch). Mobile-first.
- Backend: Firebase. Hosting, Auth (Google y link por correo), Firestore, Cloud Functions (Node 20, TypeScript, 2.ª generación) y Storage. El proyecto usa el plan Blaze.
- Desarrollo local con Firebase Emulator Suite. Mientras no haya token de Meta, las funciones corren en **modo mock** (`META_MODO=mock`) con datos de `functions/src/meta/fixtures/`.

## Roles

| Rol (custom claim `rol`) | Quién | Puede |
|---|---|---|
| `media_buyer` | Oscar | Todo, en todos los espacios de trabajo. Gestiona la bandeja, publica y responde. |
| `agencia_editor` | Jacky y su equipo | Ver los clientes de su espacio, crear solicitudes, comentar y pedir pausas o cambios. |
| `agencia_consulta` | Equipo de la agencia | Ver los clientes de su espacio y comentar. |

Los claims `rol` y `workspaces` (lista de IDs) solo los asigna una Cloud Function que exige que quien la llama sea `media_buyer`.

## Reglas que no se rompen

1. **El token de Meta nunca llega al navegador.** Vive en Secret Manager (`META_SYSTEM_USER_TOKEN`) y solo lo usan las Cloud Functions.
2. **Cada lectura de Meta pasa por una función que valida** que el usuario tiene acceso al espacio de trabajo y que la cuenta publicitaria pertenece a un cliente de ese espacio. Si no, responde `permission-denied` sin llamar a Meta.
3. **Nadie de la agencia pausa, edita ni borra campañas en Meta desde la plataforma.** Solo crean solicitudes. Las únicas escrituras a Meta son de Oscar y llegan en etapas posteriores del plan.
4. **No hay registro abierto.** Un usuario sin claims ve la pantalla "Esta cuenta no tiene acceso todavía".
5. **Los avisos son enlaces de WhatsApp** (`https://wa.me/<numero>?text=<mensaje codificado>`) que el usuario abre y envía. Nada de correo, push ni APIs de WhatsApp pagadas.
6. **No se muestran datos que Meta no entrega** (ver la lista en `docs/META.md`). Si un diseño de Stitch los incluye, se omiten.
7. **Registro inmutable:** los eventos de una solicitud (enviada, pregunta, respuesta, publicada) solo se agregan, nunca se editan ni se borran.
8. No uses los nombres ni los datos inventados de los diseños de Stitch (Roberto G., Carlos Mendoza, Clínica San José, Inmobiliaria Regia, Restaurante Sonora Prime, etc.). Los clientes reales están en `scripts/seed/clientes-jaque.json`.

## Convenciones

- Moneda MXN con formato `$1,234.00`. Zona horaria `America/Mexico_City`. Fechas cortas en estilo "mié 4 nov".
- Textos de interfaz en sentence case. Botones con verbo al inicio ("Enviar solicitud").
- Los secretos y las credenciales nunca se suben al repo (ver `.gitignore` y `.env.example`).
- Antes de cerrar una etapa: compila sin errores, pasa el lint, corre las pruebas de las reglas de Firestore en el emulador y actualiza la casilla de la etapa en `docs/PLAN.md`.
