---
alias: log
tags: [log, cronologia]
updated: 2026-06-28
---

# Log — Registro cronológico (append-only)

Cada entrada empieza con `## [AAAA-MM-DD] <tipo> | <título>` para ser parseable:
`grep "^## \[" log.md | tail -5`.

## [2026-06-27] ingest | Simulación Unidad III: Teoría de Colas
Ingerida la teoría base de colas. Creó [[fuente-teoria-de-colas-u3]]; integró en [[caso-de-estudio]],
[[modelo-de-colas]], [[variables]] y [[recursos-y-temperatura]] (notación Kendall-Lee, M/M/1,
algoritmo de lista de eventos).

## [2026-06-27] ingest | Fórmulas Sistemas de Colas tipo MM1
Creó [[fuente-formulas-mm1]]; integró el formulario M/M/1 en [[modelo-de-colas]] §3.3.

## [2026-06-27] ingest | Unidad II: Números Pseudoaleatorios
Creó [[fuente-numeros-pseudoaleatorios-u2]]; integró métodos congruenciales en
[[generadores-aleatorios]].

## [2026-06-27] ingest | Adicional: variable continua exponencial
Creó [[fuente-variable-exponencial]]; integró la transformada inversa `x=-(1/λ)ln(R)` en
[[generadores-aleatorios]] y [[variables]].

## [2026-06-27] ingest | SimPy Clases 1 y 2 (notebooks)
Creó [[fuente-simpy-clases]]; integró el idiom `Resource`/`request`/`timeout` en
[[recursos-y-temperatura]]; nota: el backend usa `Store` para poder apagar torres.

## [2026-06-27] ingest | Unidad II: Variables aleatorias discretas y continuas
Ingesta PARCIAL. Creó [[fuente-variables-aleatorias-u2]]; pendiente profundizar generación de
discretas si se activa la extensión de tipos de enemigo.

## [2026-06-27] synthesis | Contrato output.json v1.0 + primeras corridas
Redactado [[contrato-datos-json]] (v1.0). Corrida base λ=0.4 μ=0.25 c=3 K=10 → evidencia volcada en
[[sintesis]] (rendimiento marginal decreciente confirmado; c*=3; Little 1.6%; fuga sim 6.6% vs
analítica 0.16%).

## [2026-06-27] refactor | Adopción del patrón LLM Wiki
Agregados `CLAUDE.md` (schema/workflows), [[index]], [[log]], [[overview]] y [[sintesis]]. Las
páginas 01–07 recibieron frontmatter (con `alias`) y secciones "Relacionado"/"Fuentes" con
wikilinks. Las fuentes crudas quedaron resumidas en `sources/`.

## [2026-06-27] lint | Estado inicial de la wiki
- Cola de ingesta pendiente: Ejercicios Colas MM1, TP/Guías U2, Inventarios (U3), Unidad I, GPSS,
  AnyLogic.
- Páginas-concepto candidatas a extraer si crece la wiki: `ley-de-little`, `notacion-kendall-lee`,
  `optimo-economico` (hoy viven dentro de [[modelo-de-colas]] y [[analisis-recomendaciones]]).
- Sin contradicciones detectadas entre páginas. Sin huérfanas (todas enlazadas desde [[index]]).
- Próximas preguntas abiertas registradas en [[sintesis]] (no estacionariedad, tipos de enemigo, IC).

## [2026-06-27] experiment | Réplicas+IC, no estacionario y tipos de enemigo
Backend gana `experiments.py` (Módulo B+). Resultados volcados en [[sintesis]]: c*=3 robusto
(IC95% sobre 12 réplicas); oleadas → dimensionar al pico; heterogeneidad con misma media sube la
fuga (varianza importa). Refactor: streams aleatorios independientes (arribos/servicio/tipo) para
números aleatorios comunes; output.json regenerado (fuga base ahora ~4%).

## [2026-06-27] experiment | Prioridad no-preemptiva (regla cμ)
Estudio 4 en `experiments.py` + flag `Scenario(priority=True)` con `simpy.PriorityStore`. Resuelve la
pregunta abierta: la prioridad al "fuerte" baja su Wq (4.24→2.15) a costa del débil, mantiene el Wq
total (work-conserving) y NO cambia la fuga (es por bloqueo, no por espera). Vuelca en [[sintesis]].
Default output.json byte-idéntico (el path homogéneo no consume aleatoriedad extra). 17/17 tests.

