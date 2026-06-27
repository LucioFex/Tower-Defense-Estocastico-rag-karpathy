# 07 — Análisis, Rendimiento Marginal Decreciente y Recomendaciones

> Brainstorming analítico: ir más allá de lo obvio. No basta con decir "más torres = mejor".
> Demostramos **matemáticamente** dónde el rendimiento marginal de una torre decrece por
> saturación de la cola, y dónde está el **óptimo económico**.

## 7.1 Hipótesis

**H1.** Existe un `c_min` por debajo del cual el sistema es inestable (`ρ ≥ 1`) y la fuga es masiva.

**H2.** Para `c > c_min`, cada torre adicional reduce `Lq` y la fuga, pero con **retornos
decrecientes**: `ΔLq(c) = Lq(c) − Lq(c+1)` es positivo y **decreciente** (función convexa).

**H3.** La fuga simulada (con temperatura) es **sistemáticamente mayor** que la `Pb` analítica de
M/M/c/K, y la brecha crece con `ρ` (las torres se sobrecalientan más cuando trabajan más).

**H4.** Incorporando costos, el costo total `CT(c)` es **convexo** con un mínimo en `c*` finito:
agregar torres más allá de `c*` cuesta más de lo que ahorra en fugas.

## 7.2 Demostración del rendimiento marginal decreciente (M/M/c)

Sea `a = λ/μ` fijo y `ρ = a/c`. La espera en cola es `Wq(c) = C(c,a) / (c·μ − λ)`, donde `C(c,a)`
es la fórmula de Erlang C. Dos efectos se combinan al aumentar `c`:

1. El **denominador** `(c·μ − λ)` crece **linealmente** con `c` ⇒ `Wq` baja.
2. La **probabilidad de esperar** `C(c,a)` **decae** al alejarse `ρ` de 1.

Ambos empujan `Wq` hacia 0, pero su **producto** decae cada vez más lento en términos absolutos:

```
ΔWq(c) = Wq(c) − Wq(c+1) > 0      y      ΔWq(c+1) < ΔWq(c)      (decreciente)
```

Intuición de la saturación de la cola: la cola solo crece de forma explosiva cerca de `ρ → 1`
(término `1/(1−ρ)` en `Lq = C(c,a)·ρ/(1−ρ)`). Una vez que `c` aleja a `ρ` de 1, el factor
`1/(1−ρ)` ya está cerca de su piso y **ya no hay congestión que aliviar**: las torres extra quedan
**ociosas** (su utilización `ρ = a/c → 0`). La mejora marginal de la siguiente torre es entonces
casi nula porque **la cola ya estaba prácticamente vacía**.

Formalmente, para `ρ` chico, `Lq ≈ C(c,a)·ρ` y `ρ = a/c`, de modo que `Lq = O(1/c · algo que
decae)` → `ΔLq(c) → 0` super-linealmente. **El primer salto** `c_min → c_min+1` captura casi todo
el beneficio; los siguientes aportan migajas. Esto es el rendimiento marginal decreciente, y se
**verifica numéricamente** en el array `sweep` y se grafica en el Módulo B
(`grafico_rendimiento_marginal.png`).

> Lectura económica clave: la utilización `ρ = a/c` también es el indicador de **desperdicio de
> capacidad**. Pasado `c*`, las torres trabajan a baja `ρ` (están ociosas la mayor parte del
> tiempo): pagás un servidor que casi no usás. Ese es el costo invisible de la sobre-provisión.

## 7.3 Óptimo económico (decisión, no solo descripción)

Función de costo total por unidad de tiempo:

```
CT(c) = c · C_torre  +  C_fuga · λ · Pb(c)
        └─ costo de capacidad ┘   └─ costo de servicio insuficiente ┘
```

- `C_torre`: costo de tener una torre activa (mantenimiento, energía).
- `C_fuga`: penalización por cada enemigo que llega a la base.
- `Pb(c)`: probabilidad de fuga (de M/M/c/K analítico **o** de la simulación con temperatura).

Como el primer término **crece linealmente** y el segundo **decrece convexamente** con `c`, `CT(c)`
tiene un **mínimo interior** `c*`. La regla marginal de parada:

```
agregar la torre c+1 conviene  ⟺  C_fuga · λ · [Pb(c) − Pb(c+1)]  >  C_torre
```

es decir, mientras el ahorro marginal en fugas supere el costo marginal de la torre. `c*` es el
**primer** `c` donde la desigualdad se invierte. Esta es la **recomendación cuantitativa** del TP.

## 7.4 Validación del modelo (analítico vs. simulado)

El TP **valida** el modelo comparando:

| Métrica | Fórmula (control) | Simulación SimPy | Criterio |
|---|---|---|---|
| `Wq`, `Lq` | M/M/c (Erlang C) | promedios medidos | error relativo bajo en régimen estable |
| `Pb` (fuga) | M/M/c/K | `leak_rate` | sim ≥ analítico (gap por temperatura, H3) |
| Little | `L = λ·W` | medido | debe cumplirse (chequeo de consistencia) |

Un acuerdo razonable en régimen estable **valida** la implementación; la **brecha controlada** en
fuga **valida** que la temperatura aporta realismo (sin ella, sim ≈ analítico).

## 7.5 Conclusiones automatizadas (las imprime el Módulo B)

El script de análisis imprime en terminal, entre otras:
- `c_min` (estabilidad) y `c*` (óptimo económico).
- % del beneficio total capturado por la primera torre sobre `c_min`.
- Brecha fuga simulada vs. analítica (efecto temperatura).
- Verificación de la Ley de Little.

## 7.6 Recomendaciones y extensiones

1. **Dimensionar a `c*`, no al máximo:** sobre-proveer torres desperdicia capacidad (baja `ρ`) sin
   reducir fuga apreciablemente (H2).
2. **Gestión térmica:** subir `k_cool` o `T_max` recupera capacidad efectiva sin agregar torres —
   suele ser más barato que una torre nueva (analizable variando `params`).
3. **Escenario no estacionario (oleadas):** λ(t) por tramos para modelar picos; el `c*` debería
   dimensionarse al pico, no al promedio (riesgo de fuga en los picos).
4. **Prioridades (visto en clase):** atacar primero enemigos "fuertes" (PriorityResource) podría
   bajar el daño total aun con la misma `c`.
5. **Reducción de varianza:** usar números aleatorios comunes entre escenarios del sweep (ya
   aplicado) y múltiples réplicas con distinta semilla para intervalos de confianza.
