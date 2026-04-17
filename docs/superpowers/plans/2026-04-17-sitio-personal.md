# Sitio Personal — Jonathan Montenegro — Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Construir un sitio personal estático con Astro que funcione como presencia profesional, devlog de MarcusAI y hub de módulos de aprendizaje de IA.

**Architecture:** Generación estática con Astro. El contenido vive como archivos `.md` en `src/content/`. Agregar contenido = crear un archivo y hacer push. Vercel detecta el push y regenera el sitio automáticamente. Sin base de datos, sin CMS.

**Tech Stack:** Astro v4, Tailwind CSS v3, @tailwindcss/typography, MDX, Vercel

---

## Mapa de archivos

| Archivo | Responsabilidad |
|---|---|
| `src/content/config.ts` | Schemas de las colecciones de contenido |
| `src/layouts/BaseLayout.astro` | Shell HTML con Header y Footer |
| `src/layouts/ModuleLayout.astro` | Layout para páginas de módulos de aprendizaje |
| `src/components/Header.astro` | Barra de navegación fija |
| `src/components/Footer.astro` | Footer con links sociales |
| `src/components/Hero.astro` | Sección hero del Home |
| `src/components/DevlogEntry.astro` | Entrada individual del devlog de MarcusAI |
| `src/components/ModuleCard.astro` | Card con link a un módulo de aprendizaje |
| `src/pages/index.astro` | Home |
| `src/pages/sobre-mi.astro` | CV online |
| `src/pages/proyectos/marcus-ai.astro` | Timeline del devlog de MarcusAI |
| `src/pages/aprendizaje/index.astro` | Grid de módulos de aprendizaje |
| `src/pages/aprendizaje/[slug].astro` | Página de módulo individual |
| `src/pages/contacto.astro` | Links de contacto |
| `src/content/marcus-ai/*.md` | Entradas del devlog |
| `src/content/aprendizaje/*.md` | Módulos de aprendizaje |
| `CONTENT_GUIDE.md` | Guía de contenido legible por LLMs |

---

### Task 1: Inicializar el proyecto Astro

**Files:**
- Create: `astro.config.mjs`
- Create: `tailwind.config.mjs`
- Create: `src/env.d.ts`

- [ ] **Step 1: Crear el proyecto Astro con template minimal**

Desde `/Users/jonathanalexismontenegro/repos/jmontenegro-site`:

```bash
npm create astro@latest . -- --template minimal --install --no-git
```

Si el CLI pregunta si se debe continuar en un directorio con archivos existentes, responder **yes**. Cuando pregunte por TypeScript, elegir **Strict**. Cuando pregunte por instalar dependencias, elegir **Yes**.

- [ ] **Step 2: Agregar integración Tailwind**

```bash
npx astro add tailwind
```

Responder **Yes** a todo lo que pregunte.

- [ ] **Step 3: Agregar integración MDX**

```bash
npx astro add mdx
```

Responder **Yes** a todo lo que pregunte.

- [ ] **Step 4: Instalar @tailwindcss/typography**

```bash
npm install @tailwindcss/typography
```

- [ ] **Step 5: Configurar colores y tipografía en tailwind.config.mjs**

Reemplazar el contenido de `tailwind.config.mjs` con:

```javascript
/** @type {import('tailwindcss').Config} */
export default {
  content: ['./src/**/*.{astro,html,js,jsx,md,mdx,svelte,ts,tsx,vue}'],
  theme: {
    extend: {
      colors: {
        bg: '#0d1117',
        surface: '#161b22',
        text: '#e6edf3',
        muted: '#8b949e',
        accent: '#00ff9d',
      },
      fontFamily: {
        mono: ['"JetBrains Mono"', '"Fira Code"', 'monospace'],
        sans: ['Inter', 'system-ui', 'sans-serif'],
      },
    },
  },
  plugins: [require('@tailwindcss/typography')],
};
```

- [ ] **Step 6: Verificar que el proyecto compila**

```bash
npm run build
```

Resultado esperado: `Build complete.` sin errores.

- [ ] **Step 7: Commit**

```bash
git init
git add .
git commit -m "chore: init proyecto Astro con Tailwind y MDX"
```

