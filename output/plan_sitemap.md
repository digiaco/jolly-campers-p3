# Jolly Campers - Site Map

## Overview

This document outlines the complete URL structure and page hierarchy for the Jolly Campers platform, covering both public-facing pages and authenticated user areas.

**URL Pattern:** `https://jollycampers.com/[locale]/[path]`
**Supported Locales:** `en`, `nl`, `de`, `es`, `fr`

---

## Site Map

**Legend:**
| Icon | Source | Description |
|------|--------|-------------|
| 🗄️ | PostgreSQL | App database (campers, bookings, users, transactions) |
| 📝 | Sanity CMS | Headless CMS (editorial content, marketing pages) |
| 🔀 | Hybrid | Combines both sources |

---

### 🏠 Public Pages

#### Core Booking Flow

| URL | Page | Source | Notes |
|-----|------|--------|-------|
| `/` | Homepage | 🔀 | CMS: hero, offers / DB: trending, top campers |
| `/search` | Search & Listings | 🗄️ | List view, Map view, Filters panel |
| `/camper/[slug]` | Camper Detail | 🗄️ | Individual camper page |

**Search Page Features:**
- **List View** — Grid/list of camper cards with sorting
- **Map View** — Interactive map with camper pins (Mapbox)
- **Filters Panel** — Price, dates, travelers, features, camper type, etc.
- **View Toggle** — Switch between list/map views
- **URL Params** — All filters persist in URL for sharing/SEO

#### Destinations & Inspiration

| URL | Page | Source | Notes |
|-----|------|--------|-------|
| `/destinations` | Destinations Hub | 📝 | Browse by country/region |
| `/destinations/[country]` | Country Page | 🔀 | CMS: guide / DB: camper counts |
| `/destinations/[country]/[region]` | Region Page | 🔀 | CMS: guide / DB: camper counts |
| `/destinations/seasons` | Seasonal Inspiration Hub | 📝 | Browse by season |
| `/destinations/seasons/[season]` | Season Page | 🔀 | CMS: seasonal guide / DB: available campers (e.g., `/destinations/seasons/summer`) |
| `/routes` | Trip Routes Hub | 📝 | Curated road trip ideas |
| `/routes/[slug]` | Route Detail | 📝 | Individual route guide |

**Seasonal Inspiration Pages:**
- **Seasons Hub** (`/destinations/seasons`) — Browse destinations by season
- **Season Pages** (`/destinations/seasons/summer`, `/winter`, `/spring`, `/autumn`) — Seasonal destination guides with available campers

#### Blog

| URL | Page | Source | Notes |
|-----|------|--------|-------|
| `/blog` | Blog Hub | 📝 | All blog posts |
| `/blog/[category]` | Category Page | 📝 | Posts by category |
| `/blog/[slug]` | Blog Post | 📝 | Individual article |

#### Offers & Promotions

| URL | Page | Source | Notes |
|-----|------|--------|-------|
| `/offers` | Offers Hub | 🔀 | All active promotions (CMS: content / DB: applicable campers) |
| `/offers/[slug]` | Promotion Detail | 🔀 | Individual promotion landing page (e.g., `/offers/summer-2025`) |

**Offers Page Features:**
- **Active Promotions** — Current discounts, seasonal deals, flash sales
- **Campaign Landing Pages** — SEO-optimized promotion pages
- **Filtered Search** — Show campers eligible for specific promotions
- **Promotion Badges** — Display on camper cards in search results

#### Help Center

| URL | Page | Source | Notes |
|-----|------|--------|-------|
| `/help` | Help Center Home | 📝 | FAQ overview |
| `/help/travelers` | Traveler Help | 📝 | Help for renters |
| `/help/travelers/booking` | Booking Help | 📝 | |
| `/help/travelers/payments` | Payments Help | 📝 | |
| `/help/travelers/cancellations` | Cancellations Help | 📝 | |
| `/help/travelers/during-trip` | During Trip Help | 📝 | |
| `/help/owners` | Owner Help | 📝 | Help for fleet owners |
| `/help/owners/getting-started` | Getting Started | 📝 | |
| `/help/owners/manage-bookings` | Manage Bookings | 📝 | |
| `/help/owners/pricing` | Pricing Help | 📝 | |
| `/help/owners/payouts` | Payouts Help | 📝 | |

