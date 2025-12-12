# Headless CMS & Frontend Pages Development Estimate

## Summary

This document outlines the development estimate for implementing Sanity CMS as the headless content management system **and** the Next.js frontend pages (CMS-only + hybrid pages) for the Jolly Campers platform.

**Total Hours: 279 hours**

| Section | Hours |
|---------|-------|
| 1. Sanity Studio Setup | 12 |
| 2. Content Schemas | 32 |
| 3. Next.js Integration (Core) | 15 |
| 4. Multi-Language Support | 12 |
| 5. Preview & Editorial Features | 10 |
| 6. Image & Media Handling | 6 |
| 7. Migration & Initial Content | 6 |
| 8. Frontend CMS & Hybrid Pages | 186 |
| 8. Frontend CMS & Hybrid Pages (Next.js) | 72 |

---

## 1. Sanity Studio Setup

**Total: 12 hours**

| Task | Hours | Description |
|------|-------|-------------|
| Project Initialization | 2 | • Create Sanity project and dataset<br>• Configure sanity.config.ts<br>• Set up environment variables |
| Studio Customization | 4 | • Custom studio branding (logo, colors)<br>• Dashboard widgets (content stats, quick actions)<br>• User-friendly navigation structure |
| Deployment & Hosting | 2 | • Deploy Sanity Studio to Vercel/Sanity hosting<br>• Configure CORS for production domains<br>• Set up staging/production datasets |
| User Roles & Permissions | 4 | • Define roles (Admin, Editor, Marketing, Support)<br>• Configure granular permissions per content type<br>• API token management |

---

## 2. Content Schemas

**Total: 32 hours**

### 2.1 Document Schemas (22 hours)

| Schema | Hours | Description |
|--------|-------|-------------|
| Blog Post | 3 | • Title, slug, excerpt, body (rich text)<br>• Author reference, categories, tags<br>• Featured image, SEO fields<br>• Published date, reading time calculation |
| Blog Category | 1 | • Name, slug, description<br>• Parent category (optional) |
| Destination | 4 | • Name, slug, country, region<br>• Hero image, introduction (rich text)<br>• Highlights array (title, description, image)<br>• Best time to visit, recommended duration<br>• Nearby attractions with coordinates<br>• Related camper types |
| Seasonal Inspiration | 1 | • Season identifier (spring/summer/autumn/winter)<br>• Guide content + tips + hero imagery<br>• SEO fields |
| Route/Itinerary | 3 | • Name, slug, duration<br>• Route stops array (location, description, images)<br>• Map data, distance<br>• Difficulty level, recommended camper types |
| FAQ Item | 2 | • Question, answer (rich text)<br>• Category reference<br>• Related FAQs |
| FAQ Category | 1 | • Name, slug, description<br>• Icon selection |
| Legal Page | 2 | • Title, slug, body (rich text)<br>• Last updated date<br>• Version tracking |
| About Page | 1 | • Configurable sections<br>• Team members array<br>• Company stats<br>• Partner logos |
| Homepage | 1 | • Hero section (title, subtitle, background)<br>• Current offers array<br>• Trust badges array<br>• CTA sections |
| Promotion / Offer | 2 | • Campaign title, slug, content blocks<br>• Valid date range, optional promo code<br>• Eligibility tags (vehicle types, regions) |
| Fleet Owner Intro Page | 1 | • `/owners` landing content (benefits, stories, FAQ, CTAs)<br>• SEO fields |
| Navigation / Footer | 1 | • Header links, footer link groups, social links<br>• Locale-aware labels |

### 2.2 Object Schemas (6 hours)

| Schema | Hours | Description |
|--------|-------|-------------|
| SEO | 1 | • Meta title, description<br>• OG image, keywords<br>• No-index toggle |
| Localized String | 1 | • Multi-language text fields (en, nl, de, es, fr) |
| Localized Block | 2 | • Multi-language rich text with custom marks<br>• Embedded components (CTA, Image gallery) |
| Image with Alt | 1 | • Image asset reference<br>• Alt text (localized)<br>• Caption (optional) |
| CTA Block | 1 | • Title, description<br>• Button text, URL<br>• Style variant |