## [2026-06-27] project | 4º repo: presentación (deck HTML Tower Defense)
Nuevo repo `Tower-Defense-Estocastico-presentaci-n` (GitHub Pages): deck HTML 1920×1080 con web
component `deck-stage.js`, temática Tower Defense (battlefield, torretas/bichitos/núcleo en SVG),
gráficos del backend re-renderizados en tema oscuro neón, 17 slides estructuradas en "Waves".
Construido con la skill `anthropics/skills@pptx` (descartado el .pptx por preferencia de HTML puro) y
auditado con `wshobson/agents@godot-gdscript-patterns` para el frontend. Integrantes:
Luciano Esteban, Thaiel Anaya, Martín Blasson, Luca Sucri (sin apodos en el deck). Prof.: Sergio
Sirotinsky (Ing. en Informática, UCEMA). Demo en vivo invitada en varias slides. Pendiente: fotos de
las pruebas y activar/confirmar GitHub Pages. Ver [[overview]] §repos hermanos.

## [2026-06-27] verify | Verificación punta a punta del backend + mejora didáctica
Corrido todo el pipeline del back con el venv: 17/17 tests, `main.py` (output.json + auto-auditoría
de esquema), `validate_schema.py` (contrato v1.0), `analysis.py` (5 figuras + conclusiones) y
`experiments.py` (4 estudios + figuras). Reproducibilidad confirmada: regenerar `output.json` da un
modelo **byte-idéntico** (lo único que cambia es `meta.generated_at`, una marca de tiempo de pared).
Mejoras didácticas sin tocar comportamiento (tests siguen 17/17): en `simulation.py`, docstring que
mapea SimPy al **algoritmo de lista de eventos** de la Unidad III (env=FEL+reloj, `timeout`=agendar
evento, procesos=entidades) y los **estimadores** nombrados (Lq=∫q dt/T, Wq=Σwait/n) atados a la Ley
de Little; en el README del back, corregidas cifras viejas pre-refactor (c*=4→3, Little 1.6%→1.2%,
fuga 6.6%→4.0%) que contradecían a `analysis.py` y a [[sintesis]]. Commit `6fd88d1` (sin pushear).

## [2026-06-27] fix | Render del front validado con Godot 4.3 headless (2 bugs de compilación)
Validado por primera vez el repo `front` ejecutando Godot 4.3-stable portátil en modo `--headless`
(`--import` + `--quit-after`). El proyecto **no compilaba**: dos `Parse Error` por inferencia `:=`
sobre el `Variant` que devuelven los globales `max()`/`min()` de Godot 4
(`World.gd:268` `f2`, `World.gd:290` `hp_frac`). Corregido usando las variantes tipadas
`maxf`/`maxi`. Tras el fix: import sin errores ni warnings y la escena corre 600 frames sin error de
runtime (parsing de `output.json`, geometría, tabla de enemigos, interpolación y HUD ejercitados
sobre datos reales). Además se generó el **build web real** (export templates 4.3 instalados;
variante `web_nothreads_*`, PWA con service worker que inyecta COOP/COEP) y se **verificó el render
visual** sirviéndolo local con headers de cross-origin isolation y capturando con Chrome headless vía
CDP (websocket-client): se ve el HUD (M/M/c, c=3 K=10, ρ=0.533, fuga 4.0%), las 3 torres con rango y
color azul→rojo por temperatura, líneas de fuego, enemigos sobre el camino y la base con vida.
Segundo fix cosmético: los glifos de flecha `←/→` del HUD salían como tofu en `ThemeDB.fallback_font`
→ reemplazados por ASCII `[< / >]`. Contrato `output.json` ↔ `World.gd`: 100% alineado.
Ver [[overview]] §repos hermanos.

## [2026-06-27] lint | Cifras del deck son placeholders de la corrida actual
Las métricas que aparecen en el deck (c*=3, Little 1.2%, fuga sim 4.0%, ΔLq=[2.53,0.25,0.05,0.01],
prioridad 4.24→2.15s) provienen de la corrida base y PUEDEN CAMBIAR con la práctica final. Centralizadas
en [[sintesis]] y en el README del repo de presentación (regenerar gráficos con make_dark_charts.py).

