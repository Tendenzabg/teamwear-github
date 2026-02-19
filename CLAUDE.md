# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is **Horizon** (v3.3.1), a Shopify theme. It uses no Node.js build step — all development is managed via the Shopify CLI and Shopify's native theme infrastructure.

## Development Commands

```bash
# Start local development server (connects to a Shopify store)
shopify theme dev --store=<store-name>

# Push theme to Shopify
shopify theme push

# Pull latest theme from Shopify
shopify theme pull

# Check theme for issues
shopify theme check
```

There is no test suite, linter config, or npm build process. JavaScript is validated through jsconfig.json with TypeScript-style checking.

## Architecture

### Directory Structure

- **`assets/`** — JavaScript (99 files), CSS (38 files), SVGs. All client-side logic lives here.
- **`sections/`** — Full-page section components (48 files); rendered server-side via Liquid.
- **`blocks/`** — Reusable block components (94 files, prefixed with `_`) used inside sections.
- **`snippets/`** — Liquid partials (127 files) for shared markup and helpers.
- **`templates/`** — Page-level JSON/Liquid templates linking sections together.
- **`layout/`** — Base layouts (`theme.liquid` wraps nearly all pages).
- **`config/`** — Theme settings schema (`settings_schema.json`, 2,287 lines).
- **`locales/`** — Translation files for 25+ languages.

### JavaScript Architecture

JavaScript uses **Web Components** (custom elements) as the primary pattern. All interactive UI elements extend a base `Component` class (defined in `assets/component.js`), which itself extends `DeclarativeShadowElement`. Key patterns:

- `ref` attributes on DOM elements for direct references within components
- Declarative event listener system defined in component configuration
- Automatic mutation observers for DOM updates
- `ThemeEvents` (in `assets/events.js`) as the custom event bus

Key modules to understand before editing JS:
- `assets/component.js` — Base class all components extend
- `assets/events.js` — `ThemeEvents` enum and event system
- `assets/utilities.js` — `requestIdleCallback`, `yieldToMainThread`, view transition helpers
- `assets/global.d.ts` — TypeScript global type declarations (`Shopify`, `Theme` globals)

The `jsconfig.json` in `assets/` enables strict JS type checking (`noImplicitAny`, `strictNullChecks`, `noUncheckedIndexedAccess`) with ES2020 as target. Path alias `@theme/*` maps to `assets/`.

### Liquid Architecture

- Server-side rendering is primary; JavaScript adds interactivity progressively.
- Snippets use `{%- doc -%}` blocks for inline documentation.
- Sections and blocks expose settings to the Shopify theme editor via schema blocks.
- The `Theme` and `Shopify` global JS objects (typed in `global.d.ts`) bridge Liquid-rendered data to client-side JS.

### Styling

- Component styles are scoped using Liquid's `{% stylesheet %}` tag inside section/block files.
- CSS custom properties are used for theming (color schemes, spacing, typography).
- Color scheme variants are defined in `config/settings_schema.json`.

### Third-party Integrations

- **EcomPoser** — Page builder; related snippets are prefixed `ecom_*`, layout is `ecom.liquid`.
- **Foxify** — Product customization; assets prefixed `foxify-*`, layout is `theme.foxify.liquid`.