### 2.3 Validation & Custom Components (4 hours)

| Task | Hours | Description |
|------|-------|-------------|
| Custom Validation Rules | 2 | • Required field validation<br>• Slug uniqueness checks<br>• Image dimension requirements |
| Custom Input Components | 2 | • Rich text editor customization<br>• URL slug preview<br>• Character count for SEO fields |

---

## 3. Next.js Integration

**Total: 15 hours**

| Task | Hours | Description |
|------|-------|-------------|
| Sanity Client Setup | 2 | • Configure next-sanity client<br>• Production vs preview client<br>• Type generation setup |
| GROQ Queries | 6 | • Blog list/detail queries<br>• Destination list/detail queries<br>• FAQ queries by category<br>• Homepage content query<br>• Navigation/footer queries |
| TypeScript Types | 3 | • Auto-generate types from schemas (sanity-codegen)<br>• Query result types<br>• Component prop types |
| Revalidation Strategy | 2 | • Configure ISR per content type<br>• On-demand revalidation webhook<br>• Tag-based cache invalidation |
| Error Handling | 2 | • 404 handling for missing content<br>• Draft content guards<br>• Graceful fallbacks |

<span style="color: gray;">**Note:** This section covers the **integration plumbing** (client, queries, types, preview, revalidation). Actual Next.js page/UI implementation is estimated in **Section 8**.</span>

---

## 4. Multi-Language Support

**Total: 12 hours**

| Task | Hours | Description |
|------|-------|-------------|
| Localized Schema Setup | 3 | • Configure 5 languages (en, nl, de, es, fr)<br>• Default language fallback logic<br>• Language field validation |
| Studio Language Plugin | 2 | • Install @sanity/language-filter<br>• Language switcher UI<br>• Per-field translation status |
| Query Localization | 4 | • Dynamic locale parameter in GROQ queries<br>• Fallback to default language<br>• Missing translation indicators |
| Locale-Aware URLs | 3 | • Generate localized slugs<br>• Hreflang tag generation<br>• Language switcher integration |

---

## 5. Preview & Editorial Features

**Total: 10 hours**

| Task | Hours | Description |
|------|-------|-------------|
| Draft Preview Mode | 4 | • Next.js draft mode integration<br>• Preview secret validation<br>• Exit preview functionality<br>• Visual indicator for draft content |
| Live Preview | 3 | • Real-time content updates in Studio<br>• Side-by-side editing<br>• Mobile/desktop preview toggle |
| Scheduled Publishing | 2 | • Publish date field<br>• Query filter for scheduled content<br>• Cron job for publish trigger |
| Content History | 1 | • Document revision history<br>• Restore previous versions |

---

## 6. Image & Media Handling

**Total: 6 hours**

| Task | Hours | Description |
|------|-------|-------------|
| Sanity Image URL Builder | 2 | • Configure @sanity/image-url<br>• Responsive image helper<br>• Blur placeholder generation |
| Next.js Image Integration | 2 | • Custom Image component for Sanity assets<br>• Automatic srcset generation<br>• Lazy loading configuration |
| Media Library Organization | 2 | • Folder structure for assets<br>• Tagging system<br>• Unused asset cleanup |

---

## 7. Migration & Initial Content

**Total: 6 hours**

| Task | Hours | Description |
|------|-------|-------------|
| Content Migration Plan | 2 | • Identify existing content sources<br>• Data mapping strategy<br>• Migration script structure |
| Seed Content | 2 | • Sample blog posts (2-3 per language)<br>• Sample destinations (5 major regions)<br>• FAQ structure with placeholder content |
| Editor Documentation | 2 | • Content guidelines document<br>• Schema field explanations<br>• Workflow instructions |