## [2026-06-28] doc | Guía de pruebas HTML para el equipo
Creada `back/docs/guia_de_pruebas.html` (commit `3ec3885`, pusheado): documento autocontenido (un
solo archivo, sin dependencias; se abre con doble clic), estética oscura/neón. Tiene el paso a paso de
TODAS las verificaciones (entorno → 17 tests → main → validate_schema → analysis → experiments → front
Godot) con comando, salida esperada real y, en cada paso, el tema de la materia que demuestra. Cierra
con una **matriz "temas de la materia ↔ dónde se prueban"** (16 filas: PRNG congruencial, transformada
inversa, Kendall-Lee, M/M/1, Erlang C, M/M/c/K/bloqueo, Little, simulación por eventos, EDO de
temperatura, validación sim-vs-teoría, IC/réplicas, no estacionario, prioridad cμ, óptimo económico).
QA visual con Chrome headless + CDP. Pensada para defender el TP.

## [2026-06-28] state | HANDOFF — estado para continuar en otra sesión
Snapshot para retomar. **Los 4 repos están pusheados y sincronizados con origin/main** (owner
LucioFex, branch main):
- **rag-karpathy** (esta wiki): mantenida; contrato output.json v1.0; [[sintesis]] con la tesis viva.
- **back**: 17/17 tests; pipeline main/validate/analysis/experiments verificado; reproducibilidad
  byte-idéntica; mejora didáctica (puente al algoritmo de lista de eventos + estimadores/Little);
  README al día; `docs/guia_de_pruebas.html`. venv en `.venv` (sin pytest: correr
  `python tests/test_modelo.py`).
- **front**: Godot 4.3; **compila y render verificado** (headless + build web + screenshot real). Único
  no-versionado: `icon.svg.import` (se regenera). Toolchain de validación (Godot portátil + templates +
  Chrome/CDP) quedó en el scratchpad de la sesión, NO instalado: hay que rebajarlo si se reabre.
- **presentaci-n**: deck HTML 17 slides; sincronizado; cifras = placeholders de la corrida base.

**Pendientes (próxima sesión):**
1. Corrida FINAL de cifras (cuando esté la práctica): re-correr back, actualizar [[sintesis]] + README
   del back + deck (`make_dark_charts.py`) + las cifras de `guia_de_pruebas.html`. Hoy todas son
   placeholders de λ=0.4 μ=0.25 c=3 K=10 seed=42.
2. Sumar FOTOS de las pruebas reales / capturas del juego al deck (`assets/photos/`).
3. Activar/confirmar GitHub Pages del deck.
4. **Bug visual en el deck (slide 1):** el usuario notó que el texto de arriba aparece "pegado" con
   otro texto y se ve mal (probable solape/espaciado en el encabezado de la primera slide). Revisar
   `index.html` de la presentación y corregir el layout/margen del título superior.
5. (Opcional) abrir el front en Godot con GUI para el check visual pixel-a-pixel (el headless cubre
   todo menos eso).
Convenciones vigentes: equipo sin el apodo «Tincho»; commits en español + línea Co-Authored-By;
pushear solo cuando el usuario lo pida.

## [2026-06-28] project | Pulido del deck + guion de presentación por integrante
Trabajo sobre el repo `presentaci-n` (todo pusheado a origin/main):
- **Fotos/nombres del equipo** agrandados en cierre y portada (clase `.members--big`, avatar 62→128px,
  nombre 20→32px). Una 2ª sesión en paralelo resolvió lo mismo a la vez; se consolidó en un único
  mecanismo (`.members--big`) y se quitó el bloque CSS duplicado. **Cuidado: evitar editar `index.html`
  del deck desde dos sesiones simultáneas.**
- **Identificador sutil por slide**: foto del **referente/lead** + nº de slide en el footer (a la
  izquierda de "Ing. en Informática"); sin foto en portada/cierre (ahí ya está todo el equipo).
  Reparto por Waves → Luciano: 01·02·03·04·17 · Thaiel: 05·06·07 · Martín: 08·09·10·16 · Luca: 11·12·13·14·15.
- **Cierre desacoplado de la demo**: la slide 17 «¡Gracias!» ya no reinvita a la demo (la demo es la 15);
  eyebrow "PREGUNTAS & DEMO"→"PREGUNTAS".
