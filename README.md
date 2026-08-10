# Diseño de Sesión de Readaptación · Racing Club Ferrol

Panel web para diseñar sesiones de readaptación (gimnasio y, si procede, bloque de campo), con el mismo lenguaje visual que el **Protocolo Preventivo** de la cantera, y con los parámetros de carga que ya se usaban en los libros Excel (`Diseño de Sesión de Readaptación` y `Seguimiento Individual de Readaptación`).

No sustituye el criterio médico ni fisioterapéutico. Es una herramienta de apoyo para el diseño y registro de sesiones; las decisiones clínicas son siempre del cuerpo médico.

## Qué incluye

- **Banco de ejercicios** organizado en 4 bloques — Calentamiento/activación, Bloque principal · Gimnasio, Bloque principal · Campo, Vuelta a la calma — filtrado automáticamente por la **fase RTP** seleccionada (Fase 1 Aproximación → Fase 6 RTP). Cada ejercicio es una tarjeta editable con material, series, repeticiones, carga/intensidad, RPE, descanso, notas/precauciones y enlace a vídeo (YouTube). Se pueden añadir ejercicios propios.
- **Selector de ubicación**: Solo gimnasio / Gimnasio + campo / Solo campo, para sesiones mixtas o de campo cuando la fase lo permite.
- **Ficha de sesión** en formato tabla (igual que la plantilla de Excel: ID · Ejercicio · Series · Repeticiones · Carga/Intensidad · RPE · Descanso · Notas), editable en línea, reordenable, con minutos por bloque e imprimible.
- **Resumen y carga de la sesión**: duración total, RPE de la sesión y carga (session-RPE = duración × RPE) calculados en vivo, más dolor EVA post-sesión, cumplimiento y observaciones/tolerancia.
- **Registro y carga**: guarda cada sesión ejecutada por jugador y calcula automáticamente **carga aguda (7 días)**, **carga crónica (media semanal de 4 semanas)**, **ACWR**, **monotonía** y **strain**, con el mismo criterio orientativo que el Excel (ACWR 0.8–1.3 óptimo, >1.5 riesgo alto), más un mini-gráfico de carga diaria de los últimos 7 días.
- Guardado automático, ficha imprimible (`Imprimir ficha`) y botón de `Nueva sesión`.

## Cómo abrirlo

No necesita instalación ni servidor: es un único archivo HTML autocontenido.

- **Directo**: descarga `index.html` y ábrelo con cualquier navegador.
- **GitHub Pages** (recomendado para uso compartido por todo el cuerpo técnico): sigue la sección *Publicar en GitHub Pages* más abajo.

## Guardado de datos: nube vs. navegador

El panel detecta automáticamente dónde se está ejecutando:

- **Dentro de Claude** (como artefacto): usa el almacenamiento compartido de Claude — todo el cuerpo técnico que abra el panel en Claude ve el mismo banco de ejercicios y el mismo registro de carga.
- **Como archivo independiente / GitHub Pages**: usa `localStorage` del navegador. Los datos quedan guardados **solo en ese dispositivo/navegador**, no se comparten automáticamente entre coordinadores. Si necesitáis un registro compartido de verdad en producción, hay que conectar un backend (por ejemplo, una hoja de Google Sheets vía Apps Script, Firebase, o una pequeña API) — el código está organizado para que sustituir las funciones `stGet`/`stSet` por llamadas a ese backend sea el único cambio necesario.

## Crear el repositorio en GitHub

```bash
cd rcf-readaptacion-dashboard
git init
git add .
git commit -m "Panel de diseño de sesión de readaptación RCF"
git branch -M main
git remote add origin https://github.com/<tu-usuario-u-organizacion>/rcf-readaptacion-dashboard.git
git push -u origin main
```

Si el repositorio va a contener datos de jugadores lesionados (a través del registro guardado en `localStorage` no es el caso, pero si en el futuro conectáis un backend), **creadlo como repositorio privado** en GitHub.

## Publicar en GitHub Pages

1. En GitHub → **Settings → Pages → Build and deployment → Source**, elegid **GitHub Actions**.
2. Hay un workflow ya incluido en `.github/workflows/deploy.yml` que publica el sitio automáticamente en cada `push` a `main`.
3. Tras el primer despliegue, la URL aparece en **Settings → Pages** (algo como `https://<usuario>.github.io/rcf-readaptacion-dashboard/`).

Alternativa manual sin Actions: **Settings → Pages → Source → Deploy from a branch → main / (root)**.

## Estructura del repositorio

```
rcf-readaptacion-dashboard/
├── index.html                    # Aplicación completa (HTML + CSS + JS, sin dependencias de build)
├── README.md
├── .gitignore
└── .github/
    └── workflows/
        └── deploy.yml             # Despliegue automático a GitHub Pages
```

## Personalizar el banco de ejercicios

El banco inicial (unos 30 ejercicios) está pensado como punto de partida, con la misma estructura que la pestaña *Banco de Ejercicios* del Excel. Para editarlo en bloque en vez de uno a uno desde la interfaz, abre `index.html` y busca el objeto `BANK` dentro del `<script>` (sección `DATOS: BANCO DE EJERCICIOS`); cada ejercicio es un objeto con:

```js
{id:'g1', n:'Nombre del ejercicio', mat:'Material', minFase:3,
 series:'3', reps:'4-6', carga:'Descripción de la carga/intensidad',
 rpe:'5', descanso:'90"', note:'Precaución opcional', ev:'Nota de criterio opcional'}
```

`minFase` (1–6) controla a partir de qué fase RTP aparece el ejercicio en el banco.

## Fórmulas de carga utilizadas

Las mismas que en el libro Excel `Diseño de Sesión de Readaptación` (hoja *Instrucciones*):

- Carga de sesión (session-RPE) = Duración (min) × RPE (escala CR-10, 0–10).
- Carga aguda = suma de la carga de los últimos 7 días. Carga crónica = media semanal de las últimas 4 semanas.
- ACWR = carga aguda / carga crónica. Zona orientativa 0.8–1.3; > 1.5 asociado a mayor riesgo.
- Monotonía = carga media diaria de la semana / desviación estándar diaria. Strain = carga semanal × monotonía.

Es una herramienta orientativa de tendencia de carga, no un predictor exacto de lesión individual.

## Compatibilidad

Sin dependencias de build ni frameworks: HTML + CSS + JavaScript vanilla, con Google Fonts (Oswald, Inter, IBM Plex Mono) cargadas por CDN. Funciona en cualquier navegador moderno, escritorio o móvil.
