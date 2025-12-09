# Dynamic Pricing Development Estimate

## Summary

This document outlines the development estimate for the **Dynamic Pricing System**, which uses a three-step approach:
1. **Base Settings** - Standard price, min/max boundaries
2. **Dynamic Pricing** - Manual (owner rules) OR Auto (AI) - choose one
3. **Discounts** - Duration discounts, repeat customer (always apply)

**Total Hours: 54** (Phase 1 - Additional Dynamic Pricing Features + Scraper Infrastructure)

<span style="color: blue;">**Note:** This estimate only includes features NOT already in `estimate_development.md`. Manual pricing rules (54h) and price calculation (20h) are in the core estimate. This adds: base settings, price history, and scraper infrastructure.</span>

<span style="color: red;">AI/Auto mode (~118 hours) is deferred to Phase 2. Per-source competitor scrapers (8-24h+ each) are estimated separately.</span>

---

## Phase 1: Manual Mode + Data Collection

<span style="color: blue;">**Note:** Manual pricing rules, duration discounts, and price calculation are already included in `estimate_development.md` under **Rules Management (54h)** and **Price Service (20h)**. This estimate only covers **additional** dynamic pricing features not included there.</span>

---

### 1. Base Settings (Step 1)

**Total: 10 hours**

| Task | Description | Hours |
|------|-------------|-------|
| Standard Price Field | • Add standard price field per camper<br>• Default pricing base for all calculations<br>• API endpoints for CRUD operations | 3 |
| Min/Max Price Boundaries | • Add min/max price fields per camper<br>• Validation to ensure min ≤ standard ≤ max<br>• Final price always clamped to these bounds<br>• Database schema updates | 4 |
| Pricing Mode Selection | • Add pricing mode toggle (Manual / Auto)<br>• Store preference per camper<br>• Only Manual mode active in Phase 1<br>• Auto mode placeholder for Phase 2 | 3 |

<span style="color: gray;">UI is included in Dashboard UI section (6. below)</span>

---

### 2. Manual Pricing Rules (Step 2 - Manual Mode)

<span style="color: green;">**Included in estimate_development.md → Rules Management (54 hours)**</span>

Already covered:
- Date & Range Price Adjustments (seasonal pricing)
- Day-of-week rules
- Rule calculation engine
- Rules list and management UI

**Additional items for Dynamic Pricing (if not covered):**

| Task | Description | Hours |
|------|-------------|-------|
| Min/Max Clamping | • Ensure calculated price respects min/max boundaries<br>• Add validation in rule engine | 2 |

**Total: 2 hours** (additional only)

---

### 3. Discounts (Step 3 - Always Apply)

<span style="color: green;">**Duration discounts included in estimate_development.md → Rules Management → Advanced Pricing (Length-Based) (8 hours)**</span>

Already covered:
- Duration-based price adjustments (7+ days, 14+ days, etc.)
- Rule configuration UI

**Additional items NOT in estimate_development.md:**

| Task | Description | Hours |
|------|-------------|-------|
| Discount Stacking Logic | • Apply discounts AFTER rules (Step 2)<br>• Handle multiple discount combination<br>• Final min/max check | 4 |

**Total: 4 hours** (additional only)

---

### 4. Price Calculation Service

<span style="color: green;">**Included in estimate_development.md → Price Service → Quote calculation (20 hours)**</span>

Already covered:
- Quote calculation with rules, seasonal, length-of-booking adjustments
- Line items and price breakdown

**Additional items for Dynamic Pricing:**

| Task | Description | Hours |
|------|-------------|-------|
| Three-Step Integration | • Integrate min/max clamping into quote service<br>• Add repeat customer discount logic<br>• Return three-step breakdown (base → rules → discounts) | 4 |
| Price History Tracking | • Log price changes with source tracking<br>• Support future AI comparison | 4 |

**Total: 8 hours** (additional only)

---

### 5. Data Collection for AI Training (Time-Sensitive)

**Total: 20 hours** (scraper infrastructure only)

<span style="color: red;">**Important:** These data collectors must be built early to gather historical data that cannot be retrieved later. AI training in Phase 2 requires 6+ months of data.</span>

---

#### 5.1 Scraper Base Infrastructure (20 hours)

**Purpose:** Modular infrastructure for competitor price collection.