---

## 8. Frontend CMS & Hybrid Pages (Next.js)

**Total: 186 hours**

This section covers implementing the **public-facing Next.js pages** that are powered by Sanity CMS (and hybrid pages that combine CMS content with app data), based on `plan_frontend_landing.md` and `plan_sitemap.md`.

### 8.1 Shared Components & Plumbing

| Task | Hours | Description |
|------|-------|-------------|
| Shared CMS UI Plumbing | 10 | • CMS-driven header/footer rendering<br>• PortableText renderer + shared rich text components (CTA blocks, images, embeds, callouts)<br>• Basic page layout components for marketing pages<br>• Breadcrumb component |

### 8.2 Blog Pages (18 hours)

| Task | Hours | Description |
|------|-------|-------------|
| Blog Hub | 6 | • `/blog` list with pagination<br>• Category filter sidebar<br>• Featured posts section<br>• Mobile responsive grid |
| Category Pages | 4 | • `/blog/[category]` filtered list<br>• Category description header<br>• SEO metadata |
| Blog Post Detail | 8 | • `/blog/[slug]` article page<br>• Rich text rendering with embedded media<br>• Author card, publish date, reading time<br>• Related posts section<br>• Social sharing buttons<br>• SEO + JSON-LD structured data |

### 8.3 Destinations Pages (22 hours)

| Task | Hours | Description |
|------|-------|-------------|
| Destinations Hub | 6 | • `/destinations` browse by country<br>• Search/filter functionality<br>• Country cards with images |
| Country Page | 8 | • `/destinations/[country]` hero, intro, region list<br>• Hybrid: CMS guide + API camper counts<br>• Featured campers preview<br>• Map with regions |
| Region Page | 8 | • `/destinations/[country]/[region]` detailed guide<br>• Highlights, attractions, best time to visit<br>• Hybrid: API camper listings preview<br>• Canonical + hreflang handling |

### 8.4 Seasonal Inspiration Pages (8 hours)

| Task | Hours | Description |
|------|-------|-------------|
| Seasons Hub | 3 | • `/destinations/seasons` browse by season<br>• Season cards with imagery |
| Season Detail | 5 | • `/destinations/seasons/[season]` seasonal guide<br>• Hybrid: CMS content + API camper availability<br>• Recommended destinations for season |

### 8.5 Routes / Itineraries Pages (14 hours)

| Task | Hours | Description |
|------|-------|-------------|
| Routes Hub | 4 | • `/routes` list with filters (duration, region)<br>• Route cards with preview |
| Route Detail | 10 | • `/routes/[slug]` full itinerary<br>• Interactive map with route stops<br>• Day-by-day breakdown<br>• Image galleries per stop<br>• Distance, duration, difficulty indicators<br>• Recommended camper types |

### 8.6 Help Center Pages (18 hours)

| Task | Hours | Description |
|------|-------|-------------|
| Help Home | 4 | • `/help` hub with search<br>• Category overview cards<br>• Popular articles |
| Traveler Help Section | 6 | • `/help/travelers` hub<br>• `/help/travelers/booking`, `/payments`, `/cancellations`, `/during-trip`<br>• FAQ accordion components<br>• Article templates |
| Owner Help Section | 6 | • `/help/owners` hub<br>• `/help/owners/getting-started`, `/manage-bookings`, `/pricing`, `/payouts`<br>• FAQ accordion components<br>• Article templates |
| Search & Navigation | 2 | • Help center search functionality<br>• Sidebar navigation<br>• Related articles |

### 8.7 About Pages (12 hours)

| Task | Hours | Description |
|------|-------|-------------|
| About Us | 4 | • `/about` company story, mission<br>• Team section (optional)<br>• Company stats/milestones |
| How It Works | 6 | • `/about/how-it-works` platform explainer<br>• Step-by-step guide with illustrations<br>• Separate sections for travelers/owners |
| Partners | 2 | • `/about/partners` B2B partnerships page |

