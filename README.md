# YouTube Now Playing Overlay

Overlay de "Now Playing" para OBS (u otro software de streaming) que reproduce audio de una playlist de YouTube en **shuffle**, oculto detrás de escena, y muestra el título + canal de la canción actual con una animación pixel art estilo Deltarune/Undertale.

No tiene interfaz de YouTube visible: solo el ícono de nota musical, el título/artista, y dos botones (Play/Pause y Siguiente).

## Características

- Reproduce cualquier playlist pública o "no listada" de YouTube
- Orden aleatorio (shuffle) generado al cargar la página
- Título y canal se jalan directo de la YouTube Data API v3 (no dependen del reproductor embebido, que a veces da datos incompletos)
- El texto largo hace *wrap* en vez de cortarse con "..."
- El bloque de título aparece al cambiar de canción y se auto-oculta después de 30 segundos
- Fondo configurable (transparente o color sólido para chroma key)
- Título/artista y controles están en esquinas separadas de la pantalla, para poder recortar (Crop/Pad en OBS) y mostrar solo lo que necesites

## Requisitos

- Una **API Key de YouTube Data API v3** (gratis, ver abajo cómo sacarla)
- El **ID de una playlist de YouTube**
- Un servidor local o cualquier hosting (no funciona abriendo el archivo con doble clic — ver sección de problemas comunes)
- (Opcional) el archivo de la fuente pixel `the-field-of-hopes-and-dreams.otf`

## Configuración

### 1. Consigue tu API Key

1. Ve a [Google Cloud Console](https://console.cloud.google.com/)
2. Crea un proyecto (o usa uno existente)
3. Ve a **APIs & Services > Library**, busca "YouTube Data API v3" y actívala
4. Ve a **Credentials > Create Credentials > API Key**
5. Copia esa key

**Recomendado:** restringe la key en "Application restrictions" a los dominios/hosts donde vayas a usarla (por ejemplo `http://localhost:8000/*`), para que nadie más pueda usarla si se filtra.

### 2. Consigue el ID de tu playlist

En la URL de tu playlist (`youtube.com/playlist?list=XXXXXXX`), el ID es la parte después de `list=`.

### 3. Edita `index.html`

Busca este bloque cerca del inicio del `<script>` y reemplaza los valores:

```js
const API_KEY     = "TU_API_KEY_AQUI";
const PLAYLIST_ID = "TU_PLAYLIST_ID_AQUI";
```

### 4. (Opcional) Agrega la fuente pixel real

El overlay usa como respaldo la fuente **Press Start 2P** (de Google Fonts) hasta que agregues la fuente real:

**[♪~The Field of Hopes and Dreams](https://fontstruct.com/fontstructions/show/2712074/the-field-of-hopes-and-dreams)**, hecha por *Chujin* en FontStruct.

> ⚠️ Esta fuente tiene licencia **FontStruct Non-Commercial** — solo para uso no comercial, con crédito al autor.

Descárgala y colócala en:
```
resources/fonts/the-field-of-hopes-and-dreams.otf
```

No se incluye en este repo por temas de licencia; cada quien la descarga por su cuenta.

## Uso local

No abras `index.html` con doble clic — los navegadores bloquean tanto las peticiones a la API de YouTube como la carga de fuentes locales cuando la página corre bajo el protocolo `file://`. Sírvelo con un servidor local:

```bash
python -m http.server 8000
```

y abre `http://localhost:8000/index.html`.

## Uso en OBS

1. **Fuentes → + → Navegador (Browser)**
2. En "URL" pon `http://localhost:8000/index.html` (no uses "Archivo local")
3. Desmarca la opción **"Shutdown source when not visible"** si quieres que la música siga sonando aunque cambies de escena
4. Como los navegadores bloquean el autoplay con audio, la primera vez necesitas darle clic al botón Play manualmente: clic derecho en la fuente → **Interactuar** → clic en ▶ → cierra esa ventana

## Personalización

- **Colores:** todos los colores del overlay están centralizados en las variables `:root` al inicio del CSS (`--note-color`, `--title-color`, `--artist-color`, `--btn-color`, `--shadow`)
- **Fondo:** cambia la línea `background:` dentro de `html, body` (transparente para overlay normal, o un color sólido tipo `#8000ff` para chroma key)
- **Tiempo de auto-hide:** cambia la constante `AUTO_HIDE_MS` (en milisegundos) en el `<script>`
- **Ícono de nota musical:** es un SVG inline (no una imagen externa), se puede reemplazar por tu propio `<img>` o SVG

## Problemas comunes

| Síntoma | Causa probable |
|---|---|
| Se queda cargando y no pasa nada | Estás abriendo el archivo con doble clic (`file://`) en vez de por `http://localhost` |
| La fuente pixel no se ve | Mismo problema anterior, o la ruta del archivo `.otf` no coincide con tu estructura de carpetas |
| Error 400 al llamar la API | Playlist ID mal copiado, o la API no está activada en tu proyecto de Google Cloud |
| No suena nada aunque el estado diga "reproduciendo" | Falta darle Play manualmente una vez (política de autoplay del navegador) |

## Créditos

- Fuente pixel: [♪~The Field of Hopes and Dreams](https://fontstruct.com/fontstructions/show/2712074/the-field-of-hopes-and-dreams) por Chujin (FontStruct)
- Inspirado visualmente en la pantalla "Now Playing" de *Scarlet Forest* — DELTARUNE (Toby Fox)
