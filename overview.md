---
alias: overview
tags: [overview, mapa]
updated: 2026-06-27
---

# Overview — Tower Defense Estocástico (LLM Wiki)

Punto de entrada a la wiki. Para el catálogo completo ver [[index]]; para las conclusiones vivas
ver [[sintesis]]; para las convenciones y workflows ver `CLAUDE.md`.

## De qué trata
TP de **Simulación de Sistemas** (UCEMA): un *Tower Defense* modelado como **red de colas M/M/c**
y validado con simulación SimPy, reproducido en Godot 2D. El objeto de estudio **no es el juego**
sino la **validación del modelo** (definición del problema, variables, colas, recursos, análisis y
recomendaciones).

## Mapa conceptual

```
        [[caso-de-estudio]]  ── define el problema y el mapeo TD→colas
                │
                ▼
        [[variables]]  ── aleatorias (Exp), continua (temperatura), control, salida
            │        │
            ▼        ▼
[[generadores-aleatorios]]   [[modelo-de-colas]]  ── Kendall-Lee, M/M/1, M/M/c, M/M/c/K
            │                      │
            └──────────┬───────────┘
                       ▼
           [[recursos-y-temperatura]]  ── SimPy + EDO de temperatura
                       │
                       ▼
            [[contrato-datos-json]]  ── output.json (backend → frontend)
                       │
                       ▼
         [[analisis-recomendaciones]] → [[sintesis]]  ── rendimiento marginal, óptimo económico
```

## Las tres capas (patrón LLM Wiki)
1. **Fuentes crudas** (inmutables): PDFs/notebooks de la materia → resumidas en `sources/`.
2. **Wiki** (este repo): páginas generadas por el LLM, interconectadas con `[[wikilinks]]`.
3. **Schema** (`CLAUDE.md`): convenciones + workflows (ingest / query / lint).

## Repos hermanos
- Backend SimPy: `Tower-Defense-Estocastico-back` (genera `output.json`; Módulos A/B/B+ + experimentos).
- Frontend Godot: `Tower-Defense-Estocastico-front` (consume `output.json`).
- Presentación: `Tower-Defense-Estocastico-presentaci-n` (deck HTML temática Tower Defense, GitHub Pages).
