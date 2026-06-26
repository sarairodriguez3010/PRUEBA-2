# ♿ Adaptaciones para Alumna con Baja Capacidad Visual
## Dominó Numérico Infantil — Guía de Accesibilidad

> **Nota**: Las sugerencias están divididas en dos categorías:
> - 🎨 **Cambios en el código** (modificaciones directas al proyecto)
> - 📋 **Estrategias pedagógicas** (acciones que tú como docente puedes hacer sin tocar el código)

---

## 🔍 Diagnóstico del proyecto actual

Tras revisar el código, el proyecto ya tiene algunas bases positivas:
- ✅ Usa números de colores diferentes por valor (el 1 es azul, el 3 es rojo, etc.)
- ✅ Usa tipografía redondeada y grande (*Fredoka*, tamaño `2.2rem` en las fichas)
- ✅ Las fichas "jugables" brillan con animación verde
- ❌ **No tiene soporte de audio** (no hay narración ni sonidos al seleccionar fichas)
- ❌ **El tamaño de las fichas en mano es fijo** (100×60px, puede ser insuficiente)
- ❌ **No hay modo de alto contraste** integrado
- ❌ **No hay atributos de accesibilidad** (`aria-label`, `role`, etc.)

---

## 🎨 Categoría 1: Cambios en el Código (Modificaciones técnicas)

### 1.1 — Aumentar el tamaño de las fichas y los números

