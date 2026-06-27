---
alias: modelo-de-colas
tags: [concepto, unidad-3, colas]
sources: [teoria-de-colas-u3, formulas-mm1]
updated: 2026-06-27
---

# 03 — Modelo de Colas: Kendall-Lee, M/M/1 y M/M/c

## 3.1 Notación de Kendall-Lee

Formato `(a/b/c)(d/e/f)`:

| Pos | Significado | Valor en este TP |
|---|---|---|
| a | Distribución del tiempo entre llegadas | **M** (exponencial, Poisson) |
| b | Distribución del tiempo de servicio | **M** (exponencial) |
| c | Número de servidores | **c torres** (1..N) |
| d | Disciplina de la cola | **FIFO** |
| e | Capacidad del sistema | **K** (finita) o ∞ |
| f | Tamaño de la población | **∞** (oleadas ilimitadas) |

Símbolos de distribución: `M` = exponencial negativa; `D` = determinística; `Ek` = Erlang-k;
`G` = general; `GI` = general independiente; `H` = hiperexponencial.

- **Modelo de control teórico:** `(M/M/c)(FIFO/∞/∞)` → fórmulas cerradas Erlang C.
- **Modelo simulado realista:** `(M/M/c)(FIFO/K/∞)` + indisponibilidad por temperatura.

El caso `c = 1` reduce a **M/M/1**, el modelo base de la materia.

---

## 3.2 Parámetros fundamentales

```
λ  = tasa media de arribos            [enemigos/s]   E[tiempo entre arribos] = 1/λ
μ  = tasa media de servicio por torre [enemigos/s]   E[tiempo de servicio]   = 1/μ
c  = número de torres (servidores)
a  = λ/μ          intensidad de tráfico ofrecida (Erlangs)
ρ  = λ/(c·μ)      factor de utilización (debe ser < 1 para estabilidad)
```

**Condición de estabilidad:** `ρ < 1` ⟺ `λ < c·μ`. Si no se cumple, la cola crece sin límite
(en el modelo simulado, esto se traduce en fuga masiva por cola finita).

---

## 3.3 M/M/1 (una torre) — fórmulas de la materia

Idénticas a "Fórmulas Sistemas de Colas tipo MM1" del apunte:

```
ρ   = λ / μ                          utilización (= 1 − P0)
P0  = 1 − ρ = 1 − λ/μ                probabilidad de servidor ocioso
L   = λ / (μ − λ)                    n° medio en el sistema
Lq  = λ² / (μ·(μ − λ))               n° medio en cola
W   = 1 / (μ − λ)                    tiempo medio en el sistema
Wq  = λ / (μ·(μ − λ))                tiempo medio de espera en cola
Pn  = (1 − ρ)·ρ^n                    prob. de tener exactamente n en el sistema
```

Relaciones de consistencia: `L = λ·W`, `Lq = λ·Wq`, `L = Lq + ρ`, `W = Wq + 1/μ`.

### Verificación numérica (ejemplo del peaje del apunte)
λ = 0.05, μ = 0.0833 (1/12):
- `Lq = 0.05² / (0.0833·(0.0833−0.05)) ≈ 0.91` autos en cola ✔
- `W  = 1/(0.0833−0.05) ≈ 30.3` s ✔
- `P0 = 1 − 0.05/0.0833 ≈ 0.40` ✔

---

## 3.4 M/M/c (c torres, una cola única) — fórmulas Erlang C

Caso "una cola de espera con varios servidores" (Unidad III, Caso 1). Con `a = λ/μ` y `ρ = a/c`:

### Probabilidad de sistema vacío
```
P0 = [ Σ_{n=0}^{c−1} (aⁿ / n!)  +  (a^c / c!)·(1 / (1 − ρ)) ]^(−1)
```

### Probabilidad de que un enemigo tenga que esperar (fórmula de Erlang C)
```
C(c, a) = P(espera) = [ (a^c / c!) · (1 / (1 − ρ)) ] · P0
```

### Métricas de respuesta
```
Lq = C(c, a) · ρ / (1 − ρ)
Wq = Lq / λ
W  = Wq + 1/μ
L  = Lq + a                 (a = λ/μ = n° medio de servidores ocupados)
```

> **Sanity check:** con `c = 1`, `a = ρ`, y Erlang C se reduce a `C(1,a) = ρ`, de donde
> `Lq = ρ²/(1−ρ)` = la fórmula de M/M/1. ✔ (implementado en el backend como test).

---

## 3.5 M/M/c/K (capacidad finita) — modelo de la FUGA

Cuando la zona de defensa solo admite `K` enemigos (cola + en servicio), el enemigo que llega con
el sistema lleno **se pierde** (fuga a la base). Distribución de estados (`n` = enemigos en sistema):

```
        ⎧ (aⁿ / n!) · P0                 para 0 ≤ n ≤ c
P(n) =  ⎨
        ⎩ (a^c / c!) · ρ^(n−c) · P0      para c ≤ n ≤ K
```

con `P0` normalizado para que `Σ_{n=0}^{K} P(n) = 1`.

- **Probabilidad de bloqueo / tasa de fuga:** `Pb = P(K)` (sistema lleno).
- **Tasa efectiva de arribos atendidos:** `λ_eff = λ·(1 − Pb)`.
- Métricas con `λ_eff`: `L = Σ n·P(n)`, `W = L/λ_eff`, `Lq = L − (λ_eff/μ)`, `Wq = Lq/λ_eff`.

Este modelo da una **cota analítica de la fuga**. La simulación lo refina añadiendo el efecto de
la temperatura (que reduce `c` efectivo de forma intermitente y por lo tanto **aumenta** `Pb`).

---

## 3.6 Algoritmo de simulación por eventos discretos (base conceptual, Unidad III)

El núcleo de SimPy implementa la "lista de eventos" del apunte. Solo hay 2 tipos de evento
elementales: **llegada** y **salida**, más los eventos de temperatura.

```
TM = 0                       # reloj
AT = sorteo Exp(λ)           # próxima llegada
DT = ∞                       # próxima salida (nadie en servicio)
SS = libres                  # estado de servidores
WL = 0                       # longitud de cola

mientras TM < T_sim:
    si AT < DT:              # procesar LLEGADA
        TM = AT
        si hay torre libre y no sobrecalentada:
            asignar enemigo a torre; TS = sorteo Exp(μ); DT = TM + TS
        sino si WL < K−c:    # hay lugar en cola
            WL += 1
        sino:                # sistema lleno  →  FUGA
            registrar leak
        AT = TM + sorteo Exp(λ)
    sino:                    # procesar SALIDA (kill)
        TM = DT
        liberar torre; registrar kill
        si WL > 0:           # pasar siguiente de la cola al servicio
            WL -= 1; TS = sorteo Exp(μ); DT = TM + TS
        sino:
            DT = ∞
    # actualizar temperatura de cada torre según su estado (EDO por tramos)
```

SimPy abstrae esto con `Environment`, `Resource(capacity=c)` y procesos `yield env.timeout(...)`
(ver [`05_recursos_y_temperatura.md`](./05_recursos_y_temperatura.md)). La disciplina por defecto
de `simpy.Resource` es **FIFO**, exactamente la requerida.

---

## Relacionado
[[variables]] · [[recursos-y-temperatura]] · [[generadores-aleatorios]] · [[analisis-recomendaciones]] · [[sintesis]]

## Fuentes
[[fuente-teoria-de-colas-u3]] · [[fuente-formulas-mm1]]