---

### Task 2: Configurar colecciones de contenido

**Files:**
- Create: `src/content/config.ts`
- Create: `src/content/marcus-ai/.gitkeep`
- Create: `src/content/aprendizaje/.gitkeep`

- [ ] **Step 1: Crear el schema de las colecciones**

Crear `src/content/config.ts`:

```typescript
import { defineCollection, z } from 'astro:content';

const marcusAi = defineCollection({
  type: 'content',
  schema: z.object({
    title: z.string(),
    description: z.string(),
    date: z.coerce.date(),
    tags: z.array(z.string()).optional(),
    draft: z.boolean().default(false),
  }),
});

const aprendizaje = defineCollection({
  type: 'content',
  schema: z.object({
    title: z.string(),
    description: z.string(),
    date: z.coerce.date(),
    tags: z.array(z.string()).optional(),
    draft: z.boolean().default(false),
  }),
});

export const collections = {
  'marcus-ai': marcusAi,
  aprendizaje,
};
```

- [ ] **Step 2: Crear las carpetas de contenido**

```bash
mkdir -p src/content/marcus-ai src/content/aprendizaje
touch src/content/marcus-ai/.gitkeep src/content/aprendizaje/.gitkeep
```

- [ ] **Step 3: Verificar que el proyecto compila**

```bash
npm run build
```

Resultado esperado: `Build complete.` sin errores.

- [ ] **Step 4: Commit**

```bash
git add src/content/
git commit -m "feat: configurar colecciones de contenido marcus-ai y aprendizaje"
```

---

### Task 3: BaseLayout, Header y Footer

**Files:**
- Create: `src/layouts/BaseLayout.astro`
- Create: `src/components/Header.astro`
- Create: `src/components/Footer.astro`

- [ ] **Step 1: Crear Header**

Crear `src/components/Header.astro`:

```astro
---
const navLinks = [
  { href: '/sobre-mi', label: 'Sobre mí' },
  { href: '/proyectos/marcus-ai', label: 'MarcusAI' },
  { href: '/aprendizaje', label: 'Aprendizaje' },
  { href: '/contacto', label: 'Contacto' },
];

const currentPath = Astro.url.pathname;
---
<header class="border-b border-surface sticky top-0 z-10 bg-bg/90 backdrop-blur-sm">
  <nav class="max-w-3xl mx-auto px-4 py-4 flex items-center justify-between">
    <a href="/" class="font-mono text-accent font-medium tracking-tight hover:opacity-80 transition-opacity">
      jmontenegro
    </a>
    <ul class="flex gap-6 text-sm">
      {navLinks.map(link => (
        <li>
          <a
            href={link.href}
            class={`transition-colors ${currentPath.startsWith(link.href) ? 'text-text' : 'text-muted hover:text-text'}`}
          >
            {link.label}
          </a>
        </li>
      ))}
    </ul>
  </nav>
</header>
```

- [ ] **Step 2: Crear Footer**

Crear `src/components/Footer.astro`:

```astro
---
---
<footer class="border-t border-surface mt-16">
  <div class="max-w-3xl mx-auto px-4 py-6 flex items-center justify-between text-sm text-muted">
    <span>
      Hecho con{' '}
      <a href="https://astro.build" target="_blank" rel="noopener" class="hover:text-accent transition-colors">
        Astro
      </a>
    </span>
    <div class="flex gap-4 font-mono text-xs">
      <a href="https://github.com/[tu-usuario-github]" target="_blank" rel="noopener" class="hover:text-accent transition-colors">
        GitHub
      </a>
      <a href="https://www.linkedin.com/in/jonathan-alexis-montenegro/" target="_blank" rel="noopener" class="hover:text-accent transition-colors">
        LinkedIn
      </a>
    </div>
  </div>
</footer>
```

> **Nota:** Reemplazar `[tu-usuario-github]` con tu usuario real de GitHub.

- [ ] **Step 3: Crear BaseLayout**

Crear `src/layouts/BaseLayout.astro`:

