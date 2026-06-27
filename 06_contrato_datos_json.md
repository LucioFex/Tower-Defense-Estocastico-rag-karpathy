# 06 — Contrato de Datos `output.json` (FUENTE DE VERDAD)

> Este es el **único** punto de acoplamiento entre backend y frontend. El backend **produce**
> exactamente este esquema; el frontend **consume** exactamente este esquema. Cualquier cambio se
> versiona en `meta.schema_version` y se refleja en **ambos** repos. **Contrato versión: `1.0`.**

## 6.0 Principios

- Todos los tiempos en **segundos** (float), eje `t` global de la simulación.
- `events[]` está **ordenado por `t` ascendente** (invariante duro).
- `samples[]` está en una grilla **uniforme** de paso `meta.dt_sample`.
- IDs de torre: enteros `0..c−1`. IDs de enemigo: enteros `≥ 1`, únicos.
- Coordenadas en píxeles lógicos de un lienzo `meta.canvas` (Godot escala).
- El frontend **no calcula nada**: solo lee, interpola y colorea.

## 6.1 Esquema de alto nivel

```jsonc
{
  "meta":      { ... },     // metadatos de la corrida
  "params":    { ... },     // parámetros de control usados
  "layout":    { ... },     // geometría 2D para Godot (camino, torres, base)
  "analytical":{ ... },     // métricas por fórmula (M/M/c y M/M/c/K)
  "stats":     { ... },     // métricas medidas por la simulación
  "events":    [ ... ],     // línea de tiempo de eventos (Godot reproduce)
  "samples":   [ ... ],     // fotos periódicas (Godot interpola, temperatura)
  "series":    { ... },     // series temporales planas (Matplotlib, Módulo B)
  "sweep":     [ ... ]      // barrido de c=1..N (análisis marginal). Puede ser []
}
```

## 6.2 `meta`

```jsonc
"meta": {
  "schema_version": "1.0",          // string, fija
  "model": "(M/M/c)(FIFO/K/inf)",   // string descriptivo
  "seed": 42,                       // int, semilla del PRNG
  "time_unit": "s",                 // string
  "sim_time": 600.0,                // float, horizonte simulado
  "dt_sample": 0.5,                 // float, paso de la grilla de samples
  "num_towers": 3,                  // int = c
  "queue_capacity": 10,             // int = K (cola+servicio); -1 = infinito
  "generated_at": "2026-06-27T19:30:00",  // string ISO-8601 (informativo)
  "canvas": { "w": 1280, "h": 720 }       // tamaño lógico del lienzo 2D
}
```

## 6.3 `params`

```jsonc
"params": {
  "lambda": 0.40,     // float, tasa de arribos [enemigos/s]
  "mu": 0.25,         // float, tasa de servicio por torre [enemigos/s]
  "c": 3,             // int, número de torres
  "K": 10,            // int, capacidad (-1 = infinito)
  "T_amb": 20.0,      // float, temperatura ambiente
  "T_max": 100.0,     // float, umbral de sobrecalentamiento
  "T_resume": 60.0,   // float, umbral de reactivación (histéresis)
  "k_heat": 18.0,     // float, tasa de calentamiento (°/s en BUSY)
  "k_cool": 0.05      // float, constante de enfriamiento de Newton (1/s)
}
```

## 6.4 `layout` (geometría 2D para Godot)

```jsonc
"layout": {
  "spawn": { "x": 40.0,   "y": 360.0 },        // punto de aparición
  "base":  { "x": 1240.0, "y": 360.0 },        // base a defender
  "path":  [ {"x":40,"y":360}, {"x":640,"y":360}, {"x":1240,"y":360} ], // polilínea
  "queue_anchor": { "x": 560.0, "y": 360.0 },  // dónde se apilan los que esperan
  "towers": [                                   // longitud = num_towers
    { "id": 0, "x": 600.0, "y": 240.0, "range": 180.0 },
    { "id": 1, "x": 680.0, "y": 240.0, "range": 180.0 },
    { "id": 2, "x": 640.0, "y": 480.0, "range": 180.0 }
  ]
}
```

`path` es una polilínea; el enemigo avanza por ella en función de `t` entre eventos.

## 6.5 `analytical` (cota teórica por fórmula)

```jsonc
"analytical": {
  "rho": 0.533,          // λ/(c·μ)
  "a": 1.60,             // λ/μ (Erlangs)
  "stable": true,        // ρ < 1
  "P0": 0.186,           // prob. sistema vacío (Erlang)
  "P_wait": 0.237,       // prob. de esperar (Erlang C)
  "L": 1.79, "Lq": 0.19, // n° medio en sistema / en cola
  "W": 4.48, "Wq": 0.48, // tiempos medios
  "Pb_finite": 0.0021,   // prob. de bloqueo del modelo M/M/c/K (la "fuga" teórica)
  "lambda_eff": 0.3991   // λ·(1−Pb)
}
```

## 6.6 `stats` (medido por la simulación)

```jsonc
"stats": {
  "enemies_spawned": 241,
  "enemies_killed": 233,
  "enemies_leaked": 8,
  "leak_rate": 0.033,          // leaked / spawned  (la "fuga" real)
  "avg_wait_q": 0.51,          // Wq simulado
  "avg_time_system": 4.55,     // W simulado
  "avg_queue_len": 0.21,       // Lq simulado (promedio temporal)
  "max_queue_len": 6,
  "avg_in_system": 1.83,       // L simulado
  "rho_sim": 0.541,            // utilización media medida
  "tower_utilization": [0.55, 0.54, 0.53],  // por torre (len = c)
  "overheat_events": [3, 2, 4],             // sobrecalentamientos por torre
  "base_hp_init": 100,         // vida inicial de la base
  "base_hp_end": 92            // vida residual de la base (init − leaks·daño)
}
```

