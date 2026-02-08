# RNA Productions Website

Official website source for RNA Productions.

## Website Overview

- Main page: company introduction and service summary
- Contact page: email link and WhatsApp CTA with inline brand logo
- Shared layout and footer across all pages
- Mobile-friendly hero layout and transitions between pages

## Brand and Assets

- Logo: `public/logo-transparent.png`
- Favicon: `public/favicon.png`
- WhatsApp inline logo: `public/socials/WA_Digital_Inline_Dark_Green.png`
- Global styling: `src/styles/global.css`

## Contact Links

- Email: `business@rnaproductions.in` (click-to-email)
- WhatsApp CTA: opens prefilled message from the contact page
  - White-fill button with dark green border and inline WhatsApp line logo

## Local Development

```sh
npm install
npm run dev
```

Local preview: `http://localhost:4321`

## Production Build

```sh
npm run build
npm run preview
```

## Deployment

- Deploy target: GitHub Pages (via GitHub Actions)
- Workflow: `.github/workflows/deploy.yml`
- Custom domain file: `public/CNAME`

Current configured domain: `rnaproductions.in`

## Documentation

- Feature documentation lives in `docs/`
- Website audit and implementation details: `docs/website.md`