**Archivo:** [style.css](file:///c:/Users/LENOVO/Downloads/Antigravity/PRUEBA-2/style.css) (línea ~618)

El tamaño actual de las fichas es `100×60px` con números de `2.2rem`. Se recomienda:

```css
/* ANTES */
.domino-tile {
    width: 100px;
    height: 60px;
}
.domino-half {
    font-size: 2.2rem;
}

/* DESPUÉS (para baja visión) */
.domino-tile {
    width: 130px;
    height: 78px;
}
.domino-half {
    font-size: 3rem;   /* Números más grandes */
    font-weight: 900;  /* Trazo más grueso */
}
```

---

### 1.2 — Agregar un Modo de Alto Contraste

Crear un botón de accesibilidad en el header que active colores de alto contraste. Se añadiría en `index.html` y `style.css`:

```css
/* Modo alto contraste: fondo negro, números blancos con borde */
body.high-contrast .domino-tile {
    background: #000;
    border: 4px solid #fff;
}
body.high-contrast .domino-half {
    color: #FFD700 !important; /* Amarillo sobre negro = máxima legibilidad */
    -webkit-text-stroke: 1px black;
}
body.high-contrast .app-container {
    background: #111;
}
```

```js
// En app.js: botón que togglea la clase
document.getElementById('btn-contrast').addEventListener('click', () => {
    document.body.classList.toggle('high-contrast');
});
```

---

### 1.3 — Agregar bordes y texturas (no solo color)

La alumna con baja visión puede depender solo del color, pero **usar también formas** mejora la accesibilidad:

```css
/* Añadir un símbolo de forma además del color en cada número */
/* Por ejemplo, el 1 tiene un círculo, el 2 tiene cuadrado, etc. */
.num-val-1::after { content: "●"; font-size: 0.6rem; }
.num-val-2::after { content: "■"; font-size: 0.6rem; }
.num-val-3::after { content: "▲"; font-size: 0.6rem; }
```

---

### 1.4 — Añadir narración de audio al seleccionar una ficha

Esta es la mejora más impactante para baja visión. Usando la API nativa del navegador `SpeechSynthesis`:

```js
// Agregar en app.js
function speak(text) {
    const utterance = new SpeechSynthesisUtterance(text);
    utterance.lang = 'es-MX';
    utterance.rate = 0.85;  // Velocidad más lenta y clara
    utterance.pitch = 1.1;
    window.speechSynthesis.cancel(); // Cancela audio anterior
    window.speechSynthesis.speak(utterance);
}

// Llamar cuando el jugador selecciona una ficha:
// speak(`Ficha: ${valorIzquierdo} con ${valorDerecho}`);

// Llamar cuando es el turno del jugador:
// speak(`Es el turno de ${jugador.nombre}`);

// Llamar cuando una ficha es válida:
// speak(`Puedes jugar tu ficha de ${a} con ${b}`);
```

**Ejemplos de frases a narrar:**
- Al seleccionar ficha: *"Ficha cuatro con dos"*
- Al iniciar turno: *"Es tu turno, Copito"*
- Al robar del pozo: *"Robaste una ficha del pozo"*
- Al ganar: *"¡Ganaste! ¡Muy bien!"*

---

### 1.5 — Añadir atributos ARIA para tecnologías de asistencia

En `index.html`, cada ficha debería tener descripciones accesibles:

```html
<!-- ANTES -->
<div class="domino-tile">...</div>

<!-- DESPUÉS -->
<div class="domino-tile" 
     role="button" 
     aria-label="Ficha cuatro con dos. Ficha jugable."
     tabindex="0">
</div>
```

Esto permite que **lectores de pantalla del sistema operativo** (como Narrador de Windows) describan las fichas.

---

### 1.6 — Control de zoom integrado en la aplicación

Agregar botones `+` y `-` de zoom en el header:

```js
let zoomLevel = 1;

function increaseZoom() {
    zoomLevel = Math.min(zoomLevel + 0.1, 1.6);
    document.getElementById('app').style.zoom = zoomLevel;
}
function decreaseZoom() {
    zoomLevel = Math.max(zoomLevel - 0.1, 0.8);
    document.getElementById('app').style.zoom = zoomLevel;
}
```

---

## 📋 Categoría 2: Estrategias Pedagógicas (Sin modificar el código)

### 2.1 — Configuración del dispositivo antes de jugar

Antes de abrir el juego, ajusta el dispositivo para la alumna:

| Dispositivo | Acción recomendada |
|---|---|
| **Windows (PC)** | `Ctrl + +` para hacer zoom en el navegador (hasta 150%) |
| **Tablet Android** | Activar "Ampliación" en Accesibilidad → Gestos de zoom |
| **iPad** | Activar "Zoom" en Ajustes → Accesibilidad |
| **Cualquier navegador** | Ir a Configuración del sitio y aumentar tamaño de fuente |

> [!TIP]
> El navegador **Edge** tiene integrado un lector de pantalla y modo de lectura inmersiva. Es la mejor opción para esta alumna.

---

### 2.2 — Ubicación física en el aula

- 🪑 **Sentarla más cerca de la pantalla** (si es proyector o pantalla compartida)
- 💡 **Evitar reflejos de luz** en la pantalla donde juega
- 🖥️ **Usar el dispositivo en modo horizontal (landscape)** para que las fichas sean más grandes
- 👓 Si usa lentes, asegurarse de que los tenga puestos durante la actividad

---

### 2.3 — Apoyo del docente durante el juego

- **Nombrar las fichas en voz alta** cuando sea el turno de la alumna: *"Tienes un tres con cinco, ¿puedes ponerlo?"*
- **Señalar físicamente** en la pantalla cuáles fichas brillan en verde (las jugables)
- **Usar un puntero o lápiz** para indicar los extremos del tablero donde puede colocar

---

### 2.4 — Material físico complementario

Para que la alumna no dependa solo del digital:

- 🃏 **Imprime fichas físicas grandes** con los mismos números de colores del juego
- 🎨 **Usa tarjetas laminadas** con números del 0 al 6 en tipografía grande y negrita
- 🔢 **Tablero auxiliar en papel** donde puedas marcar qué números están en los extremos del tren

---

### 2.5 — Modificación del turno para la alumna

Al inicio de cada turno de la alumna, el docente o un compañero puede:
1. **Leer en voz alta** el número en el extremo izquierdo del tablero
2. **Leer en voz alta** el número en el extremo derecho del tablero
3. **Ayudarle a identificar** qué fichas de su mano coinciden

Esto convierte el juego en una actividad **colaborativa e inclusiva**.

---

### 2.6 — Ajuste de roles en el juego

Si el juego es con todo el grupo en equipo:
- Asignarle a la alumna el **rol de "contadora"**: ella cuenta en voz alta las fichas del pozo
- Al final del juego, que ella sea quien **anuncia los puntos finales** con apoyo del docente
- Esto mantiene su **participación activa** sin que la baja visión sea una barrera

---

## 🗓️ Plan de implementación sugerido

```
Semana 1: Ajustar zoom del navegador + ubicación física en aula
Semana 2: Solicitar al desarrollador aumentar tamaño de fichas (Cambio 1.1)
Semana 3: Activar modo alto contraste (Cambio 1.2)
Semana 4: Integrar narración de audio (Cambio 1.4) — mayor impacto
```

---

## 📌 Resumen de prioridades

| Prioridad | Cambio | Dificultad | Impacto |
|---|---|---|---|
| 🔴 Alta | Narración de audio (SpeechSynthesis) | Media | ⭐⭐⭐⭐⭐ |
| 🔴 Alta | Zoom del navegador al 150% | Ninguna | ⭐⭐⭐⭐⭐ |
| 🟡 Media | Fichas más grandes en CSS | Baja | ⭐⭐⭐⭐ |
| 🟡 Media | Modo alto contraste | Media | ⭐⭐⭐⭐ |
| 🟢 Baja | Atributos ARIA | Baja | ⭐⭐⭐ |
| 🟢 Baja | Material físico complementario | Ninguna | ⭐⭐⭐ |
