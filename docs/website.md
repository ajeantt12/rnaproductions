# Feature Audit: Website

## 1) Purpose

This document audits the **RNA Productions website feature** and captures implementation details in an AI-ingestible format.

Primary outcomes:
- Explain current runtime behavior and user flows.
- Define interfaces/contracts used across pages/layout/styles/config.
- Document design patterns and data models.
- Provide safe extension guidance.

---

## 2) Scope

In scope:
- Routing and page rendering (`/`, `/contact`)
- Shared layout and footer
- Hero section behavior and stability
- Navigation interactions and transitions
- Contact actions (email + WhatsApp CTA)
- Deployment and custom-domain config (GitHub Pages)

Out of scope:
- Backend services (none implemented)
- CMS/database integrations (none implemented)
- Analytics integrations (none implemented)

---

## 3) Source Map

Core files:
- `src/layouts/BaseLayout.astro`
- `src/pages/index.astro`
- `src/pages/contact.astro`
- `src/styles/global.css`
- `astro.config.mjs`
- `.github/workflows/deploy.yml`
- `public/CNAME`

Static assets:
- `public/logo-transparent.png`
- `public/favicon.png`

---

## 4) Runtime Architecture

Pattern: **Two static route pages + one shared layout + global CSS**.

Flow:
1. Route (`index.astro` or `contact.astro`) renders a hero section.
2. Each page wraps content with `BaseLayout`.
3. `BaseLayout` injects metadata, favicon, transitions, shell width/padding, and shared footer.
4. `global.css` provides tokenized scale/padding and component-style classes.
5. Build outputs static HTML/CSS/JS via Astro.

---

## 5) Interfaces (Contracts)

### 5.1 Layout Contract

```ts
interface BaseLayoutProps {
  title?: string; // Defaults to "RNA Productions"
}
```

Responsibilities:
- Global `<head>` metadata
- Favicon registration (`/favicon.png`)
- View transitions enablement
- Shared content shell (`.site-shell`)
- Shared footer

### 5.2 Route Page Contract

```ts
interface HeroPageContract {
  usesLayout: "BaseLayout";
  rootClass: "hero-page";
  includes: ["logo", "nav", "content"];
}
```

Required structure for visual consistency:
- Hero container class: `hero-page`
- Navigation class: `hero-nav`
- Content wrapper: `hero-content`

### 5.3 Contact CTA Contract

```ts
interface ContactCTAModel {
  contactEmail: string;          // mailto target
  whatsappNumber: string;        // E.164-like digits, no "+"
  whatsappText: string;          // prefilled message
  whatsappHref: string;          // https://wa.me/<number>?text=<encoded>
}
```

### 5.4 Environment Config Contract

```ts
interface BuildEnv {
  SITE?: string; // canonical site URL, used by astro config
  BASE?: string; // base path, defaults to "/"
}
```

---

## 6) Data Models

### 6.1 Navigation Item Model

```ts
interface NavItem {
  label: "About" | "Contact";
  href: "/" | "/contact";
  active: boolean; // controls class "nav-link-active"
}
```

### 6.2 Hero Content Model

```ts
interface HeroContent {
  heading: string;
  bodyHtml: string;
  ctas?: Array<{ label: string; href: string; newTab?: boolean }>;
}
```

### 6.3 Design Token Model (CSS Variables)

```ts
interface StyleTokens {
  "--page-padding-x": string; // currently 2rem
  "--hero-scale": string;     // currently 1.2
}
```

---

## 7) Design Patterns

1. **Layout Composition**
- Shared concerns are centralized in `BaseLayout`.
- Pages only hold route-specific content/state.

2. **Tokenized Responsive Styling**
- `:root` CSS variables control global scaling and spacing.
- Component class dimensions use `calc(... * var(--hero-scale))`.

3. **State via Classes (No JS state management)**
- Active nav state is encoded in class names and `aria-current`.

4. **Stable Skeleton Pattern**
- `hero-page` and mobile `hero-content` min-heights reduce layout jump between pages.