## 6.7 `events` (línea de tiempo — núcleo de la reproducción)

Array ordenado por `t`. Cada evento es `{ "t": float, "type": str, ... }`.
Tipos y campos:

| `type` | Campos extra | Significado |
|---|---|---|
| `spawn` | `enemy_id` | aparece un enemigo en `spawn` |
| `enqueue` | `enemy_id`, `queue_len` | entra a la cola; nuevo largo de cola |
| `start_service` | `enemy_id`, `tower_id` | una torre empieza a atacarlo |
| `kill` | `enemy_id`, `tower_id` | enemigo destruido (sale del sistema) |
| `leak` | `enemy_id`, `base_hp` | enemigo llega a la base (fuga); vida resultante |
| `overheat` | `tower_id`, `temp` | torre alcanza T_max → COOLDOWN |
| `cooldown_done` | `tower_id`, `temp` | torre vuelve a estar disponible |

Ejemplo:
```jsonc
"events": [
  { "t": 0.83,  "type": "spawn",         "enemy_id": 1 },
  { "t": 2.10,  "type": "enqueue",       "enemy_id": 1, "queue_len": 1 },
  { "t": 2.10,  "type": "start_service", "enemy_id": 1, "tower_id": 0 },
  { "t": 6.74,  "type": "kill",          "enemy_id": 1, "tower_id": 0 },
  { "t": 41.2,  "type": "overheat",      "tower_id": 0, "temp": 100.0 },
  { "t": 49.9,  "type": "cooldown_done", "tower_id": 0, "temp": 60.0 },
  { "t": 88.5,  "type": "leak",          "enemy_id": 37, "base_hp": 92 }
]
```

> **Regla para Godot:** un enemigo viaja `spawn → (enqueue|start_service) → (kill|leak)`. Entre
> `spawn` y `start_service` se mueve por `path` hacia `queue_anchor`. En `start_service` la torre
> `tower_id` traza su disparo. En `kill` desaparece; en `leak` cruza hasta `base` y resta vida.

## 6.8 `samples` (fotos periódicas — interpolación y temperatura)

Grilla uniforme de paso `meta.dt_sample`. Godot interpola color de torre y altura de cola.

```jsonc
"samples": [
  {
    "t": 0.0,
    "queue_len": 0,
    "in_system": 0,
    "towers": [
      { "id": 0, "temp": 20.0, "state": "idle", "busy": false },
      { "id": 1, "temp": 20.0, "state": "idle", "busy": false },
      { "id": 2, "temp": 20.0, "state": "idle", "busy": false }
    ]
  }
  // ... hasta t ≈ sim_time
]
```

`state ∈ {"idle","busy","cooldown"}`. `temp` para mapear a color: `t_norm = (temp−T_amb)/(T_max−T_amb)`.

## 6.9 `series` (para Matplotlib, Módulo B)

Columnas paralelas (mismo largo que la grilla de samples), para graficar directo con Pandas:

```jsonc
"series": {
  "time":        [0.0, 0.5, 1.0, ...],          // eje temporal
  "queue_len":   [0, 0, 1, ...],                // longitud de cola
  "in_system":   [0, 1, 1, ...],                // enemigos en el sistema
  "tower_temp":  [ [20.0,...], [20.0,...], [20.0,...] ]  // una fila por torre (len=c)
}
```

## 6.10 `sweep` (barrido de c — análisis de rendimiento marginal)

Resultado de correr el escenario con `c = 1..N` (mismos λ, μ, K, seed). Vacío `[]` si la corrida
no fue un sweep.

```jsonc
"sweep": [
  { "c": 1, "stable": false, "rho": 1.60, "Lq": null, "Wq": null,
    "leak_rate_sim": 0.48, "util_sim": 0.99 },
  { "c": 2, "stable": true,  "rho": 0.80, "Lq": 2.84, "Wq": 7.10,
    "leak_rate_sim": 0.11, "util_sim": 0.79 },
  { "c": 3, "stable": true,  "rho": 0.53, "Lq": 0.19, "Wq": 0.48,
    "leak_rate_sim": 0.03, "util_sim": 0.54 }
  // ...
]
```

Campos: `Lq`/`Wq` de Erlang C (`null` si inestable); `leak_rate_sim`/`util_sim` de la simulación.
Este array alimenta el gráfico de **rendimiento marginal decreciente** y el **óptimo económico**
(ver [`07_analisis_y_recomendaciones.md`](./07_analisis_y_recomendaciones.md)).

## 6.11 Invariantes (auto-auditoría backend ⇄ frontend)

El backend valida antes de exportar; el frontend asume que se cumplen:

1. `len(layout.towers) == meta.num_towers == params.c`.
2. `events` ordenado por `t` no decreciente.
3. Todo `enemy_id` con `kill` o `leak` tuvo antes un `spawn`.
4. `len(series.time) == len(series.queue_len) == len(samples)`.
5. `len(series.tower_temp) == meta.num_towers`.
6. `stats.enemies_spawned == enemies_killed + enemies_leaked + (en sistema al cierre)`.
7. `0 ≤ temp` y `state ∈ {idle,busy,cooldown}` en todo sample.
8. Si `meta.queue_capacity == -1`, no hay eventos `leak`.

El script `validate_schema.py` del backend chequea 1–8 y **falla la build** si algo no cumple.
