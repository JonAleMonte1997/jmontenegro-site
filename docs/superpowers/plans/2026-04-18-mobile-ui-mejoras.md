# Mobile UI Mejoras — Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Eliminar el scroll horizontal en mobile y mejorar la experiencia visual en pantallas pequeñas con hamburger menu, Hero responsive, y ajustes puntuales en páginas secundarias.

**Architecture:** Fix global de overflow-x en CSS, hamburger menu con JavaScript vanilla en Header, layout responsive con clases Tailwind en Hero y páginas secundarias. Sin dependencias nuevas.

**Tech Stack:** Astro 4, Tailwind CSS 3, JavaScript vanilla, ViewTransitions de Astro.

---

## Archivos a modificar

| Archivo | Cambio |
|---|---|
| `src/styles/global.css` | Agregar `overflow-x: hidden` a `html` y `body` |
| `src/components/Header.astro` | Hamburger menu con dropdown para mobile |
| `src/components/Hero.astro` | Layout flex-col en mobile, tipografía responsive |
| `src/pages/sobre-mi.astro` | Header de perfil responsive (flex-col en mobile) |
| `src/pages/contacto.astro` | Truncar email largo con `truncate` |

---

## Task 1: Fix overflow-x horizontal global

**Files:**
- Modify: `src/styles/global.css`

- [ ] **Step 1: Agregar overflow-x: hidden**

En `src/styles/global.css`, reemplazar el bloque `html { scroll-behavior: smooth; }` con:

```css
html {
  scroll-behavior: smooth;
  overflow-x: hidden;
}

body {
  overflow-x: hidden;
}
```

- [ ] **Step 2: Verificar en dev server**

```bash
npm run dev
```

Abrir `http://localhost:4321` en DevTools con viewport de 375px. Confirmar que no aparece scrollbar horizontal en ninguna página.

- [ ] **Step 3: Commit**

```bash
git add src/styles/global.css
git commit -m "fix: eliminar scroll horizontal con overflow-x hidden"
```

---

## Task 2: Hamburger menu en Header

**Files:**
- Modify: `src/components/Header.astro`

- [ ] **Step 1: Reemplazar el contenido completo de Header.astro**

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
<header class="border-b border-surface sticky top-0 z-10 bg-bg/90 backdrop-blur-sm" id="site-header">
  <nav class="max-w-3xl mx-auto px-4 py-4 flex items-center justify-between">
    <a href="/" class="font-mono text-accent font-medium tracking-tight hover:opacity-80 transition-opacity cursor-pointer glow-accent">
      jmontenegro
    </a>

    <!-- Nav desktop -->
    <ul class="hidden md:flex gap-6 text-sm">
      {navLinks.map(link => (
        <li>
          <a
            href={link.href}
            class={`transition-colors duration-200 cursor-pointer ${currentPath.startsWith(link.href) ? 'text-text' : 'text-muted hover:text-text'}`}
          >
            {link.label}
          </a>
        </li>
      ))}
    </ul>

    <!-- Botón hamburguesa (mobile) -->
    <button
      id="menu-toggle"
      class="md:hidden flex flex-col gap-1.5 p-2 cursor-pointer"
      aria-label="Abrir menú"
      aria-expanded="false"
    >
      <span class="hamburger-line block w-5 h-px bg-text transition-transform duration-200 origin-center"></span>
      <span class="hamburger-line block w-5 h-px bg-text transition-opacity duration-200"></span>
      <span class="hamburger-line block w-5 h-px bg-text transition-transform duration-200 origin-center"></span>
    </button>
  </nav>

  <!-- Dropdown mobile -->
  <div
    id="mobile-menu"
    class="md:hidden hidden border-t border-surface bg-bg/95 backdrop-blur-sm"
  >
    <ul class="max-w-3xl mx-auto px-4 py-2">
      {navLinks.map(link => (
        <li class="border-b border-surface/50 last:border-0">
          <a
            href={link.href}
            class={`block py-3 text-sm transition-colors duration-200 ${currentPath.startsWith(link.href) ? 'text-text' : 'text-muted hover:text-text'}`}
          >
            {link.label}
          </a>
        </li>
      ))}
    </ul>
  </div>
</header>