```astro
---
import Header from '../components/Header.astro';
import Footer from '../components/Footer.astro';

interface Props {
  title: string;
  description?: string;
}

const { title, description = 'Sitio personal de Jonathan Montenegro — Backend Developer' } = Astro.props;
---
<!doctype html>
<html lang="es">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <meta name="description" content={description} />
    <link rel="icon" type="image/svg+xml" href="/favicon.svg" />
    <title>{title === 'Home' ? 'Jonathan Montenegro' : `${title} | Jonathan Montenegro`}</title>
    <link rel="preconnect" href="https://fonts.googleapis.com" />
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
    <link
      href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600&family=JetBrains+Mono:wght@400;500&display=swap"
      rel="stylesheet"
    />
  </head>
  <body class="bg-bg text-text font-sans min-h-screen flex flex-col">
    <Header />
    <main class="flex-1 max-w-3xl mx-auto w-full px-4 py-12">
      <slot />
    </main>
    <Footer />
  </body>
</html>
```

- [ ] **Step 4: Actualizar index.astro para usar BaseLayout**

Reemplazar el contenido de `src/pages/index.astro` con:

```astro
---
import BaseLayout from '../layouts/BaseLayout.astro';
---
<BaseLayout title="Home">
  <p class="text-muted">Próximamente.</p>
</BaseLayout>
```

- [ ] **Step 5: Verificar que el proyecto compila**

```bash
npm run build
```

Resultado esperado: `Build complete.` sin errores.

- [ ] **Step 6: Verificar visualmente**

```bash
npm run dev
```

Abrir `http://localhost:4321`. Verificar: fondo oscuro (`#0d1117`), header con navegación, footer con links.

- [ ] **Step 7: Commit**

```bash
git add src/layouts/ src/components/ src/pages/index.astro
git commit -m "feat: agregar BaseLayout, Header y Footer"
```

---

### Task 4: Hero component y Home page completa

**Files:**
- Create: `src/components/Hero.astro`
- Create: `src/components/DevlogEntry.astro`
- Create: `src/components/ModuleCard.astro`
- Modify: `src/pages/index.astro`

- [ ] **Step 1: Crear Hero**

Crear `src/components/Hero.astro`:

```astro
---
---
<section class="py-16 border-b border-surface">
  <p class="font-mono text-accent text-sm mb-3">$ whoami</p>
  <h1 class="text-4xl font-semibold mb-2 tracking-tight">Jonathan Montenegro</h1>
  <p class="text-muted text-lg mb-8">Backend Developer · Building MarcusAI</p>
  <div class="flex gap-3 text-sm font-mono">
    <a
      href="https://github.com/[tu-usuario-github]"
      target="_blank"
      rel="noopener"
      class="border border-accent text-accent px-4 py-2 hover:bg-accent hover:text-bg transition-colors"
    >
      GitHub
    </a>
    <a
      href="https://www.linkedin.com/in/jonathan-alexis-montenegro/"
      target="_blank"
      rel="noopener"
      class="border border-surface text-muted px-4 py-2 hover:border-muted hover:text-text transition-colors"
    >
      LinkedIn
    </a>
  </div>
</section>
```

> **Nota:** Reemplazar `[tu-usuario-github]` con tu usuario real de GitHub.

- [ ] **Step 2: Crear DevlogEntry**

Crear `src/components/DevlogEntry.astro`:

```astro
---
interface Props {
  title: string;
  description: string;
  date: Date;
}

const { title, description, date } = Astro.props;

const formattedDate = date.toLocaleDateString('es-AR', {
  year: 'numeric',
  month: 'long',
  day: 'numeric',
});
---
<article class="border-l-2 border-surface pl-4 py-1 hover:border-accent transition-colors">
  <time class="font-mono text-xs text-muted">{formattedDate}</time>
  <h3 class="text-text font-medium mt-1">{title}</h3>
  <p class="text-muted text-sm mt-1">{description}</p>
</article>
```

- [ ] **Step 3: Crear ModuleCard**

Crear `src/components/ModuleCard.astro`:

