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