#### About & Legal

| URL | Page | Source | Notes |
|-----|------|--------|-------|
| `/about` | About Us | 📝 | Company info |
| `/about/how-it-works` | How It Works | 📝 | Platform explainer (for travelers) |
| `/about/partners` | Partner With Us | 📝 | B2B partnerships |
| `/owners` | Become a Fleet Owner | 📝 | Intro to platform for owners (CMS: content, signup link) |
| `/legal/terms` | Terms of Service | 📝 | |
| `/legal/privacy` | Privacy Policy | 📝 | |
| `/legal/cookies` | Cookie Policy | 📝 | |
| `/legal/insurance` | Insurance Policy | 📝 | |
| `/legal/deposits` | Deposit & Damage Policy | 📝 | |
| `/legal/accessibility` | Accessibility Statement | 📝 | |

**Fleet Owner Intro Page (`/owners`) Features:**
- **Platform Overview** — How Jolly Campers works for owners
- **Benefits** — Why list with us (reach, tools, support)
- **Earnings Calculator** — Estimate potential income
- **Success Stories** — Testimonials from existing owners
- **Sign Up CTA** — Link to registration
- **FAQ** — Common questions about listing

---

### 🔐 Authentication

| URL | Page | Source |
|-----|------|--------|
| `/login` | Login | 🗄️ |
| `/register` | Register | 🗄️ |
| `/forgot-password` | Forgot Password | 🗄️ |
| `/reset-password` | Reset Password | 🗄️ |

---

### 🛒 Checkout Flow

| URL | Page | Source | Step |
|-----|------|--------|------|
| `/checkout/[bookingId]` | Checkout Start | 🗄️ | 1 |
| `/checkout/[bookingId]/extras` | Add-ons Selection | 🗄️ | 2 |
| `/checkout/[bookingId]/details` | Traveler Details | 🗄️ | 3 |
| `/checkout/[bookingId]/payment` | Payment | 🗄️ | 4 |
| `/checkout/[bookingId]/confirmation` | Confirmation | 🗄️ | 5 |

---

### 👤 Traveler Dashboard (Authenticated)

#### Overview

| URL | Page | Source |
|-----|------|--------|
| `/dashboard` | Dashboard Home | 🗄️ |

#### Trips

| URL | Page | Source | Notes |
|-----|------|--------|-------|
| `/dashboard/trips` | My Trips | 🗄️ | All trips list |
| `/dashboard/trips/upcoming` | Upcoming Trips | 🗄️ | |
| `/dashboard/trips/past` | Past Trips | 🗄️ | |
| `/dashboard/trips/[bookingId]` | Trip Detail | 🗄️ | |
| `/dashboard/trips/[bookingId]/checkin` | Digital Check-in | 🗄️ | Pickup form |
| `/dashboard/trips/[bookingId]/checkout` | Digital Check-out | 🗄️ | Return form |

#### Communication

| URL | Page | Source |
|-----|------|--------|
| `/dashboard/messages` | Messages | 🗄️ |
| `/dashboard/messages/[conversationId]` | Conversation | 🗄️ |
| `/dashboard/support` | AI Support Chat | 🗄️ |

#### Account

| URL | Page | Source |
|-----|------|--------|
| `/dashboard/payments` | Payment History | 🗄️ |
| `/dashboard/reviews` | My Reviews | 🗄️ |
| `/dashboard/reviews/write/[bookingId]` | Write Review | 🗄️ |
| `/dashboard/favorites` | Saved Campers | 🗄️ |
| `/dashboard/profile` | Profile Settings | 🗄️ |
| `/dashboard/profile/edit` | Edit Profile | 🗄️ |
| `/dashboard/profile/verification` | ID Verification | 🗄️ |
| `/dashboard/notifications` | Notification Settings | 🗄️ |

---

### 🚐 Fleet Owner Dashboard (Authenticated)

#### Overview

