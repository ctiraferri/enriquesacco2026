# Quique Sacco 2026 — One-page de campaña

Landing institucional de la candidatura de Quique Sacco a presidente del Club Atlético
Independiente. Sitio 100% estático, sin build, sin dependencias: se publica tal cual en
GitHub Pages.

**Online:** https://ctiraferri.github.io/enriquesacco2026/

> ### ⚠️ El contenido todavía es de ejemplo
>
> Textos, fotos y datos son de muestra. Lo que está entre corchetes `[así]` son datos que
> hay que verificar antes de publicar.

---

## Dirección de diseño: Bloques

Identidad del club explícita: rojo profundo en campos planos, sin gradientes. Condensada
gigante, franja de ticker, filas que se llenan de color. Tipografía **Barlow Condensed +
Barlow**. Todo el sitio es `index.html`.

Se exploraron otras dos direcciones (A · Editorial y C · Informe) y se descartaron a favor
de esta. Quedan en el historial de git, en el commit `e1b075f`, por si hace falta rescatar
algo. El link viejo `/maqueta-b/` redirige a la home.

---

## Ver en local

```bash
python -m http.server 8000
# abrir http://localhost:8000
```

Abrir los `.html` con doble clic también funciona, pero el video embebido y el botón de
compartir necesitan `http://` para andar.

> Si editás y no ves los cambios, es caché del navegador: recargá con **Ctrl+F5**.

> **Si no ves ninguna animación**, revisá si tu sistema tiene las animaciones apagadas
> (en Windows: Configuración → Accesibilidad → Efectos visuales → Efectos de animación).
> El sitio respeta esa preferencia y se queda quieto a propósito. Para previsualizar sin
> cambiar nada: F12 → Ctrl+Shift+P → "Emulate CSS prefers-reduced-motion: no-preference".

---

## El movimiento

| Qué | Cómo |
|---|---|
| Entrada del hero | Cada línea del título sube desde su propia máscara, escalonada 90 ms. La foto se revela con `clip-path` y el bloque rojo entra barriendo |
| Barra de progreso | Línea roja de 3px arriba, atada al scroll |
| Franja / ticker | Loop continuo, se **pausa al pasar el mouse**, bordes difuminados |
| Cifras | Cuentan de 0 al valor final al entrar en pantalla |
| Filas de ejes | El rojo entra barriendo desde la izquierda, no de golpe |
| Botones | Relleno que barre de izquierda a derecha |

Todo usa **sólo `transform` y `opacity`** — no dispara layout ni repaint, así que va fluido
en celulares modestos. No hay ninguna librería: son ~90 líneas de JS sin dependencias.

### Con `prefers-reduced-motion`

Es más común de lo que parece: el **ahorro de batería de
Samsung** lo activa solo, igual que "quitar animaciones" en Android o Windows. Por eso no
se apaga todo — se apaga lo que marea, y se conserva lo que da respuesta:

| Se apaga | Se conserva |
|---|---|
| Desplazamientos de los reveals | Fundidos de opacidad |
| Barrido del título desde su máscara | Transiciones de color al hover |
| `clip-path` de la foto | Feedback en botones y filas |
| Barra de progreso | Cifras en su valor final |
| Franja roja (movimiento automático) | |
| Escalas y parallax | |

El criterio: un fundido o un cambio de color **no son movimiento** para efectos de la
pauta de accesibilidad. Apagarlos dejaba la página muerta para una porción grande de la
audiencia real sin ganar nada a cambio.

### Editar la franja roja

Los dos `<div class="strip__group">` son **idénticos a propósito**: es lo que hace que el
loop cierre sin salto. Si cambiás la lista de palabras, **cambiá las dos**. Conviene que la
lista sea larga: si es corta, en pantallas anchas se ve el mismo texto repetido dos veces.

---

## Publicar en GitHub Pages

**Settings → Pages → Source: Deploy from a branch → `main` / `(root)`.**

El `.nojekyll` ya está incluido (evita que GitHub procese el sitio con Jekyll).

**Dominio propio:** Settings → Pages → Custom domain. Es gratis; solo se paga el dominio
(~USD 12/año). Apuntar un `CNAME` a `usuario.github.io` y GitHub emite el HTTPS solo.

---

## Qué falta antes de publicar de verdad

### Contenido
- [ ] Bio real (los párrafos de ejemplo)
- [ ] Los seis ejes / compromisos con su redacción definitiva
- [ ] Todos los `[corchetes]`: fecha de elección, número de lista, nombres del equipo
- [ ] La cita destacada, con acto y fecha reales
- [ ] Links de redes en el footer (hoy apuntan a `#`)

### Imágenes
Reemplazar los archivos en `assets/` manteniendo el nombre, o cambiar el `src` en el HTML:

| Placeholder | Se usa para | Proporción |
|---|---|---|
| `placeholder-retrato.svg` | Retrato del hero | 3:4 |
| `placeholder-wide.svg` | Poster del video | 16:9 |
| `placeholder-cuadrado.svg` | Fotos del equipo | 1:1 |
| `placeholder-logo.svg` | Logo de campaña en el footer | ~3:1 |

Exportar en **WebP**, ≤ 1600px de ancho y ≤ 250 KB.

### Video de YouTube
Buscar `data-yt=""` y poner el ID del video (lo que va después de `v=` en la URL):

```html
<button class="video" data-yt="AbCdEfGhIjK" ...>
```

Es una *fachada*: la página **no** carga nada de YouTube hasta el click. Evita ~1 MB de
descarga y los cookies de tracking en la carga inicial.

### Imagen para redes
`assets/og.jpg`, 1200×630. **El texto está impreso en la imagen**: si cambia el nombre o
el claim, no alcanza con editar el HTML, hay que regenerarla — ver `tools/generar-og.md`.

`og:image` y `og:url` ya usan la URL **absoluta** de GitHub Pages, así que el link
compartido por WhatsApp sale con vista previa. **Si se pasa a dominio propio, hay que
cambiar las dos.**

### Al publicar, borrar
- [ ] El `<div class="mockup-badge">` y su CSS (el cartel "MAQUETA · contenido de ejemplo")
- [ ] `<meta name="robots" content="noindex, nofollow">` de `index.html`
- [ ] `robots.txt` — hoy bloquea a todos los buscadores a propósito
- [ ] Opcional: la carpeta `tools/`

---

## Decisiones técnicas

- **Sin frameworks ni build.** Un solo `index.html` autocontenido, CSS y JS inline.
- **Mobile-first**, verificado a 375 px y 1440 px: sin scroll horizontal y con todos los
  targets táctiles en 44 px o más.
- **Accesibilidad**: `lang="es-AR"`, skip link, foco visible, jerarquía de headings
  correcta, íconos en SVG (nunca emojis), `prefers-reduced-motion` respetado.
- **Reveals a prueba de anclas**: si alguien entra por `#equipo` o recarga a mitad de
  página, un barrido revela lo que quedó fuera del alcance del observer. Sin eso esas
  secciones quedarían invisibles — es un bug clásico de este patrón.
- **Peso**: la única request externa es Google Fonts. Sin analytics, sin cookies,
  sin banner de consentimiento.
- **Sin formularios**, por decisión de alcance. GitHub Pages no tiene backend: si más
  adelante se quieren captar adhesiones, se resuelve con Formspree o un Google Form.

## Nota legal

El escudo, el nombre y los signos distintivos del club son marcas registradas de la
institución. El sitio no usa el escudo, pero se apoya en la paleta del club: conviene
chequear el uso de identidad antes de publicar. El footer aclara que es un sitio no
oficial.
