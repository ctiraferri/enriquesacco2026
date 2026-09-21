# Regenerar la imagen para redes

La imagen `assets/og.jpg` (1200×630) se genera desde `tools/og.html`.
No hace falta abrir un editor: se edita el texto en el HTML y se vuelve a capturar.

## A mano (Chrome)

1. Levantar el server: `python -m http.server 8000`
2. Abrir `http://localhost:8000/tools/og.html`
3. F12 → click derecho sobre el elemento `.og` en el inspector → **Capture node screenshot**
4. Guardar como `assets/og.jpg`

## Con Playwright (si está instalado)

```js
const { chromium } = require('playwright');
(async () => {
  const b = await chromium.launch();
  const p = await b.newPage({ viewport: { width: 1200, height: 630 } });
  await p.goto('http://localhost:8000/tools/og.html');
  await p.waitForTimeout(600);                      // que carguen las fuentes
  await p.locator('.og').screenshot({ path: 'assets/og.jpg', quality: 90, type: 'jpeg' });
  await b.close();
})();
```

## URL absoluta

En `index.html`, `og:image` y `og:url` ya apuntan a la URL absoluta de GitHub Pages.
Las rutas relativas no las leen los scrapers de WhatsApp, Instagram ni X. **Si el sitio
pasa a un dominio propio, hay que cambiar las dos.**

WhatsApp guarda en caché la vista previa de cada link: si regenerás la imagen, puede
tardar en actualizarse en los chats donde el link ya se compartió.
Para verificar cómo se ve: https://www.opengraph.xyz/
