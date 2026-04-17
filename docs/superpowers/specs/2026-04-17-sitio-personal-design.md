# Diseño: Sitio Personal — Jonathan Montenegro

**Fecha:** 2026-04-17  
**Stack:** Astro + Tailwind CSS + MDX  
**Deploy:** Vercel  
**Idioma:** Español

---

## Objetivo

Sitio personal multi-propósito que funcione simultáneamente como portfolio, blog técnico y presencia profesional. Dirigido a reclutadores, clientes freelance y la comunidad dev.

---

## Estructura de páginas

```
/                          → Home
/sobre-mi                  → CV online
/proyectos/marcus-ai       → MarcusAI — devlog de progreso
/aprendizaje               → Módulos de IA aprendidos
/aprendizaje/[slug]        → Módulo individual
/contacto                  → Links de contacto
```

### Descripción de cada página

**Home (`/`)**
Hero con nombre, título profesional ("Backend Developer · Building MarcusAI"), links a GitHub y LinkedIn. Debajo: último update de MarcusAI y los últimos módulos de aprendizaje.

**Sobre mí (`/sobre-mi`)**
CV online basado en el contenido del CV de Jonathan: experiencia laboral (Interbanking, autónomo), habilidades técnicas (Java, Quarkus, Kafka, MongoDB, Spring Boot, etc.), educación y cursos.

**MarcusAI (`/proyectos/marcus-ai`)**
Página dedicada con:
- Descripción del proyecto (qué es, objetivo, tecnologías)
- Timeline / devlog cronológico: cada entrada es un archivo `.md` con fecha y descripción del progreso

**Aprendizaje (`/aprendizaje`)**
Grid de cards con los módulos de IA que Jonathan va documentando. Cada card muestra título, fecha y descripción corta. Ejemplos: "Cómo armar prompts efectivos", "Qué es RAG", "Function calling en LLMs".

**Módulo individual (`/aprendizaje/[slug]`)**
Contenido del módulo renderizado desde MDX. Incluye: título, fecha y contenido.

**Contacto (`/contacto`)**
Links a LinkedIn, GitHub y email. Sin formulario.

---

## Stack técnico

| Capa | Tecnología | Motivo |
|---|---|---|
| Framework | Astro | Generación estática, ideal para contenido, rápido |
| Estilos | Tailwind CSS | Dark mode nativo, utilidades rápidas |
| Contenido | MDX | Markdown con soporte para componentes Astro |
| Deploy | Vercel | Gratis, CI/CD automático desde GitHub |
| Versionado | GitHub | Repositorio público o privado |

---

## Diseño visual

- **Fondo:** `#0d1117` (dark, estilo GitHub)
- **Texto principal:** `#e6edf3`
- **Acento:** `#00ff9d` (verde eléctrico)
- **Tipografía código:** Monospace (JetBrains Mono o Fira Code)
- **Tipografía prosa:** Sans-serif moderna (Inter o similar)
- **Estética:** Dark mode técnico — mínimo, denso en información, sin decoración innecesaria

---

## Estructura de archivos del proyecto

```
jmontenegro-site/
├── src/
│   ├── content/
│   │   ├── aprendizaje/      # Módulos de IA (.md / .mdx)
│   │   └── marcus-ai/        # Devlog entries de MarcusAI (.md)
│   ├── components/           # Componentes Astro reutilizables
│   ├── layouts/              # Layout base y layout de módulo
│   └── pages/                # Páginas Astro
├── public/                   # Assets estáticos (favicon, imágenes)
├── CONTENT_GUIDE.md          # Guía para crear contenido (ver sección abajo)
├── astro.config.mjs
├── tailwind.config.mjs
└── package.json
```

---

## Flujo de publicación de contenido

1. Crear archivo `.md` en `src/content/aprendizaje/` o `src/content/marcus-ai/`
2. Completar el frontmatter según la guía (`CONTENT_GUIDE.md`)
3. Hacer push a la rama principal en GitHub
4. Vercel detecta el push y regenera el sitio automáticamente (~30 segundos)
5. El contenido queda publicado en la URL correspondiente

No se requiere CMS, base de datos ni proceso de build manual.

---

## CONTENT_GUIDE.md — Guía para crear contenido

Este archivo vivirá en la raíz del repositorio y permite que cualquier LLM (u humano) cree contenido válido sin contexto adicional.

### Módulo de aprendizaje de IA

**Ubicación:** `src/content/aprendizaje/[slug].md`  
**Nombre de archivo:** kebab-case, descriptivo (ej: `como-armar-prompts.md`, `que-es-rag.md`)

**Frontmatter:**

```yaml
---
title: "Cómo armar prompts efectivos"
description: "Una guía práctica sobre prompt engineering para obtener mejores resultados con modelos de lenguaje."
date: 2026-04-17          # formato YYYY-MM-DD
tags: ["prompts", "llm", "ia"]   # opcional, array de strings
draft: false              # true = no se publica, false = se publica
---
```

**Campos obligatorios:** `title`, `description`, `date`, `draft`  
**Campos opcionales:** `tags`

---

### Update de MarcusAI (devlog)

**Ubicación:** `src/content/marcus-ai/[slug].md`  
**Nombre de archivo:** fecha + descripción corta (ej: `2026-04-17-primer-prototipo.md`)

**Frontmatter:**

```yaml
---
title: "Primer prototipo funcional"
description: "Implementé el flujo básico de conversación con memoria de contexto."
date: 2026-04-17          # formato YYYY-MM-DD
tags: ["llm", "python", "memoria"]   # opcional
draft: false
---
```

**Campos obligatorios:** `title`, `description`, `date`, `draft`  
**Campos opcionales:** `tags`

---

### Reglas generales de contenido

- Escribir en español
- No usar HTML inline a menos que sea estrictamente necesario
- Las imágenes van en `public/images/` y se referencian como `/images/nombre.png`
- Los bloques de código deben tener el lenguaje especificado (` ```java `, ` ```python `, etc.)
- El slug de la URL es el nombre del archivo sin extensión

---

## Componentes principales

| Componente | Descripción |
|---|---|
| `Header.astro` | Nav fijo: nombre a la izquierda, links a la derecha |
| `Footer.astro` | Links sociales + "Hecho con Astro" |
| `Hero.astro` | Sección de presentación en Home |
| `ProjectCard.astro` | Card de proyecto con nombre, descripción, tags, link |
| `ModuleCard.astro` | Card de módulo de aprendizaje con título, fecha, descripción |
| `DevlogEntry.astro` | Entrada del timeline de MarcusAI |
| `BaseLayout.astro` | Layout base con Header y Footer |
| `ModuleLayout.astro` | Layout para módulos de aprendizaje |

---

## Fuera de scope (v1)

- Blog general
- Sistema de comentarios
- Autenticación
- CMS visual
- Búsqueda full-text (se puede agregar con Pagefind en v2)
- Versión en inglés
- Newsletter