| Task | Description | Hours |
|------|-------------|-------|
| Database Schema | • `competitor_listings` table (master data)<br>• `competitor_price_samples` table (raw scraped prices with lead time)<br>• `competitor_market_summary` table (aggregated for AI)<br>• Indexes, migrations | 4 |
| Scheduler Service | • Cron-based daily scheduling<br>• Search 4 lead times per region (14, 28, 42, 56 days)<br>• 3-night duration searches<br>• Retry logic, failure handling | 4 |
| Module Interface Design | • Abstract `DataSourceModule` interface<br>• Methods: `fetch()`, `parse()`, `validate()`, `transform()`<br>• Standardized output format<br>• Config-driven module loading<br>• Support for both HTTP and browser-based scrapers | 5 |
| Price Normalization | • Normalize fees across platforms (cleaning, service fees)<br>• Calculate `effective_price_per_night`<br>• Handle different platform fee structures | 3 |
| Market Aggregation | • Daily job to calculate regional median/avg<br>• Store market summaries for AI training | 2 |
| Alerting | • Email/Slack alert on scraper failure<br>• Alert if no data collected for X days<br>• Simple error logging | 2 |

---

<span style="color: orange;">**Per-Source Scraper Notes:**</span>

Each competitor platform requires a separate module, and **hours vary significantly** based on:

| Factor | Simple (8-12h) | Complex (16-24h+) |
|--------|----------------|-------------------|
| **URL Parameters** | Supports date filters in URL | No URL params → need browser automation |
| **Price Display** | Price shown on search results | Must drill into detail page for breakdown |
| **Fee Structure** | All-inclusive pricing | Separate cleaning/service fees (need parsing) |
| **Anti-Bot** | None or basic | CAPTCHA, rate limiting, IP blocking |
| **Page Structure** | Clean HTML/JSON | Heavy JavaScript, dynamic loading |

**Example Complexity:**
```
Platform A (Simple - 10h):
├── URL: /search?location=amsterdam&checkin=2025-01-15&checkout=2025-01-18
├── Price shown on search results page
└── Clean JSON API response

Platform B (Complex - 20h+):
├── URL: /search (no date params)
├── Must use Puppeteer/Playwright to:
│   ├── Open search page
│   ├── Fill date picker form
│   ├── Wait for results to load
│   └── Click each listing for price breakdown
└── May need proxy rotation for anti-bot
```

**Recommended approach:**
1. Start with 1-2 simpler platforms
2. Evaluate anti-bot complexity before committing
3. Consider browser extension for manual-assisted capture as fallback

<span style="color: orange;">**Note:** Each external data source (Goboony, Camptoo, etc.) is a separate module estimated independently. Anti-bot mechanisms vary by platform - some may require browser automation (Puppeteer/Playwright), proxy rotation, or may be blocked entirely. Estimate per source: 8-24 hours depending on complexity.</span>

**Example Module Estimates (not included in total):**
| Platform | Complexity | Estimated Hours | Notes |
|----------|------------|-----------------|-------|
| Goboony | Medium | 12-16 | May need browser automation |
| Camptoo | Medium | 12-16 | Similar to Goboony |
| Yescapa | Medium-High | 16-20 | More complex anti-bot |
| Indie Campers | Low-Medium | 8-12 | Simpler structure |
| Manual Entry Tool | Low | 8 | Browser extension for manual capture |

---

#### 5.2 Weather Data Collection (0 hours - Deferred)

<span style="color: green;">**No implementation needed now.** Open-Meteo provides FREE historical weather data back to 1940. We can fetch all required data when starting AI training in Phase 2.</span>

**Data Points to Collect (in Phase 2):**
```
Essential:
├── Temperature (min, max, avg)
├── Precipitation (amount in mm, probability %)
├── Wind (speed, gusts)
├── Weather code (sunny, cloudy, rain, storm)
└── Sunshine hours

Nice to Have:
├── UV index (beach/outdoor appeal)
├── Cloud cover %
├── Humidity %
├── Daylight hours (sunrise/sunset)
└── "Feels like" temperature

Derived:
└── Camping Score (0-100)
```

**Why we can defer:**
- Open-Meteo Historical API is free and has data back to 1940
- No API key required
- Data is not time-sensitive (unlike competitor pricing)
- Can backfill 2-3 years of data in a few hours when needed
- Implementation: ~16 hours when starting Phase 2

---

#### 5.3 Events Data Collection (0 hours - Deferred)

