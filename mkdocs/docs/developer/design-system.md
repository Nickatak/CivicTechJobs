# Design System

The CivicTechJobs Design System (CTJ-DS) is the shared visual + interaction language for the frontend. The source of truth lives in the [Figma file](https://www.figma.com/file/G5bOqhud6azbxyR9El9Ygp/Civic-Tech-Jobs); the implementation lives in `frontend/components/`.

## Stack

| Layer | Tool |
|-------|------|
| Framework | Next.js 15 (App Router) |
| Library | React 19 |
| Types | TypeScript 5 |
| Styling | Tailwind CSS 4 |
| Theme | CSS-first config via Tailwind 4's `@theme` directive |

The component system is **utility-first Tailwind + TypeScript-typed React components**. There is no SCSS, no CSS-modules, no separate component-library tool — Tailwind classes are the styling layer.

## Source of truth

Figma is canonical for visual decisions. Any divergence between code and Figma is a code bug unless explicitly documented. When implementing a Figma design:

- Use the design tokens (colors, typography, spacing) Figma exposes — not eyeballed values.
- Implement at the two anchor viewports: **1440px (desktop)** and **375px (mobile)**.
- Behavior between those breakpoints is the developer's call, guided by Tailwind's responsive utilities.

## Theme tokens

Theme values (colors, typography, spacing scale, breakpoints) are declared in CSS via Tailwind 4's `@theme` directive. The design system's tokens are co-located with the global CSS:

```css
/* frontend/app/globals.css */
@import "tailwindcss";

@theme {
  --color-primary: ...;
  --color-primary-dark: ...;
  --font-family-sans: ...;
  --font-size-display: ...;
  --breakpoint-tablet: 768px;
  --breakpoint-desktop: 1024px;
}
```

These map 1:1 to Figma's design tokens. Adding a new token means adding it to Figma first, then mirroring the value in `globals.css`.

## Component library structure

Components live at `frontend/components/`, organized by purpose:

```
frontend/
├── app/                       # App Router pages, layouts, route handlers
│   └── globals.css            # Tailwind import + @theme
├── components/
│   ├── ui/                    # Atoms (Button, Checkbox, TextField, Typography)
│   ├── nav/                   # Header / footer / auth nav
│   ├── cards/                 # Card variants (Standard, Circle)
│   ├── feedback/              # Dialog, Cookie banner, etc.
│   └── index.ts               # Barrel export
└── lib/                       # Utility functions
```

Each component is a single `.tsx` file with a typed prop interface. No PropTypes (TypeScript types replace them).

## Server vs client components

Next.js App Router defaults all components to **server components**. Use client components (`"use client"` directive at the top of the file) only when one of the following is needed:

- Browser-only state (`useState`, `useReducer`)
- Effects (`useEffect`)
- Event handlers tied to user interaction (`onClick`, `onSubmit` on forms not handled by server actions)
- Browser-only APIs (`window`, `localStorage`)

For example, `Typography` and `StandardCard` are server components; `Dialog` and `CookieBanner` are client components.

## Responsive design

Use Tailwind's responsive utilities (`sm:`, `md:`, `lg:`, etc.) directly on elements. Avoid writing custom media queries — the breakpoints in `@theme` are the source of truth.

For the rare component that needs both **scalable** behavior (smoothly grows with the viewport) and **responsive** behavior (snaps at a breakpoint), combine percentage / fractional units with breakpoint utilities:

```tsx
<div className="w-full md:w-1/2 lg:w-1/3 px-4 md:px-6 lg:px-8">
  ...
</div>
```

The earlier 12-column SCSS system is replaced by Tailwind's `grid-cols-*` and `flex` utilities.

## SVG assets

Two patterns:

- **As React components** — for SVGs that need props (e.g., theme-driven fill colors). Configure SVGR or Next.js's built-in SVG support and import as a React component:
    ```tsx
    import Logo from "@/assets/logo.svg";
    <Logo className="text-primary w-32 h-8" />
    ```
- **As `<Image>` source** — for static SVGs displayed at known sizes. Use `next/image`:
    ```tsx
    import logoSrc from "@/assets/logo.svg";
    <Image src={logoSrc} alt="CivicTechJobs" width={128} height={32} />
    ```

Pick based on whether the SVG needs to react to props.

## Accessibility

- WCAG 2.0 Level AA is the target compliance level (per [misc/ada-guide.md](../misc/ada-guide.md) — superseded by this guide if conflicts arise).
- All interactive elements must be keyboard-navigable.
- All images need `alt` text; decorative SVGs use `aria-hidden="true"`.
- The `eslint-plugin-jsx-a11y` rules catch most static issues (see [developer/eslint-guide.md](eslint-guide.md)).
- Use `@axe-core/react` in development to catch runtime issues — it logs accessibility warnings to the browser console.

## Resources

- [Figma file](https://www.figma.com/file/G5bOqhud6azbxyR9El9Ygp/Civic-Tech-Jobs)
- [Tailwind CSS 4 documentation](https://tailwindcss.com/docs)
- [Next.js App Router](https://nextjs.org/docs/app)
- [React 19](https://react.dev/)
- [WAI-ARIA Authoring Practices](https://www.w3.org/WAI/ARIA/apg/)
