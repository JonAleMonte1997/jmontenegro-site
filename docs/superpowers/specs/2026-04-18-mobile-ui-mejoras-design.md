# Mobile UI Mejoras — Design Spec

**Fecha:** 2026-04-18  
**Stack:** Astro 4 + Tailwind CSS 3  
**Problema:** En mobile la página permite scroll horizontal y no se ve bien — el nav desborda, el Hero no es responsive, y el espaciado no está optimizado para pantallas chicas.

---

## Sección 1: Fix de overflow horizontal

**Problema raíz:** Ningún elemento tiene restricción en el eje X. Cuando el nav o el Hero desbordan el viewport, el browser habilita scroll horizontal.

**Fix:**
- Agregar `overflow-x: hidden` en los selectores `html` y `body` en `src/styles/global.css`.

---

## Sección 2: Header / Hamburger Menu

**Comportamiento mobile (`< md`):**
- Ocultar los links del nav (`hidden md:flex`).
- Mostrar un botón hamburguesa (ícono SVG de 3 líneas) alineado a la derecha.
- Al hacer click: aparece un dropdown debajo del header con los 4 links apilados verticalmente.
- Animación: opacity 0→1 + translateY(-8px)→0, transición de 200ms.
- Al hacer click en un link o fuera del menú: se cierra.
- El ícono alterna entre hamburguesa (cerrado) y X (abierto).

**Comportamiento desktop (`md:`):**
- Comportamiento actual sin cambios (nav horizontal con gap-6).

**Implementación:**
- Archivo: `src/components/Header.astro`
- Estado open/closed: atributo `data-open` en el `<header>`.
- JavaScript vanilla en un `<script>` inline, compatible con ViewTransitions de Astro (listener en `astro:page-load`).
- Sin dependencias externas.

**Estilos del dropdown:**
- Background: `bg-bg/95 backdrop-blur-sm`
- Border bottom: `border-b border-surface`
- Links: `text-muted hover:text-text`, padding vertical `py-3`, separados con `border-b border-surface/50`

---

## Sección 3: Hero responsive

**Problema:** Layout `flex-row` con foto `w-28 h-28` y `text-4xl` desborda en pantallas de 320-375px.

**Cambios en `src/components/Hero.astro`:**

| Propiedad | Mobile | Desktop (`md:`) |
|---|---|---|
| Flex direction | `flex-col` | `flex-row` |
| Alineación foto | `mx-auto` (centrada) | alineación normal |
| Tamaño h1 | `text-2xl` | `text-4xl` |
| Padding vertical | `py-10` | `py-16` |
| Gap | `gap-4` | `gap-6` |

- Los botones GitHub/LinkedIn se mantienen en fila (`flex-row`), ya caben en mobile.
- El texto descriptivo (`text-muted text-lg`) se ajusta a `text-base` en mobile.

---

## Sección 4: Revisión general de páginas

Revisar y corregir puntualmente en:
- `src/pages/sobre-mi.astro`
- `src/pages/proyectos/marcus-ai.astro`
- `src/pages/aprendizaje/index.astro`
- `src/pages/contacto.astro`
- `src/components/DevlogEntry.astro`
- `src/components/ModuleCard.astro`

**Criterios de revisión:**
- Elementos con width fijo mayor al viewport
- Grids que no colapsen en mobile
- Tipografía demasiado grande en mobile
- Padding/margin excesivo en mobile

Los ajustes serán puntuales — solo donde haya problemas confirmados.

---

## Archivos a modificar

1. `src/styles/global.css` — overflow-x fix
2. `src/components/Header.astro` — hamburger menu
3. `src/components/Hero.astro` — layout responsive
4. Páginas varias — ajustes puntuales según revisión

## Fuera de scope

- Rediseño visual del desktop
- Cambio de paleta de colores o tokens
- Nuevas páginas o secciones
