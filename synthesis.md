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
| Consistencia (Ley de Little) | error `L vs λ_eff·W` ≈ 1.6% | ✅ confirmada |
| Temperatura aumenta la fuga (H3) | fuga sim 6.6% ≫ Pb analítica 0.16% | ✅ confirmada |
| Sin temperatura, sim ≈ analítico | test con `T_max=1e9` → Wq sim ≈ Erlang C (<20%) | ✅ confirmada (engine validado) |

## Recomendaciones (decisión)
1. **Dimensionar a `c*`, no al máximo** — sobre-proveer baja ρ (capacidad ociosa) sin reducir fuga.
2. **Gestión térmica** (subir `k_cool`/`T_max`) suele recuperar capacidad más barato que una torre.
3. **Picos no estacionarios**: dimensionar al pico de λ(t), no al promedio (riesgo de fuga en picos).

## Preguntas abiertas (alimentan lint / próximas corridas)
- ¿Cómo se mueve `c*` con escenarios **no estacionarios** (oleadas)? → ver [[index]] §experimentos.
- ¿Cuánto cambia la fuga con **tipos de enemigo** (V.A. discreta que escala μ)?
- Intervalos de confianza vía **réplicas** con distintas semillas (reducción de varianza).

## Fuentes
[[fuente-teoria-de-colas-u3]] · [[fuente-formulas-mm1]]
