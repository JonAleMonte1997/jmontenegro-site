# CONTENT_GUIDE.md

Guía para crear contenido en este sitio. Cualquier LLM o persona puede usar este archivo para agregar contenido válido sin contexto adicional.

---

## Módulo de aprendizaje de IA

**Ubicación:** `src/content/aprendizaje/[slug].md`

**Nombre de archivo:** kebab-case, descriptivo, sin fechas.
- Correcto: `como-armar-prompts.md`, `que-es-rag.md`, `function-calling-en-llms.md`
- Incorrecto: `2026-04-17-prompts.md`, `Prompts.md`

**Frontmatter obligatorio:**

```yaml
---
title: "Título del módulo"
description: "Descripción corta de una oración que aparece en las cards."
date: 2026-04-17
draft: false
---
```

**Frontmatter opcional:**

```yaml
tags: ["prompts", "llm", "ia"]
```

**Ejemplo completo:**

```markdown
---
title: "Cómo armar prompts efectivos"
description: "Guía práctica de prompt engineering para obtener mejores resultados con modelos de lenguaje."
date: 2026-04-17
tags: ["prompts", "llm"]
draft: false
---

## Introducción

Contenido del módulo en Markdown...
```

---

## Update de MarcusAI (devlog)

**Ubicación:** `src/content/marcus-ai/[slug].md`

**Nombre de archivo:** fecha ISO + descripción corta en kebab-case.
- Correcto: `2026-04-17-primer-prototipo.md`, `2026-05-01-memoria-implementada.md`

**Frontmatter obligatorio:**

```yaml
---
title: "Título del update"
description: "Descripción corta de una oración que aparece en el timeline."
date: 2026-04-17
draft: false
---
```

**Frontmatter opcional:**

```yaml
tags: ["python", "llm", "memoria"]
```

**Ejemplo completo:**

```markdown
---
title: "Primer prototipo funcional"
description: "Implementé el flujo básico de conversación con memoria de contexto usando LangChain."
date: 2026-04-17
tags: ["python", "langchain"]
draft: false
---

Detalle del update (opcional — el campo description es suficiente para el timeline).
```

---

## Reglas generales

- Escribir en **español**
- El slug de la URL = nombre del archivo sin extensión
- `draft: true` = el archivo existe pero NO se publica en el sitio
- Las imágenes van en `public/images/` y se referencian como `/images/nombre.png`
- Los bloques de código llevan el lenguaje especificado: ` ```java `, ` ```python `, ` ```bash `
- No usar HTML inline salvo que sea estrictamente necesario

---

## Workflow para publicar

1. Crear el archivo `.md` en la carpeta correspondiente
2. Completar el frontmatter con los campos obligatorios
3. Escribir el contenido en Markdown
4. Hacer `git add` y `git push` a la rama principal
5. Vercel detecta el push y regenera el sitio en ~30 segundos
