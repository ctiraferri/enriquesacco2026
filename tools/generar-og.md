# Regenerar las imágenes de redes

Las tres imágenes de `assets/og-*.jpg` (1200×630) se generan desde `tools/og.html`.
No hace falta abrir un editor: se edita el texto en el HTML y se vuelve a capturar.

## A mano (Chrome)

1. Levantar el server: `python -m http.server 8000`
2. Abrir `http://localhost:8000/tools/og.html?v=b` (o `?v=a`, `?v=c`)
3. F12 → click derecho sobre el elemento `.og` en el inspector → **Capture node screenshot**
4. Guardar como `assets/og-b.jpg`

## Con Playwright (si está instalado)

```js
const { chromium } = require('playwright');
(async () => {
  const b = await chromium.launch();
  const p = await b.newPage({ viewport: { width: 1200, height: 630 } });
  for (const v of ['a','b','c']) {
    await p.goto(`http://localhost:8000/tools/og.html?v=${v}`);
    await p.waitForTimeout(600);                    // que carguen las fuentes
    await p.locator('.og').screenshot({ path: `assets/og-${v}.jpg`, quality: 90, type: 'jpeg' });
  }
  await b.close();
})();
```

## Antes de publicar

En cada `maqueta-*/index.html`, cambiar `og:image` por la URL **absoluta**:

```html
<meta property="og:image" content="https://usuario.github.io/repo/assets/og-b.jpg">
```

Las rutas relativas no las leen los scrapers de WhatsApp, Instagram ni X.
Para verificar cómo se ve: https://www.opengraph.xyz/