| URL | Page | Source |
|-----|------|--------|
| `/owner` | Owner Dashboard Home | 🗄️ |

#### Fleet Management

| URL | Page | Source | Notes |
|-----|------|--------|-------|
| `/owner/fleet` | Fleet Overview | 🗄️ | All vehicles |
| `/owner/fleet/add` | Add New Vehicle | 🗄️ | |
| `/owner/fleet/[vehicleId]` | Vehicle Detail | 🗄️ | |
| `/owner/fleet/[vehicleId]/edit` | Edit Vehicle | 🗄️ | |
| `/owner/fleet/[vehicleId]/photos` | Manage Photos | 🗄️ | |
| `/owner/fleet/[vehicleId]/pricing` | Pricing Settings | 🗄️ | |
| `/owner/fleet/[vehicleId]/availability` | Availability Calendar | 🗄️ | |
| `/owner/fleet/[vehicleId]/addons` | Add-ons Management | 🗄️ | |

#### Bookings

| URL | Page | Source | Notes |
|-----|------|--------|-------|
| `/owner/bookings` | Booking Management | 🗄️ | All bookings |
| `/owner/bookings/pending` | Pending Requests | 🗄️ | |
| `/owner/bookings/upcoming` | Upcoming Bookings | 🗄️ | |
| `/owner/bookings/active` | Active Rentals | 🗄️ | |
| `/owner/bookings/past` | Past Bookings | 🗄️ | |
| `/owner/bookings/[bookingId]` | Booking Detail | 🗄️ | |
| `/owner/bookings/[bookingId]/pickup` | Digital Pickup Form | 🗄️ | |
| `/owner/bookings/[bookingId]/return` | Digital Return Form | 🗄️ | |

#### Calendar & Pricing

| URL | Page | Source |
|-----|------|--------|
| `/owner/calendar` | Calendar Hub | 🗄️ |
| `/owner/calendar/sync` | Platform Sync Settings | 🗄️ |
| `/owner/pricing` | Smart Pricing Dashboard | 🗄️ |
| `/owner/pricing/rules` | Pricing Rules | 🗄️ |

#### Communication

| URL | Page | Source |
|-----|------|--------|
| `/owner/messages` | Messages | 🗄️ |
| `/owner/messages/[conversationId]` | Conversation | 🗄️ |

#### Financials

| URL | Page | Source |
|-----|------|--------|
| `/owner/earnings` | Earnings & Payouts | 🗄️ |
| `/owner/earnings/history` | Payout History | 🗄️ |
| `/owner/earnings/settings` | Payout Settings | 🗄️ |

#### Reviews & Customers

| URL | Page | Source |
|-----|------|--------|
| `/owner/reviews` | Reviews Overview | 🗄️ |
| `/owner/reviews/received` | Reviews Received | 🗄️ |
| `/owner/reviews/write/[bookingId]` | Review Traveler | 🗄️ |
| `/owner/customers` | Customer Management | 🗄️ |
| `/owner/customers/[customerId]` | Customer Detail | 🗄️ |

#### Operations

| URL | Page | Source |
|-----|------|--------|
| `/owner/maintenance` | Maintenance Overview | 🗄️ |
| `/owner/maintenance/[vehicleId]` | Vehicle Maintenance | 🗄️ |
| `/owner/analytics` | Performance Analytics | 🗄️ |

#### Account

| URL | Page | Source |
|-----|------|--------|
| `/owner/profile` | Owner Profile | 🗄️ |
| `/owner/profile/edit` | Edit Profile | 🗄️ |
| `/owner/profile/verification` | Business Verification | 🗄️ |
| `/owner/settings` | Account Settings | 🗄️ |
| `/owner/settings/notifications` | Notification Settings | 🗄️ |
| `/owner/settings/locations` | Pickup Locations | 🗄️ |
| `/owner/settings/insurance` | Insurance Documents | 🗄️ |

---

### 👑 Super Admin (Internal)

