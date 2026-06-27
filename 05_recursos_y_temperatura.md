# 05 — Gestión de Recursos (SimPy) y Dinámica de Temperatura

## 5.1 Recursos en SimPy

El servidor (torre) se modela con `simpy.Resource`. El idiom (visto en clase — Call Center) es:

```python
class ZonaDefensa:
    def __init__(self, env, c, mu):
        self.env = env
        self.torres = simpy.Resource(env, capacity=c)   # c servidores, cola FIFO
        self.mu = mu

def enemigo(env, eid, zona):
    llegada = env.now
    with zona.torres.request() as req:      # se forma en la cola (FIFO)
        yield req                            # espera a tener torre libre
        espera = env.now - llegada           # Wq de este enemigo
        ts = random.expovariate(zona.mu)     # tiempo de servicio Exp(μ)
        yield env.timeout(ts)                # la torre dispara hasta el kill
```

- `simpy.Resource(capacity=c)` = **c servidores con UNA cola FIFO** = M/M/c exacto.
- `request()`/`yield req` = encolar y esperar turno.
- `yield env.timeout(ts)` = ocupar la torre durante el servicio.
- La **capacidad finita K** se implementa rechazando (fuga) cuando
  `len(cola) + en_servicio ≥ K` **antes** de hacer el `request()`.

> Para prioridades o impaciencia (vistos en clase: `PriorityResource`, balking/reneging) hay
> extensiones, pero el modelo base usa FIFO puro para contrastar con Erlang C.

## 5.2 Por qué la temperatura rompe M/M/c (y por qué eso es el punto)

M/M/c asume **c servidores siempre disponibles**. La temperatura introduce **caídas de servidor**:
una torre sobrecalentada no atiende. Esto reduce el `c` **efectivo** de forma intermitente y
**estado-dependiente** (depende de la historia de uso), violando la cadena de Markov simple.
Consecuencia medible: la fuga simulada será **mayor** que la `Pb` analítica de M/M/c/K. Cuantificar
esa brecha es una de las conclusiones del TP.

## 5.3 Modelo físico de la temperatura

Cada torre `i` tiene `T_i(t)` que evoluciona por tramos según su estado:

### Calentamiento (estado BUSY, disparando)
Ganancia de calor a tasa constante:
```
dT_i/dt = +k_heat        ⇒   T_i(t) = T_0 + k_heat·(t − t0)     (rampa lineal)
```

### Enfriamiento (estado IDLE o COOLDOWN) — Ley de enfriamiento de Newton
```
dT_i/dt = −k_cool·(T_i − T_amb)
⇒  T_i(t) = T_amb + (T_0 − T_amb)·e^(−k_cool·(t − t0))          (decaimiento exponencial)
```

> Conexión con la Unidad II: el enfriamiento es una **variable continua exponencial**; la rampa de
> calentamiento es lineal/determinística. La temperatura es la **variable de estado continua** del TP.

### Máquina de estados de la torre

```
        T_i ≥ T_max
 BUSY ───────────────► COOLDOWN (forzado, no atiende)
  ▲                        │
  │ asigna enemigo          │ T_i ≤ T_resume
  │                        ▼
 IDLE ◄───────────────── (disponible de nuevo)
  (enfría hacia T_amb)
```

- **T_max:** umbral de sobrecalentamiento → apaga la torre.
- **T_resume < T_max:** histéresis → reactiva la torre (evita on/off rápido).
- En BUSY normal (sin llegar a T_max) la torre termina el kill y pasa a IDLE.

## 5.4 Integración eficiente (sin solver numérico)

Como cada tramo tiene **solución cerrada** (lineal en calentamiento, exponencial en enfriamiento),
**no** se integra numéricamente: se evalúa la fórmula del tramo en cada cambio de estado y en cada
instante de **muestreo**. Esto es clave para correr en **Ubuntu de bajos recursos**: O(eventos),
sin paso de integración fino.

## 5.5 Muestreo para la serie temporal

Un proceso `monitor` de SimPy toma una foto cada `dt_sample` (p.ej. 0.5 s):
- `queue_len`, `n_in_system`
- por torre: `temp`, `state`, `busy`

Esto alimenta:
- `output.json → samples[]` (para que Godot interpole y coloree),
- `output.json → series` (para los gráficos de Matplotlib del Módulo B).

## 5.6 Resumen del mapeo a la visualización (Godot)

| Estado físico | Señal en `output.json` | Render en Godot |
|---|---|---|
| Temperatura `T_i` | `samples[].towers[].temp` | color del sprite (azul→rojo) |
| BUSY | evento `start_service` / `state` | torre disparando (línea al enemigo) |
| COOLDOWN | evento `overheat`/`cooldown_done` | torre "apagada" (gris/parpadeo) |
| Cola | `samples[].queue_len` | enemigos apilados antes de la zona |
| Fuga | evento `leak` | enemigo cruza y baja la vida de la base |