- **Animaciones / look & feel** (CSS puro, gated en `[data-deck-active]` del `deck-stage.js`; anuladas en
  `@media print` y `prefers-reduced-motion` → PDF y accesibilidad intactos): entrada en cascada por slide
  (deal-in, re-disparada al volver), grilla viva que se desplaza, barrido holográfico, scanlines CRT,
  baliza de radar en el HUD (latido + anillo), púas que laten, avisos de demo "respirando", shimmer en
  los titulares (`em`) y micro-interacciones al hover.
- **Guion de presentación** nuevo: `guion_presentacion.html` + `.pdf` (22 págs, commit `839d54f`). Una
  sección por **referente/lead**; cada slide con 3 subsecciones: *Puntos a presentar*, *Apoyo teórico/
  práctico* y *★ Apoyo material estudio* (conceptos clave de clase etiquetados por Unidad I/II/III).
  Encuadre de **equipo** (presentación colaborativa; el **cierre lo hace Luciano**). Generado con
  Chrome `--headless --print-to-pdf` (escribir a otra carpeta + `--user-data-dir` temporal por bloqueo
  de perfil). El `.pdf` queda commiteado como binario → al regenerar desde el `.html`, re-commitear ambos.
- Backend re-corrido para sostener cifras (17/17 tests, `main.py`/`analysis.py`) y `make_dark_charts.py`
  regenerado → gráficos **byte-idénticos** (seed 42). Cifras del deck siguen siendo placeholders.

## [2026-06-28] state | HANDOFF — cierre de sesión (deck + guion)
Los 4 repos en `origin/main` (owner LucioFex). Cambios de esta sesión pusheados (deck hasta `131d931`;
guion `839d54f`). **Usuario = Luciano Esteban** (presenta el cierre).
**Pendientes para la próxima sesión:**
1. **Corrida FINAL de cifras** (hoy placeholders λ=0.4 μ=0.25 c=3 K=10 seed=42): re-correr back y propagar
   a [[sintesis]], README del back, deck (`make_dark_charts.py`) y `back/docs/guia_de_pruebas.html`.
2. Sumar **fotos de las pruebas reales** / capturas del juego al deck (`assets/`).
3. **Activar/confirmar GitHub Pages** del deck.
4. (Opcional) check visual del front en Godot con GUI (el headless cubre todo menos eso).
- El bug visual de la slide 1 (encabezado pegado) quedó **resuelto** en sesión previa (commit `d90f9b0`).
- Al regenerar el guion: re-commitear `guion_presentacion.html` + `.pdf` juntos (el PDF es binario).
Convenciones: commits en español + línea Co-Authored-By; pushear solo cuando el usuario lo pida.

## [2026-06-28] project | Sesión grande: overhaul del juego, escenarios y deck/guía auto-explicativos
Trabajo en `front`, `back` y `presentaci-n` (todo pusheado a origin/main).

**Juego (front · Godot 4.3):**
- Overhaul visual MEDIEVAL con assets CC0 (Kenney + 0x72 DungeonTileset II): pasto/camino tileados,
  cueva de goblins en el spawn, torres de piedra, goblins/orcos animados, castillo; ambientación
  (tinte cálido + viñeta + escenografía: lago, árboles, ovejas, ciervo, aves). Ventana 1600x900.
- HUD DIDÁCTICO configurable por teclas (H/L/T/C/G): estado+parámetros, Validación Teoría vs Sim,
  óptimo c* (sweep), gráfico de cola en el tiempo, leyenda. Paneles redondeados con acento de color
  por propósito (verde=validación, dorado=c*, azul=estado/selector, violeta=leyenda) + sombra
  (`_make_panel_box`); gráfico y cartel "Cola" con el mismo estilo (`draw_style_box`).
- SELECTOR de escenarios clickeable (arriba-centro): carga corridas precomputadas en caliente
  (carga λ tranquilo/normal/saturado, torres c=1..6, disciplina FIFO/Prioridad).
- Fixes: goblins caminan CONTINUO cueva→torre (ya no se congelan/apilan); disparo solo DENTRO del
  rango (anillo elíptico, mismo cálculo); sprites anclados por su contenido (no "flotan"); flechas
  con estela. Hook `--shot` para capturas del juego.

