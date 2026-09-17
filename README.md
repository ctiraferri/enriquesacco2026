# One-page de campaña — tres direcciones de diseño

Landing institucional para un candidato a presidente de un club de fútbol.
Sitio 100% estático, sin build, sin dependencias: se publica tal cual en GitHub Pages.

> ### ⚠️ El nombre que figura es un alias
>
> Todo el material usa **"Ruben Lapaglia"** como nombre de trabajo. **No es el candidato
> real.** Se reemplaza recién en la versión final, junto con el resto del contenido.
>
> El contenido también es de ejemplo: lo que está entre corchetes `[así]` son datos que
> hay que verificar antes de publicar.

---

## Las tres maquetas

| | Dirección | Identidad del club | Tipografía | Cuándo conviene |
|---|---|---|---|---|
| **A** | [Editorial](maqueta-a/) | Alusión, no calco. Rojo solo en numerales y acentos, sobre papel hueso | Libre Bodoni + Public Sans | Se quiere gravedad institucional y diferenciarse del ruido rojo |
| **B** | [Bloques](maqueta-b/) | Explícita. Rojo profundo en campos planos, sin gradientes | Barlow Condensed + Barlow | Se prioriza reconocimiento inmediato y energía de hinchada |
| **C** | [Informe](maqueta-c/) | Casi neutra. El rojo aparece dos veces en toda la página | Outfit + Work Sans | Se quiere leer como gestión seria y no como página de hincha |

El índice en `index.html` las muestra las tres juntas, con paleta y riesgo de cada una.

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

## El movimiento de la maqueta B

B tiene un sistema de animación propio. Las otras dos mantienen un reveal sobrio, acorde a
su carácter — si alguna avanza, se le puede subir el nivel.

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

Aplica a las tres maquetas. Es más común de lo que parece: el **ahorro de batería de
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
- [ ] **Reemplazar el alias "Ruben Lapaglia" por el nombre real**
- [ ] Bio real (los párrafos de ejemplo)
- [ ] Los seis ejes / compromisos con su redacción definitiva
- [ ] Todos los `[corchetes]`: fecha de elección, número de lista, nombres del equipo
- [ ] La cita destacada, con acto y fecha reales
- [ ] Links de redes en el footer (hoy apuntan a `#`)

### Imágenes
Reemplazar los archivos en `assets/` manteniendo el nombre, o cambiar el `src` en el HTML:

| Placeholder | Se usa para | Proporción |
|---|---|---|
| `placeholder-retrato.svg` | Retrato del hero (A y B) | 3:4 |
| `placeholder-wide.svg` | Hero de C y poster del video | 16:9 |
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
`assets/og-a.jpg`, `og-b.jpg` y `og-c.jpg`, 1200×630. Cada maqueta apunta a la suya.
**Llevan el alias impreso**, así que hay que regenerarlas al poner el nombre real —
ver `tools/generar-og.md`.

Al publicar, cambiar `og:image` por la URL **absoluta**: las rutas relativas no las leen
los scrapers de WhatsApp, Instagram ni X.

### Al publicar, borrar
- [ ] El `<div class="mockup-badge">` y su CSS (el cartel "MAQUETA · contenido de ejemplo")
- [ ] `<meta name="robots" content="noindex, nofollow">` de cada página
- [ ] `robots.txt` — hoy bloquea a todos los buscadores a propósito
- [ ] Opcional: la carpeta `tools/`

---

## Decisiones técnicas

- **Sin frameworks ni build.** Un `.html` autocontenido por maqueta, CSS y JS inline.
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
institución. Las maquetas A y C no los utilizan. La maqueta B se apoya en la paleta del
club: si se elige esa dirección, conviene chequear el uso de identidad antes de publicar.
Las tres llevan en el footer la aclaración de que es un sitio no oficial.