5. **Progressive Enhancement for Transitions**
- `ViewTransitions` + `transition:animate="fade"` provide navigation animation.

---

## 8) Functional Behavior by Route

### 8.1 About Page (`/`)
- Renders logo with preserved aspect ratio.
- Shows nav with About active.
- Displays service summary text.
- Uses shared footer from layout.

### 8.2 Contact Page (`/contact`)
- Renders same hero shell and active Contact nav.
- Email link opens default mail client via `mailto:business@rnaproductions.in`.
- WhatsApp button opens new tab with prefilled message:
  - `https://wa.me/917048932354?text=<encoded message>`
- Uses shared footer from layout.

---

## 9) Styling and Interaction System

Global classes of interest:
- Layout/sizing: `site-shell`, `hero-page`, `hero-content`
- Branding/media: `hero-logo`
- Navigation: `hero-nav`, `nav-link`, `nav-link-active`
- Typography/body: `hero-title`, `hero-body`, `hero-footer`
- CTA: `hero-cta`

Interaction details:
- Nav links animate underline on hover (`::after` with `scaleX` transition).
- Active nav keeps underline visible.
- WhatsApp CTA has hover color change.
- Page transitions fade between route changes.

---

## 10) Build and Deploy Model

### Local Commands
- `npm run dev` — local development
- `npm run build` — production static build
- `npm run preview` — local production preview

### GitHub Pages Workflow
File: `.github/workflows/deploy.yml`

Pipeline:
1. Trigger on push to `main` or manual dispatch.
2. Setup Node 20 + install dependencies.
3. Build with:
   - `SITE=https://rnaproductions.in`
   - `BASE=/`
4. Upload `dist/`.
5. Deploy via `actions/deploy-pages`.

Custom domain:
- `public/CNAME` contains `rnaproductions.in`
- `public/.nojekyll` prevents Jekyll processing

---

## 11) Accessibility and SEO Notes

Implemented:
- `alt` text on logo image
- `aria-current="page"` on active navigation link
- Semantic headings and paragraph structure
- Viewport meta tag

Potential improvements:
- Add page-level meta description and OG tags.
- Add focus-visible styles for nav/CTA links.

---

## 12) Invariants (Do Not Break)

1. `hero-page` class should remain on both routes to preserve stable layout.
2. Footer should stay in `BaseLayout` (single source of truth).
3. Keep logo dimensions with `height:auto` to preserve PNG aspect ratio.
4. `whatsappNumber` must remain numeric-only for wa.me compatibility.
5. `public/CNAME` must match production custom domain.

---

## 13) Safe Change Guide

Common edits:
- Change global scale: edit `--hero-scale` in `src/styles/global.css`.
- Change side paddings: edit `--page-padding-x`.
- Update footer text once: edit in `src/layouts/BaseLayout.astro`.
- Update WhatsApp number/message: edit constants in `src/pages/contact.astro`.
- Add a new page:
  1. Create `src/pages/<route>.astro`
  2. Wrap with `BaseLayout`
  3. Reuse hero class structure
  4. Add nav item active-state handling

---

## 14) Known Gaps / Risks

- No automated tests currently.
- No lint/format enforcement documented.
- Content strings are inline (no i18n/content management layer).
- Hardcoded contact details require code change for updates.

---

## 15) Quick Reference (Machine-Friendly)

```yaml
feature: website
routes:
  - path: /
    file: src/pages/index.astro
  - path: /contact
    file: src/pages/contact.astro
layout:
  file: src/layouts/BaseLayout.astro
  shared_footer: true
styles:
  file: src/styles/global.css
  tokens:
    page_padding_x: "2rem"
    hero_scale: "1.2"
interactions:
  nav_hover_underline: true
  active_nav_indicator: true
  page_fade_transition: true
contact:
  email: business@rnaproductions.in
  whatsapp: "917048932354"
deploy:
  provider: github_pages
  workflow: .github/workflows/deploy.yml
  custom_domain: rnaproductions.in
```