### 8.8 Fleet Owner Landing (12 hours)

| Task | Hours | Description |
|------|-------|-------------|
| Owners Landing Page | 12 | • `/owners` marketing landing page<br>• Hero with value proposition<br>• Benefits section (why list with us)<br>• Earnings calculator widget<br>• Success stories/testimonials carousel<br>• FAQ section<br>• Multiple CTA sections<br>• Mobile responsive |

### 8.9 Legal Pages (6 hours)

| Task | Hours | Description |
|------|-------|-------------|
| Legal Page Template | 6 | • `/legal/terms`, `/privacy`, `/cookies`, `/insurance`, `/deposits`, `/accessibility`<br>• Rich text rendering<br>• "Last updated" display<br>• Table of contents (auto-generated)<br>• Print-friendly styling |

### 8.10 Offers & Promotions Pages (12 hours)

| Task | Hours | Description |
|------|-------|-------------|
| Offers Hub | 5 | • `/offers` all active promotions<br>• Promotion cards with countdown timers<br>• Category filters |
| Promotion Detail | 7 | • `/offers/[slug]` campaign landing page<br>• Hero with offer details<br>• Terms & conditions<br>• CTA to search with promotion filter<br>• Countdown timer (if time-limited)<br>• Eligible campers preview |

### 8.11 Homepage (42 hours)

| Task | Hours | Description |
|------|-------|-------------|
| Hero Section with Search | 10 | • Full-width hero with background image/video (CMS)<br>• Complex search bar: location autocomplete (Mapbox), date range picker, traveler selector<br>• Search suggestions / recent searches (if logged in)<br>• Mobile responsive (stacked fields on mobile)<br>• Animated transitions |
| Current Offers Carousel | 5 | • Active promotions carousel (CMS)<br>• Offer cards with countdown timers (for time-limited)<br>• Auto-scroll with pause on hover<br>• Swipeable on mobile<br>• CTA buttons with tracking |
| Trending Destinations | 5 | • Hybrid: CMS guides + DB camper counts<br>• Destination cards with hero images<br>• "X campers available" badges<br>• Carousel with arrows + dots |
| Browse by Camper Type | 3 | • Type icons/images grid (campervan, motorhome, converted van, etc.)<br>• Count per type from DB<br>• Links to pre-filtered `/search` |
| Top Rated Campers | 6 | • Hybrid: DB top-rated campers<br>• Full camper card component (image gallery, rating, price, location, key features)<br>• Quick view hover state<br>• Favorites button (if logged in)<br>• Carousel with peek-ahead |
| Trust / Why Us Section | 4 | • "Why Jolly Campers" section (CMS)<br>• Trust badges with icons (Verified owners, Secure payments, 24/7 support)<br>• Platform stats (X bookings, Y reviews, Z% satisfaction)<br>• Testimonials carousel |
| Inspiration / Routes Preview | 4 | • Featured routes/itineraries carousel (CMS)<br>• Route cards with map preview thumbnail<br>• Duration, difficulty indicators<br>• "See all routes" CTA |
| Blog Preview | 3 | • Latest 3-4 blog posts (CMS)<br>• Article cards with image, title, category tag<br>• Read time indicator<br>• "Read more" links |
| Newsletter Signup | 2 | • Email capture form<br>• Integration with email service (Mailchimp/SendGrid)<br>• Success/error states<br>• GDPR consent checkbox |

### 8.12 Header & Footer (12 hours)

| Task | Hours | Description |
|------|-------|-------------|
| Header (Desktop) | 4 | • Logo with link to home<br>• Main navigation (Destinations, How it works, Help, Offers)<br>• Language/locale selector<br>• Login/Register buttons OR User menu (if logged in)<br>• Sticky header on scroll |
| Header (Mobile) | 3 | • Hamburger menu with slide-out navigation<br>• Touch-optimized menu items<br>• User menu / login in mobile nav<br>• Search icon shortcut |
| Footer | 5 | • Multi-column link grid (CMS-driven)<br>• Columns: Explore, Support, Company, Legal<br>• Social media icons<br>• Language/currency selector<br>• Payment method icons<br>• Copyright & legal links<br>• Newsletter signup (compact version)<br>• Mobile responsive (stacked columns) |