<script>
  function initMenu() {
    const toggle = document.getElementById('menu-toggle');
    const menu = document.getElementById('mobile-menu');
    const lines = document.querySelectorAll<HTMLElement>('.hamburger-line');

    if (!toggle || !menu) return;

    function openMenu() {
      menu!.classList.remove('hidden');
      toggle!.setAttribute('aria-expanded', 'true');
      lines[0].style.transform = 'translateY(6.5px) rotate(45deg)';
      lines[1].style.opacity = '0';
      lines[2].style.transform = 'translateY(-6.5px) rotate(-45deg)';
    }

    function closeMenu() {
      menu!.classList.add('hidden');
      toggle!.setAttribute('aria-expanded', 'false');
      lines[0].style.transform = '';
      lines[1].style.opacity = '';
      lines[2].style.transform = '';
    }

    toggle.addEventListener('click', () => {
      const isOpen = toggle.getAttribute('aria-expanded') === 'true';
      isOpen ? closeMenu() : openMenu();
    });

    // Cerrar al hacer click en un link
    menu.querySelectorAll('a').forEach(link => {
      link.addEventListener('click', closeMenu);
    });

    // Cerrar al hacer click fuera
    document.addEventListener('click', (e) => {
      const header = document.getElementById('site-header');
      if (header && !header.contains(e.target as Node)) {
        closeMenu();
      }
    });
  }

  document.addEventListener('astro:page-load', initMenu);
</script>
```

- [ ] **Step 2: Verificar en dev server**

```bash
npm run dev
```

En DevTools con viewport 375px:
- Confirmar que el nav desktop está oculto y el botón hamburguesa visible
- Hacer click en el botón: verificar que el dropdown aparece con los 4 links
- Hacer click en un link: verificar que el menú se cierra
- Hacer click fuera del menú: verificar que se cierra
- Cambiar a 768px+: verificar que el nav desktop aparece y el botón hamburguesa se oculta
- Las 3 líneas del ícono deben animarse a una X al abrir y volver al abrir de nuevo

- [ ] **Step 3: Commit**

```bash
git add src/components/Header.astro
git commit -m "feat: hamburger menu responsive para mobile"
```

---

## Task 3: Hero responsive

**Files:**
- Modify: `src/components/Hero.astro`

- [ ] **Step 1: Reemplazar el contenido del template en Hero.astro**

Reemplazar solo la parte del template (el HTML entre `---` y `<script>`), sin tocar el script:

```astro
<section class="py-10 md:py-16 border-b border-surface relative overflow-hidden" data-animate>
  <!-- Scanlines overlay -->
  <div class="absolute inset-0 scanlines-pattern pointer-events-none" aria-hidden="true"></div>

  <div class="flex flex-col sm:flex-row items-center sm:items-start gap-4 sm:gap-6 relative">
    <!-- Foto de perfil -->
    <div class="shrink-0">
      <div class="w-24 h-24 sm:w-28 sm:h-28 rounded-full border border-accent/30 overflow-hidden ring-2 ring-accent/10">
        <img
          src="/foto.jpg"
          alt="Jonathan Montenegro"
          class="w-full h-full object-cover"
        />
      </div>
    </div>

    <div class="flex-1 text-center sm:text-left">
      <p class="font-mono text-accent text-sm mb-3">
        <span id="typing-prompt"></span><span class="typing-cursor text-accent">█</span>
      </p>
      <h1 class="text-2xl sm:text-3xl md:text-4xl font-display font-semibold mb-2 tracking-tight glow-accent-subtle">
        Jonathan Montenegro
      </h1>
      <p class="text-muted text-base md:text-lg mb-6 sm:mb-8">Backend Developer · Building MarcusAI</p>
      <div class="flex gap-3 text-sm font-mono justify-center sm:justify-start">
        <a
          href="https://github.com/JonAleMonte1997"
          target="_blank"
          rel="noopener"
          class="border border-accent text-accent px-4 py-2 hover:bg-accent hover:text-bg transition-colors duration-200 cursor-pointer glow-accent"
        >
          GitHub
        </a>
        <a
          href="https://www.linkedin.com/in/jonathan-alexis-montenegro/"
          target="_blank"
          rel="noopener"
          class="border border-surface text-muted px-4 py-2 hover:border-muted hover:text-text transition-colors duration-200 cursor-pointer"
        >
          LinkedIn
        </a>
      </div>
    </div>
  </div>
