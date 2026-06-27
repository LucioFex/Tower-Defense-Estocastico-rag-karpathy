---
alias: index
tags: [index, catalogo]
updated: 2026-06-27
---

# Index — Catálogo de la wiki

Catálogo orientado a contenido. Para la cronología ver [[log]]; para el mapa ver [[overview]].

## Entrada y síntesis
| Página | Resumen |
|---|---|
| [[overview]] (`overview.md`) | Punto de entrada y mapa conceptual de la wiki |
| [[sintesis]] (`synthesis.md`) | Tesis evolutiva + evidencia de las corridas (conclusiones vivas) |

## Modelo (específico del proyecto)
| Página | Resumen |
|---|---|
| [[caso-de-estudio]] (`01_caso_de_estudio.md`) | Problema, mapeo Tower Defense → colas, objetivos, alcance |
| [[variables]] (`02_variables.md`) | Variables aleatorias / continua (temperatura) / control / salida |
| [[recursos-y-temperatura]] (`05_recursos_y_temperatura.md`) | Recursos SimPy + EDO de temperatura (por qué rompe M/M/c) |

## Conceptos (teoría transferible)
| Página | Resumen |
|---|---|
| [[modelo-de-colas]] (`03_modelo_de_colas.md`) | Kendall-Lee, M/M/1, M/M/c (Erlang C), M/M/c/K, algoritmo de eventos |
| [[generadores-aleatorios]] (`04_generadores_aleatorios.md`) | PRNG congruenciales + transformada inversa exponencial |
| [[analisis-recomendaciones]] (`07_analisis_y_recomendaciones.md`) | Hipótesis, rendimiento marginal decreciente, óptimo económico |

## Contrato de datos
| Página | Resumen |
|---|---|
| [[contrato-datos-json]] (`06_contrato_datos_json.md`) | Esquema exacto de `output.json` v1.0 (tipos, eventos, invariantes) |

## Fuentes ingeridas (`sources/`)
| Página | Fuente cruda | Estado |
|---|---|---|
| [[fuente-teoria-de-colas-u3]] | Simulación Unidad III Teoría de Colas.pdf | completa |
| [[fuente-formulas-mm1]] | Fórmulas Sistemas de Colas tipo MM1.pdf | completa |
| [[fuente-numeros-pseudoaleatorios-u2]] | Unidad II Números Pseudoaleatorios.pdf | completa |
| [[fuente-variable-exponencial]] | Adicional variable continua exponencial.pdf | completa |
| [[fuente-simpy-clases]] | SimPy Clase 1 y 2 (notebooks) | completa |
| [[fuente-variables-aleatorias-u2]] | Unidad II Variables aleatorias discretas y continuas.pdf | parcial |

## Fuentes disponibles aún no ingeridas (cola de trabajo / lint)
- Ejercicios de Colas MM1.pdf · Guía/TP Unidad II · Unidad III Inventarios · Unidad I · Guía GPSS ·
  Ejercicios AnyLogic. (Ver [[log]] para el detalle de prioridades.)

## Schema
- `CLAUDE.md` — convenciones de la wiki y workflows (ingest / query / lint).