<span style="color: gray;">**Note on hybrid pages:** Hybrid pages display **CMS content** plus **dynamic app data** via API calls (e.g., camper counts, preview listings). Backend logic for promotions/eligibility is out of scope here unless separately estimated.</span>

---

## Summary Breakdown

| Section | Hours | % of Total |
|---------|-------|------------|
| 1. Sanity Studio Setup | 12 | 4% |
| 2. Content Schemas | 32 | 11% |
| 3. Next.js Integration (Core) | 15 | 5% |
| 4. Multi-Language Support | 12 | 4% |
| 5. Preview & Editorial | 10 | 4% |
| 6. Image & Media | 6 | 2% |
| 7. Migration & Initial Content | 6 | 2% |
| 8. Frontend CMS & Hybrid Pages | 186 | 67% |
| **Total** | **279** | 100% |

---

## Content Type Summary

| Content Type | Schema Hours | Integration (Core) | Frontend Pages | Total |
|--------------|--------------|--------------------|----------------|-------|
| Blog | 4 | 3 | 18 | 25 |
| Destinations (country/region) | 4 | 3 | 22 | 29 |
| Seasonal Inspiration | 1 | 1 | 8 | 10 |
| Routes/Itineraries | 3 | 2 | 14 | 19 |
| Help Center/FAQ | 3 | 2 | 18 | 23 |
| Legal Pages | 2 | 1 | 6 | 9 |
| About / How it works / Partners | 1 | 0 | 12 | 13 |
| Homepage (CMS + Hybrid sections) | 1 | 1 | 42 | 44 |
| Promotions / Offers | 2 | 1 | 12 | 15 |
| Fleet Owner Intro (`/owners`) | 1 | 0 | 12 | 13 |
| Header & Footer | 1 | 0 | 12 | 13 |
| Shared Components | 1 | 0 | 10 | 11 |
| **Content Subtotal** | 24 | 14 | 186 | **224** |

### Platform & Infrastructure Hours

| Task | Hours | Description |
|------|-------|-------------|
| Sanity Studio Setup | 12 | Project init, customization, deployment, roles |
| Multi-Language Support | 12 | 5 languages, localization, hreflang |
| Preview & Editorial | 10 | Draft mode, live preview, scheduling |
| Image & Media | 6 | Image optimization, media library |
| Migration & Initial Content | 6 | Content migration, seed data, documentation |
| Schema & Integration Overhead | 9 | Validation rules, custom components, error handling |
| **Platform Subtotal** | | | **55** |

| | | | **Grand Total: 279** |

---

## Dependencies

- Sanity account (free tier sufficient for MVP)
- `next-sanity` package
- `@sanity/image-url` package
- `@portabletext/react` for rich text rendering
- `sanity` (Studio) as dev dependency

---

## Notes

1. **Free Tier Limits:** Sanity free tier includes 3 users, 10GB assets, 100K API requests/month. Sufficient for launch, may need upgrade as content grows.

2. **Not Included:**
   - Content writing/translation (marketing responsibility)
   - Deep SEO/content strategy and copywriting beyond technical setup
   - SEO optimization beyond technical setup

3. **Assumptions:**
   - 5 languages (en, nl, de, es, fr)
   - Marketing team will manage content after handoff
   - Standard Sanity Studio UI (no custom plugins beyond basics)

4. **Phase Recommendation:**
   - Phase 1: CMS foundation + Blog + Help Center + Legal + `/owners` + basic homepage sections
   - Phase 2: Destinations + Seasons + Routes + Offers/Promotions + advanced editorial workflows