| URL | Page | Source | Notes |
|-----|------|--------|-------|
| `/admin` | Admin Dashboard | 🗄️ | |
| `/admin/users` | User Management | 🗄️ | |
| `/admin/owners` | Fleet Owner Management | 🗄️ | |
| `/admin/vehicles` | Vehicle Management | 🗄️ | |
| `/admin/bookings` | All Bookings | 🗄️ | |
| `/admin/transactions` | Financial Transactions | 🗄️ | |
| `/admin/disputes` | Dispute Management | 🗄️ | |
| `/admin/reviews` | Review Moderation | 🗄️ | |
| `/admin/analytics` | Platform Analytics | 🗄️ | |
| `/admin/content` | Content Management | 📝 | Links to Sanity Studio |
| `/admin/settings` | Platform Settings | 🗄️ | |

---

## Page Inventory

### Public Pages (No Auth Required)

#### 🗄️ PostgreSQL Pages (App Data)

| Page | URL | Rendering | Priority |
|------|-----|-----------|----------|
| Search Results | `/search` | SSR | High |
| Camper Detail | `/camper/[slug]` | ISR (5m) | High |

#### 📝 Sanity CMS Pages (Editorial Content)

| Page | URL | Rendering | Priority |
|------|-----|-----------|----------|
| Destinations Hub | `/destinations` | SSG | Medium |
| Trip Routes | `/routes` | SSG | Low |
| Route Detail | `/routes/[slug]` | SSG | Low |
| Blog Hub | `/blog` | ISR (1h) | Medium |
| Blog Category | `/blog/[category]` | ISR (1h) | Low |
| Blog Post | `/blog/[slug]` | ISR (1d) | Medium |
| Help Center | `/help` | SSG | Medium |
| Help - Travelers | `/help/travelers/*` | SSG | Medium |
| Help - Owners | `/help/owners/*` | SSG | Medium |
| About | `/about` | SSG | Low |
| How It Works | `/about/how-it-works` | SSG | Medium |
| Partners | `/about/partners` | SSG | Low |
| Terms | `/legal/terms` | SSG | Low |
| Privacy | `/legal/privacy` | SSG | Low |
| Cookies | `/legal/cookies` | SSG | Low |
| Insurance | `/legal/insurance` | SSG | Low |
| Deposits | `/legal/deposits` | SSG | Low |
| Accessibility | `/legal/accessibility` | SSG | Low |

#### 🔀 Hybrid Pages (Both Sources)

| Page | URL | CMS Content | DB Content | Rendering |
|------|-----|-------------|------------|-----------|
| Homepage | `/` | Hero, Offers, Trust section | Trending destinations, Top campers | ISR (1h) |
| Country Page | `/destinations/[country]` | Description, Tips, Images | Camper counts, Listings preview | ISR (1d) |
| Region Page | `/destinations/[country]/[region]` | Description, Attractions | Camper counts, Listings preview | ISR (1d) |

### Authentication Pages

| Page | URL | Rendering |
|------|-----|-----------|
| Login | `/login` | CSR |
| Register | `/register` | CSR |
| Forgot Password | `/forgot-password` | CSR |
| Reset Password | `/reset-password` | CSR |

### Checkout Flow (Auth Required)

| Page | URL | Rendering |
|------|-----|-----------|
| Checkout Start | `/checkout/[bookingId]` | CSR |
| Add-ons | `/checkout/[bookingId]/extras` | CSR |
| Traveler Details | `/checkout/[bookingId]/details` | CSR |
| Payment | `/checkout/[bookingId]/payment` | CSR |
| Confirmation | `/checkout/[bookingId]/confirmation` | CSR |

### Traveler Dashboard (Auth Required)

