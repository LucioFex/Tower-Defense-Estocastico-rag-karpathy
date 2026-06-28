---
alias: log
tags: [log, cronologia]
updated: 2026-06-27
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
