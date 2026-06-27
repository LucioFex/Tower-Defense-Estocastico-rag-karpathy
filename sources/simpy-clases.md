---
alias: fuente-simpy-clases
tags: [fuente, simpy, simulacion]
source_file: "SimPy Clase 1.zip, SimPy Clase 2.zip (notebooks)"
type: notebooks
ingested: 2026-06-27
---

# Fuente — SimPy Clases 1 y 2 (notebooks)

Notebooks de la cátedra con el idiom de SimPy. Base del estilo de `simulation.py`.

## Takeaways clave (del ejemplo Call Center)
- **Recurso**: `simpy.Resource(env, capacity=n)` = n servidores con **cola FIFO** = M/M/c.
- **Cliente/proceso**:
  ```python
  with recurso.request() as req:
      yield req                  # encola y espera turno (FIFO)
      yield env.timeout(ts)      # ocupa el servidor durante el servicio
  ```
- **Arribos**: bucle `while True: yield env.timeout(intervalo); env.process(cliente(...))`.
- **Ejecución**: `env = simpy.Environment(); env.process(setup(...)); env.run(until=T)`.
- Clase 2 agrega: prioridades (`PriorityResource`), **impaciencia** (reneging), y `Store`.
- Se usan `random` y `numpy.random` para las distribuciones.

## Notas de integración
- El backend usa `simpy.Store` como cola FIFO única (en vez de `Resource`) para poder **apagar
  torres** sobrecalentadas (una torre en cooldown simplemente no hace `get()`), algo que `Resource`
  no permite. Ver [[recursos-y-temperatura]].

## Alimenta a
- [[recursos-y-temperatura]] (idiom SimPy, FIFO con Store)
- [[modelo-de-colas]] (algoritmo de eventos → procesos)
