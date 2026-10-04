# DECANTS PARANA v6 · Cloudflare

Sitio estático + un Worker que atiende `/api/*` (catálogo, login, guardado, fotos).
Datos y fotos subidas desde el panel viven en un KV. Las 139 fotos actuales ya vienen
incluidas en `public/fotos/` y los 191 productos + la configuración, en
`public/seed-products.json` y `public/seed-settings.json`: **no hay que importar nada a mano**.
La primera vez que entres al panel, se copian al KV.

## Despliegue (una sola vez)

1. **Subí esta carpeta a tu repo de GitHub** (reemplazando el contenido anterior).
2. En Cloudflare: **Storage & databases → KV → Create** → nombre `decants-parana`.
   Copiá el **ID** del namespace y pegalo en `wrangler.jsonc`, en lugar de `PEGAR_ID_DEL_KV_AQUI`
   (podés editarlo desde GitHub). Subí el cambio.
3. **Workers & Pages → Create → Import a repository** → elegí el repo.
   - Nombre del proyecto: `decants-parana`
   - Build command: *(vacío)*
   - Deploy command: `npx wrangler deploy`
4. Cuando termine el primer despliegue: **Worker `decants-parana` → Settings → Variables and Secrets**, agregá como **Secret**:
   - `ADMIN_USER` → tu usuario
   - `ADMIN_PASSWORD` → tu contraseña
   - `ADMIN_SESSION_SECRET` → cualquier texto largo y aleatorio
5. Abrí `https://decants-parana.<tu-subdominio>.workers.dev/admin`, ingresá, y probá subir una foto y "Guardar cambios".

Alternativa por consola: `npm install`, `npx wrangler kv namespace create DECANTS`, pegar el ID en `wrangler.jsonc`, `npx wrangler secret put ADMIN_USER` (idem las otras dos) y `npx wrangler deploy`.

## Cómo funciona

- `public/` se sirve como archivos estáticos (gratis e ilimitado). El Worker solo corre para `/api/*`.
- `/api/image/<id>`: si subiste una foto desde el panel, sale del KV; si no, sale de `public/fotos/<id>.webp`.
- Los productos sin foto subida (47 con ilustración provisoria) siguen usando `public/assets/perfumes/`.
- Para cambiar el usuario o la contraseña, editá los secretos del punto 4.

## Nota

`seed-products.json` y `seed-settings.json` solo se usan si el KV está vacío. Después manda lo que guardes en el panel.