```astro
---
interface Props {
  title: string;
  description: string;
  date: Date;
  slug: string;
}

const { title, description, date, slug } = Astro.props;

const formattedDate = date.toLocaleDateString('es-AR', {
  year: 'numeric',
  month: 'short',
  day: 'numeric',
});
---
<a
  href={`/aprendizaje/${slug}`}
  class="block border border-surface p-4 hover:border-accent transition-colors group"
>
  <time class="font-mono text-xs text-muted">{formattedDate}</time>
  <h3 class="text-text font-medium mt-1 group-hover:text-accent transition-colors">{title}</h3>
  <p class="text-muted text-sm mt-1">{description}</p>
</a>
```

- [ ] **Step 4: Crear contenido seed para que la Home no quede vacía**

Crear `src/content/marcus-ai/2026-04-17-inicio-del-proyecto.md`:

```markdown
---
title: "Inicio del proyecto"
description: "Arranqué MarcusAI. Definí el objetivo: un asistente de IA personal con memoria de contexto persistente."
date: 2026-04-17
draft: false
---

Arranqué el proyecto con la idea de construir un asistente de IA que me ayude en mi día a día como desarrollador. El foco inicial es entender cómo funciona la memoria de contexto en los LLMs.
```

Crear `src/content/aprendizaje/como-armar-prompts.md`:

```markdown
---
title: "Cómo armar prompts efectivos"
description: "Guía práctica de prompt engineering: estructuras, patrones y ejemplos concretos para obtener mejores resultados con modelos de lenguaje."
date: 2026-04-17
draft: false
---

## ¿Qué es un prompt?

Un prompt es la instrucción que le das a un modelo de lenguaje. La calidad del resultado depende directamente de cómo estructures esa instrucción.

## Principios básicos

**1. Sé específico sobre el formato de salida**

En lugar de: "explicame qué es una API REST"
Mejor: "Explicame qué es una API REST en 3 bullets, en español, para alguien que ya sabe programar en Java"

**2. Dá contexto de rol**

"Sos un desarrollador senior revisando este código. ¿Qué cambiarías y por qué?"

**3. Usá ejemplos (few-shot prompting)**

Mostrarle al modelo 1-2 ejemplos del output que esperás antes de pedirle que genere uno nuevo mejora drásticamente la calidad.

## Patrones útiles

- **Chain of thought:** pedirle que piense paso a paso antes de responder
- **Role prompting:** asignarle un rol específico
- **Output format:** especificar el formato exacto de la respuesta (JSON, lista, párrafo)
```

- [ ] **Step 5: Actualizar Home page**

Reemplazar `src/pages/index.astro`:

```astro
---
import BaseLayout from '../layouts/BaseLayout.astro';
import Hero from '../components/Hero.astro';
import DevlogEntry from '../components/DevlogEntry.astro';
import ModuleCard from '../components/ModuleCard.astro';
import { getCollection } from 'astro:content';

const devlogEntries = (await getCollection('marcus-ai', ({ data }) => !data.draft))
  .sort((a, b) => b.data.date.valueOf() - a.data.date.valueOf())
  .slice(0, 1);

const modules = (await getCollection('aprendizaje', ({ data }) => !data.draft))
  .sort((a, b) => b.data.date.valueOf() - a.data.date.valueOf())
  .slice(0, 3);
---
<BaseLayout title="Home">
  <Hero />

  <section class="mt-12">
    <h2 class="font-mono text-accent text-sm mb-4">// último update · MarcusAI</h2>
    <div class="space-y-4">
      {devlogEntries.map(entry => (
        <DevlogEntry
          title={entry.data.title}
          description={entry.data.description}
          date={entry.data.date}
        />
      ))}
    </div>
    <a
      href="/proyectos/marcus-ai"
      class="font-mono text-xs text-muted hover:text-accent transition-colors mt-4 inline-block"
    >
      ver devlog completo →
    </a>
  </section>

  <section class="mt-12">
    <h2 class="font-mono text-accent text-sm mb-4">// aprendizaje reciente</h2>
    <div class="grid gap-3">
      {modules.map(mod => (
        <ModuleCard
          title={mod.data.title}
          description={mod.data.description}
          date={mod.data.date}
          slug={mod.slug}
        />
      ))}
    </div>
    <a
      href="/aprendizaje"
      class="font-mono text-xs text-muted hover:text-accent transition-colors mt-4 inline-block"
    >
      ver todo →
    </a>
  </section>
</BaseLayout>
```

