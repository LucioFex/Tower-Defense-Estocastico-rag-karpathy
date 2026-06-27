# Tower Defense Estocástico — RAG / LLM Wiki

> Materia: **Simulación de Sistemas** (UCEMA). Entregable: **validación de un modelo de colas**, no un videojuego.
> Metodología: **LLM Wiki** (estilo Karpathy RAG) — `.md` planos, crudos y densos, mantenidos por el
> LLM como un wiki **persistente e interconectado**, no como chunks que se re-descubren en cada query.

Este repositorio es la **fuente de verdad** del proyecto. Define el problema, las variables, el
modelo matemático de colas, los generadores de números pseudoaleatorios y —crítico— el
**contrato de datos `output.json`** que desacopla el backend (Python/SimPy) del frontend (Godot 4).

## Cómo navegar esta wiki

- **Empezá por** [`overview.md`](./overview.md) (mapa) o [`index.md`](./index.md) (catálogo completo).
- **Conclusiones vivas:** [`synthesis.md`](./synthesis.md). **Cronología:** [`log.md`](./log.md).
- **Cómo se mantiene** (convenciones + workflows ingest/query/lint): [`CLAUDE.md`](./CLAUDE.md).
- **Fuentes** de la materia resumidas e integradas en [`sources/`](./sources).
- Navegación asociativa con `[[wikilinks]]` (Obsidian): cada página declara su `alias` en el
  frontmatter. La carpeta es un repo git → historial y graph view gratis.

### Las tres capas
1. **Fuentes crudas** (inmutables): PDFs/notebooks de la cátedra → resumidas en `sources/`.
2. **Wiki** (este repo): páginas `.md` generadas por el LLM, interconectadas.
3. **Schema** (`CLAUDE.md`): convenciones y workflows que vuelven al LLM un mantenedor disciplinado.

## Regla de oro de la arquitectura

**Cero acoplamiento en tiempo real.**

```
[ Python + SimPy ]  --(output.json)-->  [ Godot 4 / 2D ]
   hace la matemática        contrato        solo reproduce e interpola
```

- Python ejecuta la simulación estocástica y exporta `output.json` (una sola vez, offline).
- Godot **no calcula nada**: lee la línea de tiempo de eventos y reproduce gráficamente.
- El esquema de `output.json` es ley: ver [`06_contrato_datos_json.md`](./06_contrato_datos_json.md).

## Índice de páginas de contenido

> Catálogo navegable y por categorías en [`index.md`](./index.md). Los prefijos numéricos marcan el
> orden de lectura y son citados por el código de los repos hermanos (`RAG/03_...`, `RAG/06`).

| Archivo | Contenido |
|---|---|
| [`01_caso_de_estudio.md`](./01_caso_de_estudio.md) | Caso de estudio, problema a resolver, objetivo académico |
| [`02_variables.md`](./02_variables.md) | Variables: aleatorias, continuas, de estado, de control, de salida (justificación) |
| [`03_modelo_de_colas.md`](./03_modelo_de_colas.md) | Notación Kendall-Lee, M/M/1 y M/M/c, ecuaciones y derivaciones |
| [`04_generadores_aleatorios.md`](./04_generadores_aleatorios.md) | PRNG congruenciales, transformada inversa de la exponencial |
| [`05_recursos_y_temperatura.md`](./05_recursos_y_temperatura.md) | Gestión de recursos (SimPy) y dinámica continua de temperatura |
| [`06_contrato_datos_json.md`](./06_contrato_datos_json.md) | Contrato exacto del `output.json` (schema, tipos, invariantes) |
| [`07_analisis_y_recomendaciones.md`](./07_analisis_y_recomendaciones.md) | Hipótesis, rendimiento marginal decreciente, óptimo económico |

## Repos hermanos

- Backend: `Tower-Defense-Estocastico-back` (SimPy + análisis Matplotlib/Pandas)
- Frontend: `Tower-Defense-Estocastico-front` (Godot 4, 2D estricto, export web)

## Glosario rápido de símbolos

| Símbolo | Significado | Unidad |
|---|---|---|
| λ (`lambda`) | Tasa media de arribos de enemigos | enemigos / s |
| μ (`mu`) | Tasa media de servicio por torre (kills/s) | enemigos / s |
| c | Número de torres (servidores) | — |
| ρ (`rho`) | Factor de utilización = λ/(c·μ) | adimensional |
| a | Intensidad de tráfico ofrecida = λ/μ | Erlangs |
| K | Capacidad total del sistema (cola + servicio) | enemigos |
| Lq, L | N° medio en cola / en el sistema | enemigos |
| Wq, W | Tiempo medio de espera en cola / en sistema | s |
| P0 | Probabilidad de sistema ocioso | adimensional |
| Pb | Probabilidad de bloqueo (fuga del enemigo) | adimensional |