**Backend (`back`):**
- `make_scenarios.py`: genera 9 `output.json` en `front/scenarios/` para el selector (corridas Poisson
  válidas; dt_sample=1.0 para achicar). c=3/Normal reusan el `output.json` canónico.
- `docs/guia_de_pruebas.html`: nueva sección "Recorrido del código SimPy — dónde y qué mirar" (6
  fragmentos reales anotados con `archivo:línea`) + `guia_de_pruebas.pdf` generado.

**Deck (`presentaci-n`) — ahora 21 slides:**
- Slides nuevas: 15 Diccionario visual (concepto↔juego), 16 HUD/escenarios, 14 Frontend con screenshot
  real, y **08·09 CÓDIGO SimPy** (fragmentos anotados estilo presentación).
- Auto-documentación: barra **SÍMBOLOS** por slide teórica (define cada símbolo/fórmula); línea
  **FUENTE DE DATOS** en slides de resultados (de dónde sale cada número); **RUTA DEL DATO**
  (Escenario→SimPy→output.json→Deck+Juego) en Arquitectura.
- Arte del juego en el deck con **sprites medievales reales** (símbolos `#td-tower/#td-creep/#td-base`).
- Guion `guion_presentacion.html/.pdf` con apoyos de estudio más auto-explicativos.

**Re-corrida y alineación:** main/analysis/experiments re-corridos; `make_dark_charts.py` regenerado →
gráficas **byte-idénticas** ⇒ deck ALINEADO con la corrida base (seed 42): Little 1.2%,
ΔLq [2.53,0.25,0.05,0.01], c*=3, fuga 4.0% vs 0.16%, oleadas c2=42%/c4=10%, prioridad 4.24→2.15.

**Terminología (criterio de Luciano):** en deck/guion/guía usar SOLO términos vistos en clase
(verificado contra `sources/`). SACADOS por no estar en el programa → Pollaczek-Khinchine/M-G-1,
regla cμ, work-conserving, EOQ (reemplazados por descripción de la materia). **Ley de Little SE
MANTIENE** (decisión de Luciano, aunque no figure en el `source`). **Mersenne Twister NO es del
apunte** (U2 cubre cuadrados medios + congruencial); descripto como "generador por defecto del
lenguaje". Regla general: a los términos con nombre de autor, describir qué representan.

**CONVENCIÓN NUEVA (reemplaza la anterior):** **commitear y pushear SIEMPRE, sin preguntar.**

## [2026-06-28] state | HANDOFF — cierre de sesión (juego + deck + recorrido de código)
4 repos en origin/main (owner LucioFex); todo pusheado. Usuario = **Luciano Esteban**.
Últimos commits: front `64ab13d` · back `61d422e` · presentaci-n `1de57a1`.
**Pendientes próxima sesión:**
1. Corrida FINAL de cifras: **alineada** para λ=0.4 μ=0.25 c=3 K=10 seed=42. Solo re-propagar (back +
   `make_dark_charts.py` + guía + deck) si cambian los parámetros prácticos.
2. (Ofrecido, NO hecho) **Tipos de monstruo VISIBLES** en el juego: goblin vs orco coloreados por tipo
   + Wq por tipo en el HUD (potencia los escenarios FIFO/Prioridad). Requiere que el back emita el
   `tipo` por enemigo en los eventos (contrato v1.1) y que el front lo lea.
3. Activar/confirmar **GitHub Pages** del deck (y, si se quiere web, incluir `scenarios/*.json` en el build).
4. (Opcional) render final del deck completo a PDF para revisión de punta a punta.
5. (Opcional) check visual del front en Godot con GUI (el headless/`--shot` cubre casi todo).
Notas útiles: **Godot 4.3 portátil** en `Documents/UCEMA/anio_5/simul_sis_tp/godot/Godot_v4.3-stable_win64.exe`.
Regenerar guion/guía PDF con Chrome `--headless --print-to-pdf --no-pdf-header-footer --user-data-dir <temp>`.
Capturas del juego: `Godot ... --path <front> --shot` (dispara al llegar la sim a t=50; ruta en `SHOT_PATH`).
Convenciones: commits en español + Co-Authored-By; **commitear y pushear SIEMPRE** (regla nueva); equipo sin «Tincho».