<span style="color: green;">**No implementation needed now.** PredictHQ has historical event data back to 2011. We can fetch all required data when starting AI training in Phase 2.</span>

**Event Data Points to Collect (in Phase 2):**
```
From PredictHQ:
├── Event ID, title, category
├── Start/end dates
├── Location (lat, lng)
├── Rank (0-100 importance)
├── Local rank (regional importance)
├── PHQ attendance (predicted)
├── Impact patterns (leading, event_day, lagging)
└── Predicted spend (accommodation vertical)

From Free APIs:
├── Public holidays (Nager.Date)
├── School holidays (per country)
└── Major recurring events (hard-coded list)
```

**Why we can defer:**
- PredictHQ has historical data back to 2011 for concerts, sports, festivals
- Public holidays from Nager.Date are also historical (free API)
- Data is not time-sensitive (unlike competitor pricing)
- Can backfill years of data when needed
- Implementation: ~24 hours when starting Phase 2

<span style="color: orange;">**Cost:** PredictHQ is paid (~$200-500/month). 14-day trial = 90 days history only. Need to contact them for full historical access.</span>

---

#### Data Collection Summary

| Component | Hours | Notes |
|-----------|-------|-------|
| Scraper Base Infrastructure | 20 | Core infrastructure + price normalization |
| Per-source scraper | 8-24+ | Each platform varies by complexity |
| Weather Data | 0 | **Deferred** - can backtrack from Open-Meteo (1940+) |
| Events Data | 0 | **Deferred** - can backtrack from PredictHQ (2011+) |
| **Base Total** | **20** | Excludes per-source scrapers |

<span style="color: red;">**Per-Source Scrapers:** Not included. Each competitor platform (Goboony, Camptoo, etc.) requires 8-24 hours depending on anti-bot complexity. Recommend starting with 1-2 sources and expanding.</span>

<span style="color: green;">**Deferred to Phase 2:**</span>
- Weather (~16 hours) - Open-Meteo has free historical data back to 1940
- Events (~24 hours) - PredictHQ has historical data back to 2011

**Only competitor pricing is time-sensitive** - prices change daily and historical data is lost forever.

---

### 6. Dashboard UI (Fleet Owner)

**Total: 10 hours**

| Task | Description | Hours |
|------|-------------|-------|
| Pricing Overview Page | • Show current pricing setup<br>• Display active rules summary<br>• Display active discounts | 4 |
| Price Preview/Calculator | • Enter date range, show calculated price<br>• Show step-by-step breakdown<br>• Useful for owner to test rules | 4 |
| Price History View | • Simple list of price changes<br>• Filter by date range | 2 |

---

## Phase 1 Summary

| Section | Hours | Notes |
|---------|-------|-------|
| 1. Base Settings | 10 | min/max/standard, pricing mode |
| 2. Manual Pricing Rules (additional) | 2 | min/max clamping only |
| 3. Discounts (additional) | 4 | stacking logic only |
| 4. Price Calculation Service (additional) | 8 | three-step integration, history |
| 5. Scraper Base Infrastructure | 20 | competitor pricing collection |
| 6. Dashboard UI | 10 | pricing overview |
| **Phase 1 Total** | **54** | |

<span style="color: blue;">**Already in estimate_development.md (not counted here):**</span>
- Rules Management: 54 hours (seasonal, day-of-week, duration discounts)
- Price Service: 20 hours (quote calculation with rules)

**Additional (Per-Source Scrapers):**
| Platform | Estimated Hours |
|----------|-----------------|
| Goboony | 12-16 |
| Camptoo | 12-16 |
| Yescapa | 16-20 |
| Manual Entry Tool | 8 |

<span style="color: orange;">Start with 1-2 platforms, expand based on value and feasibility.</span>

<span style="color: green;">**Deferred to Phase 2 (can backtrack historical data):**</span>
- Weather data (~16 hours) - Open-Meteo has data back to 1940
- Events data (~24 hours) - PredictHQ has data back to 2011

---

## Phase 2: AI/Auto Mode (Deferred)

<!--
### AI Model Development (40 hours)