- [ ] **Step 6: Verificar que el proyecto compila**

```bash
npm run build
```

Resultado esperado: `Build complete.` sin errores.

- [ ] **Step 7: Verificar visualmente**

```bash
npm run dev
```

Abrir `http://localhost:4321`. Verificar: hero con nombre y botones, sección de MarcusAI con la entrada seed, sección de aprendizaje con el módulo seed.

- [ ] **Step 8: Commit**

```bash
git add src/components/ src/pages/index.astro src/content/
git commit -m "feat: Home page con Hero, preview de MarcusAI y módulos de aprendizaje"
```

---

### Task 5: Página Sobre mí

**Files:**
- Create: `src/pages/sobre-mi.astro`

- [ ] **Step 1: Crear la página**

Crear `src/pages/sobre-mi.astro`:

```astro
---
import BaseLayout from '../layouts/BaseLayout.astro';

const skills = [
  'Java 7/8/11', 'Spring Boot', 'Quarkus', 'Kafka', 'JUnit',
  'Oracle SQL', 'MongoDB', 'Git', 'API Rest', 'SOAP',
  'DDD', 'Arquitectura Hexagonal', 'Docker', 'Microservicios',
];

const tools = [
  'IntelliJ IDEA', 'Eclipse', 'GitHub', 'GitLab', 'Docker',
  'SonarQube', 'MongoDBCompass', 'Jira', 'Postman', 'Oracle SQL Developer',
];
---
<BaseLayout title="Sobre mí" description="Experiencia, habilidades y educación de Jonathan Montenegro">
  <h1 class="text-3xl font-semibold mb-1">Jonathan Montenegro</h1>
  <p class="text-muted mb-10">Backend Developer · Chivilcoy, Argentina</p>

  <section class="mb-12">
    <h2 class="font-mono text-accent text-sm mb-6">// experiencia</h2>

    <div class="mb-8">
      <div class="flex items-start justify-between gap-4 flex-wrap">
        <div>
          <h3 class="font-medium">Back End Developer</h3>
          <p class="text-muted text-sm">Interbanking</p>
        </div>
        <span class="font-mono text-xs text-muted whitespace-nowrap">Sep 2021 — Actualidad</span>
      </div>
      <ul class="mt-3 space-y-1 text-sm text-muted">
        <li>— Migración completa de microservicios Quarkus 2 → 3</li>
        <li>— Integración con Oracle Content Management para gestión de documentos legales</li>
        <li>— Diseño con DDD, arquitectura hexagonal, patrón Outbox, Kafka, MongoDB y Oracle SQL</li>
        <li>— Desacoplamiento de procesos en sistemas legacy Struts hacia microservicios Quarkus</li>
        <li>— 100% de cobertura de tests unitarios en todos los desarrollos</li>
        <li>— Documentación de servicios con Swagger y Backstage</li>
        <li>— Monitoreo continuo con SonarQube y Trivy</li>
      </ul>
    </div>

    <div>
      <div class="flex items-start justify-between gap-4 flex-wrap">
        <div>
          <h3 class="font-medium">Desarrollador Autónomo</h3>
          <p class="text-muted text-sm">Independiente</p>
        </div>
        <span class="font-mono text-xs text-muted whitespace-nowrap">Mar 2020 — Ago 2021</span>
      </div>
      <ul class="mt-3 space-y-1 text-sm text-muted">
        <li>— Sistema de gestión de recetas para Óptica Chivilcoy (Java, Spring Boot, SQL)</li>
        <li>— Sistema de ventas, stock y pedidos para Panadería La Unión</li>
      </ul>
    </div>
  </section>

  <section class="mb-12">
    <h2 class="font-mono text-accent text-sm mb-4">// habilidades técnicas</h2>
    <div class="flex flex-wrap gap-2">
      {skills.map(skill => (
        <span class="font-mono text-xs border border-surface px-2 py-1 text-muted hover:border-muted transition-colors">
          {skill}
        </span>
      ))}
    </div>
  </section>

  <section class="mb-12">
    <h2 class="font-mono text-accent text-sm mb-4">// herramientas</h2>
    <div class="flex flex-wrap gap-2">
      {tools.map(tool => (
        <span class="font-mono text-xs border border-surface px-2 py-1 text-muted hover:border-muted transition-colors">
          {tool}
        </span>
      ))}
    </div>
  </section>

  <section>
    <h2 class="font-mono text-accent text-sm mb-4">// educación</h2>
    <div class="space-y-4 text-sm">
      <div>
        <p class="font-medium">Técnico en Programación de Computadores</p>
        <p class="text-muted">Universidad Nacional de Lomas de Zamora</p>
      </div>
      <div>
        <p class="font-medium">Java Inicial · Java Avanzado · Metodologías Ágiles · Desarrollo Web · Angular · Testing QA Manual · JavaScript</p>
        <p class="text-muted">CoderHouse</p>
      </div>
      <div>
        <p class="font-medium">Docker · Arquitectura Software Moderna · Apache Kafka desde cero · Microservices: Clean Architecture, DDD, SAGA, Outbox & Kafka</p>
        <p class="text-muted">Udemy</p>
      </div>
    </div>
  </section>
</BaseLayout>
```