## [2026-06-29] project | Deck: slide "La máquina de la demo" (Luciano) + re-sync del guion a 22 slides
Trabajo en `presentaci-n` (todo pusheado a origin/main).

**Deck (`index.html`) — pasa de 21 a 22 slides:**
- Nueva **slide 03 · "La máquina de la demo"** (referente **Luciano**, arco de apertura, después de
  Recorrido): *establishing shot* de alto nivel del **runtime de la demo en vivo**, complementaria —no
  duplicada— de la slide de **Arquitectura desacoplada** de Luca (ahora la 16, contrato/repos).
  Pipeline vivo con paquete de datos animado por el riel: `SimPy (offline)` —exporta→ `output.json
  (contrato, semilla fija)` —lee→ `Godot 2D (reproduce e interpola)`; nodo "EN VIVO" con badge
  REPRODUCE·CERO CÁLCULO; cue "▶ EN LA DEMO" (selector en caliente: λ/c/FIFO·Prioridad) y tres
  cifras-ancla **1× / v1.0 / 0**. CSS nuevo gated en `[data-deck-active]`, neutralizado en
  `@media print` + `prefers-reduced-motion` → PDF/accesibilidad intactos. Renumerados footers `.sn` y
  comentarios de sección (04..22). El contador actual/total de `deck-stage.js` es **dinámico** (no se
  toca JS al agregar `<section>`). Commit deck `6355e83`.
- **Reparto real del deck (22 slides):** Luciano 01·02·03·04·05·22 · Thaiel 06·07·08·09·10 ·
  Martín 11·12·13·21 · Luca 14·15·16·17·18·19·20.

**Guion (`guion_presentacion.html`/`.pdf`) — re-sincronizado:** estaba en el esquema **viejo de 17
slides** (nunca se regeneró tras el overhaul que llevó el deck a 21). Re-mapeado 1:1 a las 22 slides:
renumeradas las 17 entradas, corregido el reparto y la portada, y **redactadas 5 entradas nuevas** con
los 3 bloques de siempre (Puntos a presentar / Apoyo teórico-práctico / ★ Apoyo material estudio por
Unidad): **03 Panorama**, **09/10 Código SimPy I/II**, **18 Diccionario visual**, **19 HUD/escenarios**.
PDF regenerado (Chrome headless) y re-commiteado junto al `.html`. Commit `b51f5e6`.
Terminología solo-de-clase respetada (nombres de autor descriptos; sin PK/cμ/EOQ/Mersenne como tema).

## [2026-06-29] state | HANDOFF — cierre de sesión (slide demo + guion)
4 repos en origin/main (owner LucioFex); todo pusheado. Usuario = **Luciano Esteban**.
Últimos commits: front `64ab13d` · back `61d422e` · presentaci-n `b51f5e6` (deck `6355e83` + guion `b51f5e6`).
**Estado:** deck = **22 slides** (alta la 03 "La máquina de la demo"); guion alineado 1:1 al deck.
**Pendientes próxima sesión (sin cambios de fondo):**
1. Corrida FINAL de cifras: **alineada** para λ=0.4 μ=0.25 c=3 K=10 seed=42. Solo re-propagar
   (back + `make_dark_charts.py` + guía + deck) si cambian los parámetros prácticos.
2. **Tipos de monstruo VISIBLES** (goblin vs orco por tipo + Wq por tipo en HUD): requiere que el back
   emita el `tipo` por enemigo (contrato **v1.1**) y que el front lo lea. Es el ítem sustancioso pendiente.
3. Activar/confirmar **GitHub Pages** del deck (si se quiere web, incluir `scenarios/*.json` en el build).
4. (Opcional) render final del **deck** completo a PDF (hoy se generó uno de verificación, NO commiteado).
5. (Opcional) check visual del front en Godot con GUI.
Notas útiles: **Godot 4.3 portátil** en `Documents/UCEMA/anio_5/simul_sis_tp/godot/Godot_v4.3-stable_win64.exe`.
PDFs con Chrome `--headless=new --no-pdf-header-footer --user-data-dir <temp> --print-to-pdf`.
Convenciones: commits en español + Co-Authored-By; **commitear y pushear SIEMPRE**; equipo sin «Tincho».