| Task | Description | Hours |
|------|-------------|-------|
| Data Preprocessing Pipeline | • Extract and transform booking data<br>• Feature engineering (occupancy, lead time, seasonality)<br>• Handle missing data | 10 |
| ML Model Development | • XGBoost or LightGBM model<br>• Train on historical data<br>• Hyperparameter tuning | 12 |
| Prediction Service | • API endpoint for price suggestions<br>• Return confidence score<br>• Return adjustment reasons | 8 |
| Model Storage & Versioning | • Store models in cloud storage<br>• Version tracking<br>• Fallback to manual mode | 4 |
| Cold Start Strategy | • Detect insufficient data<br>• Gradual phase-in of AI suggestions | 6 |

### Event Data Integration (24 hours)

| Task | Description | Hours |
|------|-------------|-------|
| PredictHQ Integration | • API integration for events<br>• Store events in database<br>• Daily event sync job | 12 |
| Event Impact Calculation | • Calculate impact per region/date<br>• Leading/event/lagging day classification | 8 |
| Event Dashboard | • Show upcoming events<br>• Display impact on calendar | 4 |

### Auto Mode Dashboard (20 hours)

| Task | Description | Hours |
|------|-------------|-------|
| AI Suggestion Panel | • Show AI-suggested price<br>• Confidence score<br>• Accept/reject buttons | 10 |
| Performance Metrics | • AI vs manual comparison<br>• Revenue impact<br>• Occupancy lift | 6 |
| Model Retraining UI | • Admin controls for retraining<br>• Status and notifications | 4 |

### Monitoring & Feedback Loop (18 hours)

| Task | Description | Hours |
|------|-------------|-------|
| Data Collection Pipeline | • Store booking outcomes<br>• Track AI suggestion acceptance | 8 |
| Model Retraining Workflow | • Weekly/monthly retraining<br>• Performance validation | 10 |

**Phase 2 Total: ~102 hours**

Note: Phase 2 should begin after 6+ months of data collection from Phase 1 scrapers.
-->

<span style="color: gray;">Phase 2 (AI/Auto Mode) is commented out and will be estimated separately after Phase 1 data collection is complete.</span>

---

## Summary Breakdown

| Phase | Section | Hours | Notes |
|-------|---------|-------|-------|
| **Phase 1 (this doc)** | Base Settings | 10 | New fields only |
| | Manual Rules (add'l) | 2 | Min/max clamping |
| | Discounts (add'l) | 4 | Stacking logic |
| | Price Calc (add'l) | 8 | Three-step integration |
| | Scraper Infrastructure | 20 | Time-sensitive |
| | Dashboard UI | 10 | |
| **Phase 1 Total** | | **54** | |
| | | | |
| **Add-ons** | Per-source scraper | 8-24+ | Varies by complexity |
| | Example: 2 platforms | ~32 | |
| **Phase 1 + 2 Sources** | | **~86** | |
| | | | |
| **Phase 2** | Weather Data | ~16 | Backtrackable |
| | Events Data | ~24 | Backtrackable |
| | AI Model | ~40 | |
| | Auto Mode Dashboard | ~20 | |
| | Monitoring | ~18 | |
| **Phase 2 Total** | | **~118** | |
| | | | |
| **Grand Total (Phase 1+2)** | | **~172-204** | |

<span style="color: blue;">**Note:** Core pricing rules (54h) and quote calculation (20h) are in `estimate_development.md`. This doc adds: base settings, price history, scraper infrastructure.</span>

---

## Notes

### Phase 1 Focus
- Manual mode gives fleet owners full control
- Duration discounts apply automatically regardless of mode
- Data scrapers run in background to collect training data
- No AI/ML infrastructure needed yet

### Why Build Scrapers Now?
- Competitor pricing changes daily - historical data is lost forever
- Weather forecasts are time-limited (14 days max)
- AI training requires 6-12 months of historical data
- Building scrapers early = more training data for Phase 2

### Data Collection Timeline
```
Month 1-3: Scrapers running, collecting data
Month 4-6: Continue collection, analyze patterns
Month 6+:  Enough data to begin AI model training (Phase 2)
```

### Legal Considerations for Scraping
- Competitor scraping may violate ToS of some platforms
- Consider:
  - Manual data entry with browser extension assist
  - Public API partnerships (if available)
  - Aggregated market data from industry reports
- Weather data from Open-Meteo is free and legal

---

## Exclusions (Not in Estimate)

- **PredictHQ subscription** (~$200-500/month for events API)
- **ML infrastructure setup** (if not already available, add 15-20 hours)
- **Mobile app support** for pricing management
- **Advanced AI features** (reinforcement learning, personalization)
- **Multi-currency dynamic adjustments**