- [ ] **Step 2: Verificar que el proyecto compila**

```bash
npm run build
```

Resultado esperado: `Build complete.` sin errores.

- [ ] **Step 3: Verificar visualmente**

```bash
npm run dev
```

Abrir `http://localhost:4321/sobre-mi`. Verificar: experiencia laboral, skills como tags, educación.

- [ ] **Step 4: Commit**

```bash
git add src/pages/sobre-mi.astro
git commit -m "feat: página Sobre mí con CV completo"
```

---

### Task 6: Página MarcusAI

**Files:**
- Create: `src/pages/proyectos/marcus-ai.astro`

- [ ] **Step 1: Crear la página**

Crear `src/pages/proyectos/marcus-ai.astro`:

```astro
---
import BaseLayout from '../../layouts/BaseLayout.astro';
import { getCollection } from 'astro:content';

const entries = (await getCollection('marcus-ai', ({ data }) => !data.draft))
  .sort((a, b) => b.data.date.valueOf() - a.data.date.valueOf());
---
<BaseLayout title="MarcusAI" description="Seguimiento del progreso de MarcusAI — asistente de IA personal">
  <header class="mb-10 pb-10 border-b border-surface">
    <h1 class="text-3xl font-semibold mb-2">MarcusAI</h1>
    <p class="text-muted mb-4">
      Asistente de IA personal con memoria de contexto. En construcción.
    </p>
    <div class="flex flex-wrap gap-2">
      {['Python', 'LLM', 'En progreso'].map(tag => (
        <span class="font-mono text-xs border border-surface px-2 py-1 text-muted">{tag}</span>
      ))}
    </div>
  </header>

  <section>
    <h2 class="font-mono text-accent text-sm mb-6">// devlog</h2>
    {entries.length === 0 && (
      <p class="text-muted text-sm">Sin entradas todavía.</p>
    )}
    <div class="space-y-6">
      {entries.map(entry => (
        <article class="border-l-2 border-surface pl-4 hover:border-accent transition-colors">
          <time class="font-mono text-xs text-muted">
            {entry.data.date.toLocaleDateString('es-AR', {
              year: 'numeric',
              month: 'long',
              day: 'numeric',
            })}
          </time>
          <h3 class="font-medium mt-1">{entry.data.title}</h3>
          <p class="text-muted text-sm mt-1">{entry.data.description}</p>
        </article>
      ))}
    </div>
  </section>
</BaseLayout>
```

- [ ] **Step 2: Verificar que el proyecto compila**

```bash
npm run build
```

Resultado esperado: `Build complete.` sin errores.

- [ ] **Step 3: Verificar visualmente**

```bash
npm run dev
```

Abrir `http://localhost:4321/proyectos/marcus-ai`. Verificar: header del proyecto, devlog con la entrada seed en timeline.

- [ ] **Step 4: Commit**

