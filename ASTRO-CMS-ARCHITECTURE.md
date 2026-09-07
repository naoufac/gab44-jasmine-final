# Gab44 Astro + Visual CMS Architecture

**Status:** Draft for Nao's reference and confirmation. No implementation is authorized by this document.

## The simple model

Astro is the rendering engine. It turns structured content and reusable components into fast HTML pages.

Astro is not itself a Webflow-like editing interface. A visual headless CMS supplies the human editor. For Gab44, the closest fit to the requested Webflow-style preview is **Storyblok**, connected to Astro through its official integration and Visual Editor.

```text
Nao edits in Storyblok Visual Editor
              |
              v
Structured content: pages, articles, navigation, global CTA
              |
              v
Astro renders shared layouts and components
              |
              v
Cloudflare serves fast HTML
```

## The Webflow example: change one CTA across 1,000 pages

The wrong system copies this markup into every page:

```html
<a href="/reading">Get your $9 reading</a>
```

The proposed system has one global content record:

```text
Global CTA
- eyebrow
- title
- supporting text
- button label
- destination
- enabled
```

Every page renders the same `GlobalCTA.astro` component using that record. Change the label, price, destination, or visibility once in the CMS, publish, and every page receives the change.

For static pages, publishing triggers an Astro rebuild and Cloudflare deployment. For selected frequently changing content, Astro can fetch live CMS content at request time, but that adds runtime cost and another failure point. Gab44 should remain static by default.

## How 1,000 pages work

There should not be 1,000 hand-built page layouts.

There should be:

- One typed content collection containing 1,000 entries
- A small number of page templates
- One dynamic Astro route such as `src/pages/library/[...slug].astro`
- `getStaticPaths()` generating the final routes from the collection

Example:

```text
Content entry: numerology/life-path-1
Template: LibraryArticle.astro
Output: /library/numerology/life-path-1/
```

The entry contains the content and metadata. The template controls design, accessibility, navigation, author information, related content, and the shared CTA.

Official Astro documentation confirms that content collections can use schemas and loaders for Markdown, MDX, YAML, TOML, JSON, APIs, and CMS data. `getStaticPaths()` can generate thousands of routes from one template.

## Proposed content model

### Global settings singleton

Edited once and used everywhere:

- Brand name and description
- Primary navigation
- Footer navigation
- Global personal-reading CTA
- Announcement bar
- Social links
- Author identity
- Default SEO metadata
- Legal and privacy links

### Navigation singleton

- Ordered top-level items
- Nested menu groups
- Primary CTA
- Mobile labels
- Visibility controls
- Internal route validation

Navigation is content, but its rendered structure remains controlled by `Header.astro`. Editors can change labels/order/destinations without breaking the mobile drawer layout.

### Page

- Title
- Description
- Slug
- Hero treatment
- Ordered page sections
- Optional CTA override
- SEO fields

### Library article

- Title
- Slug
- Topic family
- Visitor intent
- Summary
- Body
- Sources/methodology
- Related entries
- Author
- Published and updated dates
- Featured flag

### Personal reading offer

- Price
- Promise
- Deliverables
- Delivery time
- Sample
- Guarantee
- Privacy copy
- Form fields
- Payment destination

## Proposed navigation from scratch

This is structural, not visual. Nao's forthcoming reference controls appearance.

### Desktop

- Gab44 logo/home
- Start Here
- Astrology
- Numerology
- Library
- About
- Primary button: Get your $9 reading

### Mobile

- Gab44 logo
- Hamburger button
- Drawer containing the same five destinations
- Persistent or repeated reading CTA, only if it does not obstruct content

### Homepage decision paths

The homepage should guide by human intention rather than list every database category:

1. Know myself
2. Love and relationships
3. What is happening now
4. Understand my numbers
5. Get a personal reading

The full spiritual encyclopedia remains available inside Library with categories, search, and filters.

## Visual editing options

### Storyblok: recommended for the stated goal