| Page | URL | Description |
|------|-----|-------------|
| Dashboard Home | `/dashboard` | Overview, quick actions |
| My Trips | `/dashboard/trips` | All trips list |
| Upcoming Trips | `/dashboard/trips/upcoming` | Future bookings |
| Past Trips | `/dashboard/trips/past` | Completed rentals |
| Trip Detail | `/dashboard/trips/[bookingId]` | Single booking details |
| Digital Check-in | `/dashboard/trips/[bookingId]/checkin` | Pickup form |
| Digital Check-out | `/dashboard/trips/[bookingId]/checkout` | Return form |
| Messages | `/dashboard/messages` | All conversations |
| Conversation | `/dashboard/messages/[conversationId]` | Chat thread |
| Payments | `/dashboard/payments` | Payment history |
| My Reviews | `/dashboard/reviews` | Reviews written |
| Write Review | `/dashboard/reviews/write/[bookingId]` | Review form |
| Favorites | `/dashboard/favorites` | Saved campers |
| Profile | `/dashboard/profile` | Profile settings |
| Edit Profile | `/dashboard/profile/edit` | Edit personal info |
| Verification | `/dashboard/profile/verification` | ID upload |
| Notifications | `/dashboard/notifications` | Notification prefs |
| Support | `/dashboard/support` | AI support chat |

### Fleet Owner Dashboard (Auth Required)

| Page | URL | Description |
|------|-----|-------------|
| Owner Home | `/owner` | KPI overview |
| Fleet | `/owner/fleet` | All vehicles |
| Vehicle Detail | `/owner/fleet/[vehicleId]` | Single vehicle |
| Edit Vehicle | `/owner/fleet/[vehicleId]/edit` | Edit listing |
| Vehicle Photos | `/owner/fleet/[vehicleId]/photos` | Photo management |
| Vehicle Pricing | `/owner/fleet/[vehicleId]/pricing` | Pricing settings |
| Vehicle Availability | `/owner/fleet/[vehicleId]/availability` | Calendar |
| Vehicle Add-ons | `/owner/fleet/[vehicleId]/addons` | Extras management |
| Add Vehicle | `/owner/fleet/add` | Create listing |
| Bookings | `/owner/bookings` | All bookings |
| Pending Bookings | `/owner/bookings/pending` | Awaiting approval |
| Upcoming Bookings | `/owner/bookings/upcoming` | Future rentals |
| Active Rentals | `/owner/bookings/active` | Currently rented |
| Past Bookings | `/owner/bookings/past` | Completed |
| Booking Detail | `/owner/bookings/[bookingId]` | Single booking |
| Pickup Form | `/owner/bookings/[bookingId]/pickup` | Digital pickup |
| Return Form | `/owner/bookings/[bookingId]/return` | Digital return |
| Calendar Hub | `/owner/calendar` | Master calendar |
| Sync Settings | `/owner/calendar/sync` | Platform connections |
| Smart Pricing | `/owner/pricing` | AI pricing dashboard |
| Pricing Rules | `/owner/pricing/rules` | Manual rules |
| Messages | `/owner/messages` | All conversations |
| Conversation | `/owner/messages/[conversationId]` | Chat thread |
| Earnings | `/owner/earnings` | Revenue overview |
| Payout History | `/owner/earnings/history` | Past payouts |
| Payout Settings | `/owner/earnings/settings` | Bank account |
| Reviews | `/owner/reviews` | All reviews |
| Reviews Received | `/owner/reviews/received` | From travelers |
| Review Traveler | `/owner/reviews/write/[bookingId]` | Review form |
| Maintenance | `/owner/maintenance` | Service overview |
| Vehicle Maintenance | `/owner/maintenance/[vehicleId]` | Per vehicle |
| Customers | `/owner/customers` | Customer list |
| Customer Detail | `/owner/customers/[customerId]` | Customer info |
| Analytics | `/owner/analytics` | Performance stats |
| Owner Profile | `/owner/profile` | Public profile |
| Edit Profile | `/owner/profile/edit` | Edit info |
| Verification | `/owner/profile/verification` | Business docs |
| Settings | `/owner/settings` | Account settings |
| Notification Settings | `/owner/settings/notifications` | Alert prefs |
| Locations | `/owner/settings/locations` | Pickup points |
| Insurance | `/owner/settings/insurance` | Insurance docs |

---

## URL Parameters Reference

### Search Page Parameters