```bash
git add src/pages/proyectos/
git commit -m "feat: página MarcusAI con devlog timeline"
```

---

### Task 7: Páginas de Aprendizaje

**Files:**
- Create: `src/layouts/ModuleLayout.astro`
- Create: `src/pages/aprendizaje/index.astro`
- Create: `src/pages/aprendizaje/[slug].astro`

- [ ] **Step 1: Crear ModuleLayout**

Crear `src/layouts/ModuleLayout.astro`:

```astro
---
import BaseLayout from './BaseLayout.astro';

interface Props {
  title: string;
  description: string;
  date: Date;
}

const { title, description, date } = Astro.props;

const formattedDate = date.toLocaleDateString('es-AR', {
  year: 'numeric',
  month: 'long',
  day: 'numeric',
});
---
<BaseLayout title={title} description={description}>
  <article>
    <header class="mb-8 pb-8 border-b border-surface">
      <a href="/aprendizaje" class="font-mono text-xs text-muted hover:text-accent transition-colors">
        ← aprendizaje
      </a>
      <time class="font-mono text-xs text-muted block mt-4">{formattedDate}</time>
      <h1 class="text-3xl font-semibold mt-2">{title}</h1>
      <p class="text-muted mt-2">{description}</p>
    </header>
    <div class="prose prose-invert prose-sm max-w-none prose-headings:font-semibold prose-headings:text-text prose-a:text-accent prose-code:text-accent prose-code:bg-surface prose-code:px-1 prose-code:rounded prose-pre:bg-surface">
      <slot />
    </div>
  </article>
</BaseLayout>
```

- [ ] **Step 2: Crear página index de Aprendizaje**

Crear `src/pages/aprendizaje/index.astro`:

```astro
---
import BaseLayout from '../../layouts/BaseLayout.astro';
import ModuleCard from '../../components/ModuleCard.astro';
import { getCollection } from 'astro:content';

const modules = (await getCollection('aprendizaje', ({ data }) => !data.draft))
  .sort((a, b) => b.data.date.valueOf() - a.data.date.valueOf());
---
<BaseLayout title="Aprendizaje" description="Módulos de inteligencia artificial que Jonathan Montenegro va documentando">
  <h1 class="text-3xl font-semibold mb-2">Aprendizaje</h1>
  <p class="text-muted mb-8">Módulos de IA que voy documentando mientras aprendo.</p>

  {modules.length === 0 && (
    <p class="text-muted text-sm">Sin módulos todavía.</p>
  )}
  <div class="grid gap-3">
    {modules.map(mod => (
      <ModuleCard
        title={mod.data.title}
        description={mod.data.description}
        date={mod.data.date}
        slug={mod.slug}
      />
    ))}
  </div>
</BaseLayout>
```

- [ ] **Step 3: Crear página dinámica de módulo individual**

Crear `src/pages/aprendizaje/[slug].astro`:

```astro
---
import ModuleLayout from '../../layouts/ModuleLayout.astro';
import { getCollection } from 'astro:content';

export async function getStaticPaths() {
  const modules = await getCollection('aprendizaje', ({ data }) => !data.draft);
  return modules.map(mod => ({
    params: { slug: mod.slug },
    props: { mod },
  }));
}

const { mod } = Astro.props;
const { Content } = await mod.render();
---
<ModuleLayout
  title={mod.data.title}
  description={mod.data.description}
  date={mod.data.date}
>
  <Content />
</ModuleLayout>
```

- [ ] **Step 4: Verificar que el proyecto compila**

```bash
npm run build
```

Resultado esperado: `Build complete.` sin errores. Debe generar `/aprendizaje/` y `/aprendizaje/como-armar-prompts/`.

- [ ] **Step 5: Verificar visualmente**

```bash
npm run dev
```

Abrir `http://localhost:4321/aprendizaje`. Verificar: grid con el módulo seed.
Abrir `http://localhost:4321/aprendizaje/como-armar-prompts`. Verificar: contenido del módulo renderizado con estilos de prosa.

- [ ] **Step 6: Commit**

```bash
git add src/layouts/ModuleLayout.astro src/pages/aprendizaje/
git commit -m "feat: páginas de aprendizaje con grid y módulo individual"
```

