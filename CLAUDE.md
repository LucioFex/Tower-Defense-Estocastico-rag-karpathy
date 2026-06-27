# CLAUDE.md — Schema y workflows de esta LLM Wiki

> Este archivo es la **configuración** de la wiki. Le dice al agente LLM cómo está estructurada,
> qué convenciones seguir y qué workflows ejecutar al **ingerir** fuentes, **responder** preguntas
> y **mantener** (lint) la base de conocimiento. Sigue el patrón "LLM Wiki" (Memex de Bush con el
> LLM haciendo el mantenimiento). El humano cura fuentes y hace preguntas; **el LLM escribe y
> mantiene toda la wiki**.

## Las tres capas

1. **Fuentes crudas (inmutables).** Material de la materia en `../../Simulación-de-sistemas-contexto/`
   (PDFs de Unidades I–III, notebooks SimPy). El LLM **lee** de ahí pero **nunca** las modifica.
   Son la fuente de verdad. No viven en este repo (son material protegido de la cátedra); se
   referencian por nombre desde `sources/`.
2. **La wiki (este repo).** Markdown generado por el LLM: resúmenes de fuentes, páginas de
   conceptos, páginas del modelo, contrato de datos, síntesis. El LLM es dueño de esta capa.
3. **El schema (este archivo).** Convenciones + workflows. Co-evoluciona con el dominio.

## Estructura de directorios

```
CLAUDE.md          <- este schema
index.md           <- catálogo de contenido (orientado a contenido)
log.md             <- registro cronológico append-only (orientado a tiempo)
overview.md        <- mapa / punto de entrada a la wiki
synthesis.md       <- tesis evolutiva (conclusiones vivas del proyecto)
README.md          <- portada del repo (apunta a index.md y CLAUDE.md)

sources/           <- un resumen por fuente cruda ingerida (PDF/notebook)
01..07_*.md        <- páginas de la wiki (conceptos, modelo, contrato, análisis)
```

> **Nota de naming.** Las páginas de contenido conservan prefijo numérico (`01_..07_`) porque
> tienen un **orden de lectura** pedagógico y porque el código de los repos hermanos las cita por
> ese nombre (`RAG/03_modelo_de_colas.md`, `RAG/06`...). Para navegación asociativa estilo Obsidian,
> cada página declara un **`alias`** en su frontmatter, de modo que los `[[wikilinks]]` usen nombres
> legibles (p. ej. `[[modelo-de-colas]]`) y no el prefijo numérico.

## Convención de página

Cada `.md` de contenido empieza con frontmatter YAML:

```yaml
---
alias: nombre-legible-kebab        # para [[wikilinks]] y graph view
tags: [concepto|modelo|contrato|fuente|sintesis, unidad-2, unidad-3, ...]
sources: [teoria-de-colas-u3, formulas-mm1]   # fuentes que alimentan esta página
updated: 2026-06-27
---
```

Convenciones del cuerpo:
- **Wikilinks** `[[alias]]` para enlazar páginas. Enlazar generosamente; un `[[alias]]` que aún no
  existe marca una página por crear (trabajo de lint), no un error.
- **Citar fuentes**: secciones que vienen de una fuente cruda enlazan a su página en `sources/`.
- Página de fuente (`sources/*.md`): frontmatter con `source_file`, `type`, `ingested`; cuerpo con
  *takeaways* clave y una sección **"Alimenta a"** con wikilinks a las páginas que actualizó.

## Workflows

### 1. Ingest (incorporar una fuente)
1. Leer la fuente cruda (no modificarla).
2. Discutir *takeaways* con el humano.
3. Escribir/actualizar `sources/<slug>.md` (resumen + "Alimenta a").
4. **Integrar**, no solo archivar: actualizar las páginas de concepto/modelo afectadas, agregar
   wikilinks, y **anotar contradicciones** si la fuente nueva choca con una afirmación previa.
5. Actualizar `index.md`.
6. Append en `log.md`: `## [AAAA-MM-DD] ingest | <Título de la fuente>`.

### 2. Query (responder una pregunta)
1. Leer `index.md` para ubicar páginas relevantes; luego drill-in.
2. Sintetizar respuesta **con citas** a páginas de la wiki y/o `sources/`.
3. Si la respuesta es valiosa y reutilizable, **archivarla** como página nueva (que no muera en el
   chat) y enlazarla desde `index.md`. Append en `log.md`: `## [fecha] query | <pregunta>`.

### 3. Lint (chequeo de salud)
Buscar y reportar: contradicciones entre páginas, afirmaciones obsoletas superadas por fuentes
nuevas, **páginas huérfanas** (sin enlaces entrantes), conceptos mencionados sin página propia,
cross-references faltantes, y huecos de datos que convenga llenar. Proponer nuevas preguntas y
fuentes. Append en `log.md`: `## [fecha] lint | <resumen>`.

## Invariante operativo del proyecto

Esta wiki es además la **fuente de verdad del contrato `output.json`** que desacopla backend (SimPy)
y frontend (Godot). Cualquier cambio al contrato se versiona en `[[contrato-datos-json]]`
(`06_contrato_datos_json.md → meta.schema_version`) y debe reflejarse en **ambos** repos hermanos.
