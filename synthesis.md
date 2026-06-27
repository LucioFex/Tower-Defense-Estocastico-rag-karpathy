---
alias: sintesis
tags: [sintesis, tesis-evolutiva, conclusiones]
sources: [teoria-de-colas-u3, formulas-mm1]
updated: 2026-06-27
---

# Síntesis — Tesis evolutiva del proyecto

Página de conclusiones **vivas**: se actualiza cada vez que una corrida nueva del backend o una
fuente nueva cambia la evidencia. El detalle metodológico está en [[analisis-recomendaciones]].

## Tesis actual

> En el sistema de defensa modelado como **M/M/c (FIFO/K)**, el número de torres tiene un
> **rendimiento marginal fuertemente decreciente**: pasada la primera torre que estabiliza la cola,
> cada torre adicional aporta cada vez menos. El óptimo **no** es "más torres", sino el `c*` que
> minimiza costo de capacidad + costo de fugas. Además, una variable continua realista (la
> **temperatura**) degrada la capacidad efectiva y vuelve la fuga **mayor** que la predicha por la
> fórmula cerrada: por eso **simular** aporta sobre el modelo analítico.

## Evidencia (corrida base: λ=0.4, μ=0.25, c=3, K=10, seed=42, T=1200 s)

| Afirmación | Evidencia | Estado |
|---|---|---|
| Estabilidad ⟺ ρ<1; `c_min` = mínimo estable | ρ(c=3)=0.533; `c_min=2` | ✅ confirmada |
| Rendimiento marginal decreciente (H2) | ΔLq por torre ≈ [2.53, 0.25, 0.05, 0.01]; 1ª torre = 89% de la mejora | ✅ confirmada |
| Óptimo económico interior (H4) | curva CT(c) en U → `c*=3` (C_torre=1, C_fuga=25) | ✅ confirmada |
| Consistencia (Ley de Little) | error `L vs λ_eff·W` ≈ 1.2% | ✅ confirmada |
| Temperatura aumenta la fuga (H3) | fuga sim 4.0% ≫ Pb analítica 0.16% | ✅ confirmada |
| Sin temperatura, sim ≈ analítico | test con `T_max=1e9` → Wq sim ≈ Erlang C (<20%) | ✅ confirmada (engine validado) |

## Experimentos avanzados (módulo `experiments.py`, n=12 réplicas)

| Estudio | Hallazgo | Lectura |
|---|---|---|
| **Réplicas + IC 95%** | fuga(c=3)=4.07%±2.00; `c*=3` robusto en 12 semillas | el óptimo no depende de una corrida afortunada |
| **No estacionario (oleadas)** | dimensionar al promedio (c=2)→fuga 42%, base destruida; al pico (c=4)→fuga 10%, base sobrevive | **dimensionar al pico, no al promedio** |
| **Tipos de enemigo** | con **misma media** de servicio, heterogeneidad (mayor varianza) sube la fuga +0.94 pp | la **varianza** del servicio importa, no solo la media (Pollaczek-Khinchine) |
| **Prioridad no-preemptiva** | atender al "fuerte" primero: Wq_fuerte 4.24→2.15, Wq_débil 4.47→5.88, Wq total ≈ igual, fuga idéntica (4.9%) | la prioridad **reasigna** la espera (work-conserving), no la elimina; conviene si el fuerte es más costoso (**regla cμ**) |

## Recomendaciones (decisión)
1. **Dimensionar a `c*`, no al máximo** — sobre-proveer baja ρ (capacidad ociosa) sin reducir fuga.
2. **Gestión térmica** (subir `k_cool`/`T_max`) suele recuperar capacidad más barato que una torre.
3. **Picos no estacionarios**: dimensionar al pico de λ(t), no al promedio (riesgo de fuga en picos).

## Preguntas (estado)
- ✅ ¿Cómo se mueve `c*` con escenarios **no estacionarios**? → resuelto: dimensionar al pico
  (Estudio 2 de `experiments.py`).
- ✅ ¿Cuánto cambia la fuga con **tipos de enemigo**? → resuelto: la varianza sube la fuga
  (Estudio 3).
- ✅ Intervalos de confianza vía **réplicas** (reducción de varianza) → Estudio 1.
- ✅ Política de **prioridades** (atacar primero al "fuerte"): resuelto (Estudio 4). No baja la fuga
  (es por bloqueo de capacidad, no por espera), pero **reasigna** la espera hacia los débiles y baja
  el tiempo de espera **ponderado por importancia** (regla cμ). Implementado con `PriorityStore`.

## Fuentes
[[fuente-teoria-de-colas-u3]] · [[fuente-formulas-mm1]]