</section>
```

- [ ] **Step 2: Verificar en dev server**

```bash
npm run dev
```

En DevTools con viewport 375px en `http://localhost:4321`:
- La foto debe estar centrada arriba
- El texto y botones deben estar centrados debajo de la foto
- El `h1` debe ser más pequeño (text-2xl)
- No debe haber scroll horizontal
- En 640px+ (sm): foto a la izquierda, texto a la derecha (layout original)
- En 768px+ (md): `h1` crece a `text-4xl`

- [ ] **Step 3: Commit**

```bash
git add src/components/Hero.astro
git commit -m "feat: hero responsive con layout flex-col en mobile"
```

---

## Task 4: sobre-mi.astro — header de perfil responsive

**Files:**
- Modify: `src/pages/sobre-mi.astro`

El header tiene `flex items-center gap-5` con `w-32 h-32` + `text-3xl`. En 375px puede quedar ajustado. Lo hacemos flex-col en mobile.

- [ ] **Step 1: Modificar el div del header de perfil**

Reemplazar:
```astro
  <div class="flex items-center gap-5 mb-10">
    <div class="shrink-0">
      <div class="w-32 h-32 rounded-full border border-accent/30 overflow-hidden ring-2 ring-accent/10">
        <img
          src="/foto.jpg"
          alt="Jonathan Montenegro"
          class="w-full h-full object-cover"
        />
      </div>
    </div>
    <div>
      <h1 class="text-3xl font-semibold mb-1">Jonathan Montenegro</h1>
      <p class="text-muted">Backend Developer · Chivilcoy, Argentina</p>
    </div>
  </div>
```

Con:
```astro
  <div class="flex flex-col sm:flex-row items-center sm:items-center gap-4 sm:gap-5 mb-10 text-center sm:text-left">
    <div class="shrink-0">
      <div class="w-24 h-24 sm:w-32 sm:h-32 rounded-full border border-accent/30 overflow-hidden ring-2 ring-accent/10">
        <img
          src="/foto.jpg"
          alt="Jonathan Montenegro"
          class="w-full h-full object-cover"
        />
      </div>
    </div>
    <div>
      <h1 class="text-2xl sm:text-3xl font-semibold mb-1">Jonathan Montenegro</h1>
      <p class="text-muted">Backend Developer · Chivilcoy, Argentina</p>
    </div>
  </div>
```

- [ ] **Step 2: Verificar en dev server**

En DevTools con 375px en `/sobre-mi`:
- La foto centrada arriba, texto centrado abajo
- En 640px+: vuelve al layout en fila
- Las secciones de experiencia, habilidades y educación deben verse bien (ya usan `flex-wrap` y `text-sm`)

- [ ] **Step 3: Commit**

```bash
git add src/pages/sobre-mi.astro
git commit -m "fix: header de perfil responsive en sobre-mi"
```

---

## Task 5: contacto.astro — email largo

**Files:**
- Modify: `src/pages/contacto.astro`

El email `jonathanalexismontenegro97@gmail.com` es muy largo y puede desbordar el contenedor en mobile con `justify-between`.

- [ ] **Step 1: Agregar truncate al span del display**

Reemplazar:
```astro
        <span class="text-muted text-sm group-hover:text-text transition-colors">{link.display}</span>
```

Con:
```astro
        <span class="text-muted text-sm group-hover:text-text transition-colors truncate max-w-[55%]">{link.display}</span>
```

- [ ] **Step 2: Verificar en dev server**

En DevTools con 375px en `/contacto`:
- El email debe mostrarse truncado con `...` en lugar de desbordar
- Los otros links (LinkedIn, GitHub, Instagram) deben mostrarse completos
- El layout de cada card debe quedar alineado

- [ ] **Step 3: Commit**

```bash
git add src/pages/contacto.astro
git commit -m "fix: truncar email largo en contacto para mobile"
```

---

## Verificación final

- [ ] Abrir cada ruta en DevTools con viewport 375px y 320px:
  - `/` — Hero centrado, sin scroll horizontal
  - `/sobre-mi` — perfil en columna, experiencia legible
  - `/proyectos/marcus-ai` — devlog bien formateado
  - `/aprendizaje` — grid de cards sin overflow
  - `/contacto` — email truncado, cards alineadas
- [ ] Verificar que en 768px+ todo se ve igual que antes (desktop no roto)
- [ ] Verificar que el hamburger menu funciona: abre, cierra con click en link, cierra con click afuera
- [ ] Correr type check:
  ```bash
  npm run astro check
  ```
  Expected: 0 errores