---

### Task 8: Página de Contacto

**Files:**
- Create: `src/pages/contacto.astro`

- [ ] **Step 1: Crear la página**

Crear `src/pages/contacto.astro`:

```astro
---
import BaseLayout from '../layouts/BaseLayout.astro';

const links = [
  {
    label: 'LinkedIn',
    href: 'https://www.linkedin.com/in/jonathan-alexis-montenegro/',
    display: 'jonathan-alexis-montenegro',
  },
  {
    label: 'GitHub',
    href: 'https://github.com/[tu-usuario-github]',
    display: '[tu-usuario-github]',
  },
  {
    label: 'Email',
    href: 'mailto:jonathanalexismontenegro97@gmail.com',
    display: 'jonathanalexismontenegro97@gmail.com',
  },
];
---
<BaseLayout title="Contacto" description="Formas de contactar a Jonathan Montenegro">
  <h1 class="text-3xl font-semibold mb-2">Contacto</h1>
  <p class="text-muted mb-8">Podés encontrarme en:</p>

  <div class="space-y-3">
    {links.map(link => (
      <a
        href={link.href}
        target={link.href.startsWith('mailto') ? undefined : '_blank'}
        rel={link.href.startsWith('mailto') ? undefined : 'noopener'}
        class="flex items-center justify-between border border-surface p-4 hover:border-accent transition-colors group"
      >
        <span class="font-mono text-accent text-sm">{link.label}</span>
        <span class="text-muted text-sm group-hover:text-text transition-colors">{link.display}</span>
      </a>
    ))}
  </div>
</BaseLayout>
```

> **Nota:** Reemplazar `[tu-usuario-github]` con tu usuario real de GitHub en los dos lugares donde aparece.

- [ ] **Step 2: Verificar que el proyecto compila**

```bash
npm run build
```

Resultado esperado: `Build complete.` sin errores.

- [ ] **Step 3: Verificar visualmente**

```bash
npm run dev
```

Abrir `http://localhost:4321/contacto`. Verificar: tres links con hover effect.

- [ ] **Step 4: Commit**

```bash
git add src/pages/contacto.astro
git commit -m "feat: página de contacto con links sociales"
```

---

### Task 9: CONTENT_GUIDE.md

**Files:**
- Create: `CONTENT_GUIDE.md`

- [ ] **Step 1: Crear la guía**

Crear `CONTENT_GUIDE.md` en la raíz del proyecto:

````markdown
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
````

- [ ] **Step 2: Verificar que el proyecto compila**

```bash
npm run build
```

Resultado esperado: `Build complete.` sin errores.

- [ ] **Step 3: Commit**

```bash
git add CONTENT_GUIDE.md
git commit -m "docs: agregar CONTENT_GUIDE.md para creación de contenido"
```

---

### Task 10: Deploy en Vercel

- [ ] **Step 1: Crear repositorio en GitHub**

Crear un repositorio nuevo en github.com (público o privado, sin README).

- [ ] **Step 2: Pushear el código**

```bash
git remote add origin https://github.com/[tu-usuario-github]/jmontenegro-site.git
git branch -M main
git push -u origin main
```

- [ ] **Step 3: Conectar con Vercel**

1. Ir a [vercel.com](https://vercel.com) e iniciar sesión con GitHub
2. Click en **Add New → Project**
3. Seleccionar el repositorio `jmontenegro-site`
4. Framework Preset: **Astro** (se detecta automáticamente)
5. Click **Deploy**

- [ ] **Step 4: Verificar el deploy**

Vercel asigna una URL tipo `jmontenegro-site.vercel.app`. Verificar que todas las páginas cargan correctamente:

- `/` → Home con Hero, MarcusAI preview, módulos
- `/sobre-mi` → CV
- `/proyectos/marcus-ai` → Devlog
- `/aprendizaje` → Grid de módulos
- `/aprendizaje/como-armar-prompts` → Módulo individual
- `/contacto` → Links

- [ ] **Step 5: (Opcional) Configurar dominio personalizado**

En Vercel → Settings → Domains, agregar tu dominio si tenés uno.
````