- Visual editor with the site in an iframe
- Click a page block to edit its fields
- Live preview support through the official Astro SDK
- Reusable structured blocks
- Global referenced stories for navigation and CTA
- Editors cannot accidentally rewrite the design system if available blocks are controlled

Tradeoff: content lives in a hosted CMS and the project depends on its service/pricing.

### TinaCMS: strongest Git-backed alternative

- Content stored with the repository
- Production editing can commit directly to GitHub
- `/admin` collection editing
- Can use Tina Cloud or a self-hosted data layer

Tradeoff: excellent ownership and Git history, but less naturally Webflow-like than Storyblok's visual block editor for this use case.

### Plain Astro content collections

- Maximum simplicity and ownership
- Markdown/MDX/JSON in Git
- Typed schemas and excellent performance

Tradeoff: no friendly visual editing unless a CMS is added.

## What Astro can do

- Render static HTML with little or no client JavaScript
- Generate 1,000 or more routes from structured content
- Enforce required content fields with schemas
- Share layouts, navigation, CTA, footer, SEO, and author components globally
- Optimize local images
- Generate sitemaps, RSS, robots, and structured data
- Add interactive islands only where needed, such as calculators, search, filters, theme toggle, and mobile navigation
- Run server endpoints for forms and payment webhooks when paired with an adapter or edge function
- Mix static pages with selected on-demand routes

## What Astro does not do by itself

- Provide a Webflow-like visual editor
- Store customer orders
- Process payments
- Authenticate editors
- Send finished readings
- Decide the product hierarchy

Those belong to the CMS, payment provider, database/order service, and product architecture.

## Gab44 migration reality

The preserved repository currently contains the exact 7.2 MB compiled Worker bundle, not maintainable authoring source. The page HTML and content are embedded in generated JavaScript functions.

The migration is therefore not "install Astro around the current file." It is:

1. Preserve the Worker snapshot permanently.
2. Extract every article and its metadata.
3. Normalize entries into typed collections.
4. Define global records for navigation, CTA, author, footer, and SEO.
5. Build a small set of Astro templates.
6. Connect Storyblok visual editing.
7. Repair the reading order/payment/delivery service.
8. Generate redirects so existing working URLs continue to resolve.
9. Verify every route and the complete customer journey.
10. Deploy to a separate preview before changing any production domain.

## Publishing lifecycle

```text
Edit content in visual CMS
        |
Publish
        |
Webhook triggers build
        |
Astro validates schemas and renders pages
        |
Automated checks reject broken links, missing fields, or dead CTAs
        |
Cloudflare deployment
        |
Live site updates everywhere
```

A global CTA or navigation edit should not require touching code. A new component type or design-system change still requires code review and deployment.

## Guardrail against visual-CMS chaos

Do not reproduce Webflow's unlimited canvas. Editors should compose pages from approved blocks:

- Hero
- Intent gateway
- Featured reading
- Article body
- Related content
- Method/source note
- Testimonial
- FAQ
- Global CTA

This gives human freedom over content, order, visibility, and approved variants while protecting typography, spacing, accessibility, responsiveness, and brand coherence.

## Decisions waiting for Nao

1. Visual reference for the redesign
2. Confirm or correct `VISION.md`
3. Choose Storyblok visual editing or TinaCMS Git-backed editing
4. Confirm whether the $9 human-written reading remains the primary paid product
5. Confirm whether Library keeps all current spiritual traditions or only selected Gab44 pillars

## Sources

- Astro content collections: https://docs.astro.build/en/guides/content-collections/
- Astro CMS overview: https://docs.astro.build/en/guides/cms/
- Astro and TinaCMS: https://docs.astro.build/en/guides/cms/tina-cms/
- Astro and Storyblok: https://docs.astro.build/en/guides/cms/storyblok/
- Storyblok visual preview for Astro: https://www.storyblok.com/docs/guides/astro/visual-preview
