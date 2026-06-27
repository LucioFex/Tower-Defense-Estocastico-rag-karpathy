# 01 — Caso de Estudio y Problema a Resolver

## 1.1 Descripción del sistema real

Una **base** debe ser defendida de oleadas de **enemigos** que ingresan por un camino (`path`) y
avanzan hacia ella. Sobre el camino hay una **zona de defensa** cubierta por `c` **torres**.
Cuando un enemigo entra en el rango de la zona, queda disponible para ser atacado. Una torre
**enfoca fuego** sobre un único enemigo a la vez hasta destruirlo (no hay fuego dividido).

Si todas las torres están ocupadas, los enemigos que entran al rango **esperan en una cola**
(orden de llegada). La cola tiene una **capacidad física** `K` (cuántos enemigos caben en la zona
antes de que el frente sature). Cuando un enemigo no logra ser atacado a tiempo y atraviesa la
zona, se produce una **fuga**: el enemigo llega a la base y causa daño. La fuga es el evento que
queremos minimizar.

Adicionalmente, cada torre tiene una **temperatura** que sube mientras dispara y baja mientras
descansa. Si una torre se **sobrecalienta**, se apaga forzosamente hasta enfriarse: durante ese
lapso **no puede atender**, reduciendo la capacidad efectiva del sistema.

## 1.2 Traducción a Teoría de Colas (Unidad III)

Este es el corazón del TP: el juego es solo la **fachada**; el objeto de estudio es un
**sistema de líneas de espera**.

| Elemento del juego | Elemento de Teoría de Colas |
|---|---|
| Enemigo | Cliente / transacción |
| Torre | Servidor (canal de servicio) |
| Disparar hasta matar al enemigo | Tiempo de servicio |
| Enemigos en rango esperando turno | Cola (línea de espera) |
| Disciplina: primero en entrar al rango, primero atacado | FIFO |
| Capacidad de la zona | Capacidad del sistema `K` |
| Enemigo que llega a la base | Cliente perdido / bloqueo (balking por cola llena) |
| Sobrecalentamiento de torre | Caída/indisponibilidad del servidor (server breakdown) |

El sistema base es un **M/M/c (FIFO/K/∞)** en notación de Kendall-Lee
(ver [`03_modelo_de_colas.md`](./03_modelo_de_colas.md)).

## 1.3 Problema a resolver (pregunta de simulación)

> **¿Cuál es el número de torres `c` que defiende la base con un nivel de servicio aceptable
> (probabilidad de fuga acotada y espera en cola baja), y a partir de qué punto agregar una torra
> adicional deja de valer la pena por rendimiento marginal decreciente?**

Sub-preguntas operativas:

1. Dada una intensidad de ataque λ y una potencia de fuego μ por torre, ¿el sistema es estable
   (ρ < 1)? ¿Cuántas torres se necesitan como **mínimo** solo para no diverger?
2. ¿Cuánto bajan la longitud de cola `Lq` y el tiempo de espera `Wq` por cada torre que agrego?
3. ¿Dónde está el **óptimo económico**, considerando costo por torre vs. costo por fuga?
4. ¿Cuánto degrada el modelo idealizado M/M/c la **temperatura** (que no es markoviana)? Es decir:
   ¿cuánta capacidad efectiva pierdo por sobrecalentamiento y por qué **la simulación es necesaria**
   y no alcanza con las fórmulas cerradas?

## 1.4 Por qué SIMULAR y no solo aplicar fórmulas

Las fórmulas analíticas de M/M/c (Erlang C) asumen:
- Arribos Poisson y servicio exponencial **puros**.
- Servidores **siempre disponibles**.
- Cola **infinita**.

Nuestro sistema **viola** dos de esos supuestos a propósito:
- Cola **finita** `K` → hay **fuga** (bloqueo), que la fórmula de M/M/c con cola infinita no modela.
- Servidores que se **caen** por temperatura → la capacidad de servicio efectiva fluctúa.

Por eso usamos las fórmulas como **cota teórica / caso de control** y la **simulación SimPy**
para capturar el comportamiento real. La comparación *analítico vs. simulado* es en sí misma una
**validación del modelo** (objetivo central del TP).

## 1.5 Objetivos del trabajo (alineados a la Unidad III)

1. **Identificar el nivel óptimo de capacidad** (número de torres) que minimiza el costo global.
2. **Evaluar el impacto** de modificar la capacidad (sweep de `c`) sobre las métricas del sistema.
3. **Equilibrar costo / calidad de servicio** (torres vs. fugas).
4. Prestar atención al **tiempo de permanencia** en el sistema y en la cola.

> La Teoría de Colas por sí sola no resuelve el problema: **proporciona información para la toma
> de decisiones**. La simulación cuantifica esa decisión bajo supuestos realistas.

## 1.6 Alcance y supuestos

- Régimen **estacionario** (λ y μ constantes en el tiempo durante una corrida).
- Una sola **zona de defensa** con `c` torres compartiendo **una cola única** (caso 1 de la
  Unidad III: una cola de espera con varios servidores).
- Disciplina **FIFO**.
- Enemigos homogéneos en la versión base (extensión posible: tipos de enemigo = variable aleatoria
  discreta que escala μ).
- Sistema **uni-etapa** (sin retroalimentación).