```
/search?
  location=amsterdam          # City or region
  checkin=2025-01-15         # Check-in date (YYYY-MM-DD)
  checkout=2025-01-22        # Check-out date (YYYY-MM-DD)
  travelers=2                # Number of travelers
  type=campervan             # Vehicle type
  minPrice=50                # Min price per day
  maxPrice=200               # Max price per day
  features=shower,toilet     # Comma-separated features
  transmission=automatic     # automatic | manual
  petFriendly=true          # Boolean
  unlimitedKm=true          # Boolean
  sort=recommended          # recommended | price_asc | price_desc | rating
  page=1                    # Pagination
```

### Camper Detail Parameters

```
/camper/[slug]?
  checkin=2025-01-15        # Pre-fill dates
  checkout=2025-01-22
  travelers=2
```

---

## Redirect Rules

| From | To | Type |
|------|----|------|
| `/` (no locale) | `/en/` | 302 (detect locale) |
| `/campers` | `/search` | 301 |
| `/vehicles` | `/search` | 301 |
| `/rentals` | `/search` | 301 |
| `/faq` | `/help` | 301 |
| `/contact` | `/help` | 301 |
| `/terms-and-conditions` | `/legal/terms` | 301 |
| `/privacy-policy` | `/legal/privacy` | 301 |
| `/owner/dashboard` | `/owner` | 301 |
| `/user/dashboard` | `/dashboard` | 301 |

---

## SEO Considerations

### Priority Pages for SEO

| Priority | Pages | Focus |
|----------|-------|-------|
| **P1** | Homepage, Search, Camper Detail | Core conversion pages |
| **P2** | Destinations, Blog | Organic traffic drivers |
| **P3** | Help Center, About | Trust & support |
| **P4** | Legal pages | Compliance |

### Sitemap Generation

```typescript
// Dynamically generated pages for sitemap.xml
const dynamicPages = [
  // From PostgreSQL
  ...campers.map(c => `/camper/${c.slug}`),

  // From Sanity CMS
  ...destinations.map(d => `/destinations/${d.country}/${d.region}`),
  ...blogPosts.map(p => `/blog/${p.slug}`),
  ...routes.map(r => `/routes/${r.slug}`),
]
```

### robots.txt

```
User-agent: *
Allow: /

Disallow: /dashboard/
Disallow: /owner/
Disallow: /admin/
Disallow: /checkout/
Disallow: /api/

Sitemap: https://jollycampers.com/sitemap.xml
```

---

## Page Count Summary

### By Data Source

| Data Source | Page Types | Dynamic Pages |
|-------------|------------|---------------|
| 🗄️ **PostgreSQL** | Camper listings, Search, Dashboards, Checkout | ~500+ campers |
| 📝 **Sanity CMS** | Blog, Destinations, Help, About, Legal | ~150+ articles |
| 🔀 **Hybrid** | Homepage, Country/Region pages | ~50 |

### By Category

| Category | Page Count | Data Source |
|----------|------------|-------------|
| Public - Core (Search, Camper) | ~500+ | 🗄️ PostgreSQL |
| Public - Editorial (Blog, Routes) | ~100+ | 📝 Sanity CMS |
| Public - Destinations | ~50+ | 🔀 Hybrid |
| Public - Static (Help, Legal, About) | ~20 | 📝 Sanity CMS |
| Auth Pages | 4 | 🗄️ PostgreSQL |
| Checkout Flow | 5 | 🗄️ PostgreSQL |
| Traveler Dashboard | ~15 | 🗄️ PostgreSQL |
| Fleet Owner Dashboard | ~35 | 🗄️ PostgreSQL |
| Super Admin | ~12 | 🗄️ PostgreSQL |
| **Total Unique Routes** | **~750+** | |

### Content Ownership Summary

| Who Manages | Content Type | Where |
|-------------|--------------|-------|
| **Marketing Team** | Blog posts, Destinations, Promotions, About | Sanity Studio |
| **Support Team** | Help Center / FAQ | Sanity Studio |
| **Legal Team** | Terms, Privacy, Policies | Sanity Studio |
| **Fleet Owners** | Camper listings, Pricing, Availability | Owner Dashboard |
| **Travelers** | Reviews, Bookings, Messages | Traveler Dashboard |
| **System** | Transactions, Analytics, Calculations | Automatic |

