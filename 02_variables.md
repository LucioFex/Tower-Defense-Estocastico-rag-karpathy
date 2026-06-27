# 02 — Variables del Modelo (con justificación)

Clasificación según la metodología de la materia: **aleatorias**, **continuas (de estado)**,
**de control (parámetros de diseño)** y **de salida (respuesta)**. Cada variable se justifica:
qué representa, por qué se modela así, y de dónde sale su distribución.

---

## 2.1 Variables ALEATORIAS (de entrada / estocásticas)

Son la fuente de incertidumbre del sistema. Se generan con números pseudoaleatorios
(ver [`04_generadores_aleatorios.md`](./04_generadores_aleatorios.md)).

### V.A.1 — Tiempo entre arribos de enemigos (`TE`)
- **Distribución:** Exponencial negativa de tasa λ. → símbolo **"M"** (markoviano) en Kendall-Lee.
- **Densidad:** `f(t) = λ·e^(−λt)`, `t ≥ 0`. Media `E[TE] = 1/λ`.
- **Justificación:** los arribos de enemigos son eventos independientes que ocurren "al azar" en
  el tiempo; el conteo de arribos en un intervalo es un **proceso de Poisson**, cuyo tiempo entre
  eventos es exactamente exponencial. Es el supuesto estándar de la Unidad III para el patrón de
  llegadas y el que hace al sistema markoviano (sin memoria).
- **Mapeo al juego:** "spawn" de enemigos.

### V.A.2 — Tiempo de servicio por torre (`TS`)
- **Distribución:** Exponencial negativa de tasa μ. → símbolo **"M"**.
- **Densidad:** `f(t) = μ·e^(−μt)`. Media `E[TS] = 1/μ`.
- **Justificación:** el tiempo que tarda una torre en destruir a un enemigo depende de factores no
  controlables (precisión, vida residual del enemigo, ráfaga); la exponencial es la distribución de
  máxima entropía dada solo la media, y mantiene la propiedad markoviana (falta de memoria), lo que
  permite contrastar con las fórmulas cerradas de M/M/c.
- **Mapeo al juego:** duración del "foco de fuego" sobre un enemigo hasta el kill.

### V.A.3 — (Extensión) Tipo de enemigo
- **Distribución:** discreta (p.ej. Bernoulli/categórica: débil/fuerte).
- **Justificación:** variable aleatoria **discreta** (Unidad II) que escala la tasa de servicio
  efectiva (un enemigo "fuerte" tiene μ menor). Permite enriquecer el modelo sin romper el núcleo.
  En la versión base se deja desactivada para preservar el contraste limpio con M/M/c.

---

## 2.2 Variable CONTINUA de estado: Temperatura de la torre `T_i(t)`

Es la variable **continua** exigida por el TP y la que vuelve al sistema **no puramente markoviano**.

- **Dominio:** `T_i(t) ∈ [T_amb, ∞)`, evoluciona en tiempo continuo para cada torre `i`.
- **Dinámica (EDO por tramos):**

  - Mientras la torre **dispara** (sirve), gana calor a tasa constante:
    ```
    dT_i/dt = +k_heat            (estado: BUSY)
    ```
  - Mientras la torre **descansa** (idle / en cooldown), se enfría según la **ley de enfriamiento
    de Newton** (decaimiento exponencial hacia la temperatura ambiente):
    ```
    dT_i/dt = −k_cool · (T_i − T_amb)     (estado: IDLE / COOLDOWN)
    ```
    Solución cerrada del enfriamiento: `T_i(t) = T_amb + (T_0 − T_amb)·e^(−k_cool·(t−t0))`.

- **Umbrales:**
  - `T_max` (sobrecalentamiento): si `T_i ≥ T_max`, la torre entra en **COOLDOWN forzado** y deja
    de atender.
  - `T_resume`: la torre vuelve a estar disponible cuando `T_i ≤ T_resume` (histéresis para evitar
    parpadeo on/off).

- **Justificación:** es una variable de **estado continua** genuina (definida por una ecuación
  diferencial, no por sorteo). Acopla la disponibilidad del servidor a su historia de uso reciente,
  lo que **no** captura M/M/c. Es la razón técnica por la que la simulación aporta valor sobre la
  fórmula. Para el frontend, la temperatura se mapea a **color del sprite** (interpolación azul →
  rojo), conectando la matemática con la visualización.

> Nota de implementación: la EDO se integra de forma **cerrada por tramos** (no hay que resolver
> numéricamente porque cada tramo es lineal o exponencial), y además se **muestrea** periódicamente
> para exportar la serie temporal a `output.json`.

---

## 2.3 Variables de ESTADO discretas (del reloj de simulación)

Heredadas del algoritmo de la Unidad III (lista de eventos):

| Variable | Significado |
|---|---|
| `TM` | Tiempo de reloj de la simulación (env.now en SimPy) |
| `WL` / `queue_len` | Longitud de la línea de espera (enemigos en cola) |
| `n_in_system` | Enemigos en el sistema (en cola + en servicio) |
| `SS_i` | Estado del servidor `i`: IDLE / BUSY / COOLDOWN |
| Lista de eventos | `spawn`, `enqueue`, `start_service`, `kill`, `leak`, `overheat`, `cooldown_done` |

---

## 2.4 Variables de CONTROL (parámetros de diseño / experimentación)

Son las "perillas" que el analista varía para responder el problema. Definen un **escenario**.

| Parámetro | Símbolo | Rol |
|---|---|---|
| Tasa de arribos | λ | Intensidad del ataque |
| Tasa de servicio por torre | μ | Potencia de fuego de cada torre |
| **Número de torres** | **c** | **Variable de decisión principal (sweep 1..N)** |
| Capacidad del sistema | K | Cuántos enemigos caben antes de fuga |
| Tasa de calentamiento | k_heat | Velocidad a la que sube la temperatura |
| Tasa de enfriamiento | k_cool | Velocidad a la que baja la temperatura |
| Umbral de sobrecalentamiento | T_max | Cuándo se apaga la torre |
| Tiempo de simulación | T_sim | Horizonte de la corrida |
| Semilla | seed | Reproducibilidad del experimento |

---

## 2.5 Variables de SALIDA (respuesta / métricas)

Lo que se mide para sacar conclusiones (Unidad III). Se calculan de dos formas y se comparan:
**analítica** (fórmula) vs. **simulada** (conteo en SimPy).

| Métrica | Símbolo | Interpretación |
|---|---|---|
| Utilización | ρ | % del tiempo que las torres están ocupadas |
| Prob. de sistema ocioso | P0 | Probabilidad de que no haya enemigos |
| N° medio en cola | Lq | Cuántos enemigos esperan en promedio |
| N° medio en sistema | L | Enemigos en cola + en servicio |
| Tiempo de espera en cola | Wq | Cuánto espera un enemigo antes de ser atacado |
| Tiempo en sistema | W | Espera + servicio |
| Prob. de bloqueo / **tasa de fuga** | Pb | Fracción de enemigos que llegan a la base |
| Utilización por torre (sim) | — | Reparto de carga entre torres |
| Eventos de sobrecalentamiento | — | Cuántas veces se apagó cada torre |

> **Ley de Little** (control de consistencia): `L = λ·W` y `Lq = λ·Wq`. La simulación debe
> reproducir esta identidad; si no lo hace, hay un error de modelado.
