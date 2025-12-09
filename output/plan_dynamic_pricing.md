# Dynamic Pricing System - Architecture & Development Plan

**Project:** Jolly Campers V3  
**Component:** Hybrid Dynamic Pricing System  
**Last Updated:** November 25, 2025

---

## Table of Contents

**1. [System Overview](#1-system-overview)**
- 1.1 [Three-Step Pricing System](#three-step-pricing-system)
- 1.2 [Step 1: Base Settings](#step-1-base-settings-foundation)
- 1.3 [Step 2: Dynamic Pricing](#step-2-dynamic-pricing-choose-one-mode)
  - Manual Mode
  - Auto Mode (AI-Driven)
- 1.4 [Step 3: Discounts](#step-3-discounts-always-apply)

**2. [System Architecture](#2-system-architecture)**
- 2.1 [High-Level Architecture](#21-high-level-architecture)
- 2.2 [Technology Stack](#22-technology-stack)
- 2.3 [Data Models](#23-data-models)
- 2.4 [API Design](#24-api-design)

**3. [AI-Driven Dynamic Pricing](#3-ai-driven-dynamic-pricing)**
- 3.1 [ML Model Design](#31-ml-model-design)
- 3.2 [AI Training - Internal Data](#32-ai-training---internal-data)
- 3.3 [Geographic Region Strategy](#33-geographic-region-strategy)
- 3.4 [Data Sources for AI Training](#34-data-sources-for-ai-training)
    - 3.4.1 [External Data Sources Overview](#341-external-data-sources-overview)
    - 3.4.2 [Competitor Pricing](#342-competitor-pricing)
    - 3.4.3 [Weather Data](#343-weather-data)
    - 3.4.4 [Local Occupancy Data](#344-local-occupancy-data)
    - 3.4.5 [Events & Festivals (PredictHQ)](#345-events--festivals-predicthq)

**4. [Implementation & Deployment](#4-implementation--deployment)**
- 4.1 [Implementation Phases](#41-implementation-phases)

**5. [Operations & Governance](#5-operations--governance)**
- 5.1 [Technical Considerations](#51-technical-considerations)
- 5.2 [Security & Compliance](#52-security--compliance)
- 5.3 [Monitoring & Observability](#53-monitoring--observability)
- 5.4 [Deployment Plan](#54-deployment-plan)

**6. [Planning & Success](#6-planning--success)**
- 6.1 [Future Enhancements](#61-future-enhancements)
- 6.2 [Success Criteria](#62-success-criteria)
- 6.3 [Risks & Mitigation](#63-risks--mitigation)

---

# 1. System Overview

### Three-Step Pricing System

The pricing system has **three sequential steps**:

```
┌─────────────────────────────────────────────────────────────────┐
│                                                                 │
│  STEP 1: BASE SETTINGS (Foundation)                             │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │ • Standard Price: €85/night                              │   │
│  │ • Minimum Price: €65 (floor)                             │   │
│  │ • Maximum Price: €150 (ceiling)                          │   │
│  └──────────────────────────────────────────────────────────┘   │
│                           ↓                                     │
│                                                                 │
│  STEP 2: DYNAMIC PRICING (Choose One)                           │
│  ┌───────────────────────────┬──────────────────────────────┐   │
│  │   MANUAL MODE             │    AUTO MODE (AI)            │   │
│  ├───────────────────────────┼──────────────────────────────┤   │
│  │ Owner defines rules:      │ AI handles pricing:          │   │
│  │ • Seasonal adjustments    │ • Learns seasonal demand     │   │
│  │   "Summer +25%"           │ • Monitors competitors       │   │
│  │ • Weekend premiums        │ • Weather & events           │   │
│  │ • Holiday pricing         │ • Regional occupancy         │   │
│  │ • Custom rules            │ • Real-time optimization     │   │
│  └───────────────────────────┴──────────────────────────────┘   │
│                           ↓                                     │
│                                                                 │
│  STEP 3: DISCOUNTS (Always Apply)                               │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │ • Duration: 7d=-10%, 14d=-15%, 28d=-20%                  │   │
│  │ • Repeat Customer: -5% (future)                          │   │
│  │ → Applied AFTER dynamic pricing                          │   │
│  └──────────────────────────────────────────────────────────┘   │
│                           ↓                                     │
│                                                                 │
│  FINAL PRICE (Check Min/Max Boundaries)                         │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Step 1: Base Settings (Foundation)

Every camper must have these foundational settings:

| Setting | Description | Example |
|---------|-------------|---------|
| **Standard Price** | Starting nightly rate | €85/night |
| **Minimum Price** | Price floor (never go below) | €65/night |
| **Maximum Price** | Price ceiling (never exceed) | €150/night |

```
Owner sets for "VW California":
├── Standard Price: €85/night
├── Minimum Price: €65/night
└── Maximum Price: €150/night
```

---

## Step 2: Dynamic Pricing (Choose One Mode)

### Manual Mode

**For owners who want full control over pricing rules.**

Owner defines custom rules for market adjustments:

#### Seasonal Adjustments

```
Seasonal Rules:
├── Summer (Jun-Aug): +25%        → Peak camping season
├── Christmas/NY (Dec 20-Jan 5): +40%  → Holiday premium
├── Spring (Apr-May): +15%        → Good weather returns
└── Winter (Nov-Mar): -10%        → Off-season discount
```

#### Weekend & Holiday Premiums

```
Day-of-Week Rules:
├── Friday-Sunday: +15%           → Weekend premium
├── Public holidays: +20%         → Holiday rates
└── Weekdays: Standard price      → No adjustment
```

#### Custom Rules

```
Other Manual Rules:
├── School holidays: +20%         → Family travel season
├── Local events: +30%            → Manually set for known events
└── Gap fillers (1-2 nights): -15%  → Fill small gaps
```

**Manual Mode Example:**
```
Standard Price: €85
Booking in July (summer) on weekend:
├── Summer rule: +25%             = €106.25
├── Weekend premium: +15%         = €122.19
└── Final (before discounts)      = €122.19/night
```

---

### Auto Mode (AI-Driven)

**For owners who want hands-off optimization.** AI learns and adapts automatically.

#### What AI Analyzes

| Data Source | How AI Uses It |
|-------------|----------------|
| **Internal Booking History** | Learns your seasonal demand patterns |
| **Competitor Pricing** | Positions you competitively in the market |
| **Weather Forecast** | Increases prices for good camping weather |
| **Events & Festivals** | Prices up during nearby events (F1, concerts) |
| **Regional Occupancy** | Raises prices when area is in high demand |

#### AI Learning Examples

```
AI Learns Seasonal Patterns:
├── "This camper always books fast in July-Aug"
│   → AI suggests +25% in summer
│
├── "Competitors charge 30% more in December"
│   → AI suggests +30% for holiday season
│
└── "80% occupancy in Noord-Holland in June"
    → AI suggests +15% for that region
```

```
AI Responds to Real-Time Data:
├── "F1 Dutch Grand Prix next weekend"
│   → AI suggests +40% for event weekend
│
├── "Sunny weather forecast for next week"
│   → AI suggests +10% for good weather
│
└── "Competitors raised prices 15% this week"
    → AI suggests +12% to stay competitive
```

**Auto Mode Example:**
```
Standard Price: €85
AI analyzing July weekend booking:
├── Historical: July peak season   = +€20
├── Competitors: Market avg €125   = +€8
├── Weather: Sunny forecast        = +€6
├── Event: Music festival nearby   = +€12
├── Occupancy: 82% regional        = +€7
AI Suggested Price: €138/night
├── Confidence: 84%
└── Within bounds: €65-€150 ✓
```

---

## Step 3: Discounts (Always Apply)

**Discounts are separate from dynamic pricing.** They apply AFTER Step 2, regardless of Manual or Auto mode.

### Duration Discounts

Incentivize longer stays to reduce turnover and maximize occupancy:

| Duration | Discount | Reason |
|----------|----------|--------|
| 7+ days | -10% | Weekly bookings |
| 14+ days | -15% | 2-week trips |
| 28+ days | -20% | Monthly stays |
| 90+ days | -30% | Long-term rentals |

**Example:**
```
Price after dynamic pricing: €120/night
7-day booking: €120 × 0.90 = €108/night
Total: €756 for 7 nights
```

### Repeat Customer Discount (Future)

Reward loyalty:

```
Repeat Customer: -5%
└── Applied to customers with 2+ previous bookings
```

### How Discounts Work

```
Full Calculation:
1. Base Settings: €85
2. Dynamic Pricing (Manual/Auto): €120  ← Step 2
3. Duration Discount (7d): -10%: €108   ← Step 3
4. Repeat Customer: -5%: €102.60        ← Step 3
5. Check Min/Max (€65-€150): €102.60 ✓
```

> **Key:** Discounts always apply AFTER dynamic pricing, whether you use Manual or Auto mode.

---

### Comparison: Manual vs Auto

| Aspect | Manual Mode | Auto Mode |
|--------|-------------|-----------|
| **Setup** | Define seasonal rules upfront | Just set min/max |
| **Maintenance** | Update rules manually | AI learns continuously |
| **Seasonal Pricing** | ✅ Owner defines | ✅ AI learns automatically |
| **Market Response** | ❌ Static rules | ✅ Dynamic, real-time |
| **Best For** | Predictable pricing | Growth & optimization |
| **Owner Effort** | Medium (rule management) | Low (review suggestions) |
| **Discounts** | ✅ Applied after (Step 3) | ✅ Applied after (Step 3) |

---

### Key Principles

1. **Three sequential steps** - Base → Dynamic → Discounts
2. **Choose ONE mode** - Manual OR Auto for dynamic pricing (Step 2)
3. **Discounts always apply** - After Step 2, regardless of mode
4. **Min/Max sacred** - Final price never exceeds boundaries
5. **Full transparency** - Every price shows complete breakdown
6. **Owner control** - Can switch modes or override anytime
7. **AI learns over time** - Improves with more booking data

---

### Price Breakdown Examples

#### Example 1: MANUAL Mode

```
Booking: Aug 15-22 (7 nights), Amsterdam area
Camper: VW California

STEP 1 - Base Settings:
├── Standard Price: €85/night
├── Min: €65, Max: €150

STEP 2 - Dynamic Pricing (MANUAL):
├── Summer season rule (Jun-Aug): +25%   = €106.25
├── Weekend included (Sat-Sun): +10%     = €116.88

STEP 3 - Discounts:
├── 7-day duration: -10%                 = €105.19

FINAL PRICE: €105.19/night
├── Total for 7 nights: €736.33
├── Within bounds: €65-€150 ✓
└── Breakdown shown to customer

Note: Owner manually set summer +25% rule.
Duration discount applied automatically after.
```

#### Example 2: AUTO Mode (AI)

```
Booking: Aug 15-22 (7 nights), Amsterdam area
Camper: VW California

STEP 1 - Base Settings:
├── Standard Price: €85/night
├── Min: €65, Max: €150

STEP 2 - Dynamic Pricing (AUTO):
AI Analysis:
├── Historical: August peak demand       = +€25
├── Competitors: Market avg €130         = +€10
├── Weather: Sunny week forecast         = +€8
├── Event: Sail Amsterdam nearby         = +€15
├── Regional occupancy: 78%              = +€5
├── AI Suggested Price (Step 2)          = €148

STEP 3 - Discounts:
├── 7-day duration: -10%                 = €133.20

FINAL PRICE: €133.20/night
├── Total for 7 nights: €932.40
├── Within bounds: €65-€150 ✓
├── AI Confidence: 84%
└── Auto-approved (or awaiting review)

Note: AI learned August is peak season automatically.
Duration discount applied after AI pricing.
```

#### Example 3: Same Booking, Different Modes

```
Scenario: 28-day booking in November (off-season)
Camper: VW California, Base: €85

┌─────────────────────────────────────────────────────────┐
│                    MANUAL MODE                          │
├─────────────────────────────────────────────────────────┤
│ Step 1: Base Price                       = €85          │
│ Step 2: Winter rule (-10%)               = €76.50       │
│ Step 3: 28-day discount (-20%)           = €61.20       │
│ FINAL: €61.20/night × 28 = €1,713.60                   │
└─────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────┐
│                     AUTO MODE                           │
├─────────────────────────────────────────────────────────┤
│ Step 1: Base Price                       = €85          │
│ Step 2: AI Analysis:                                    │
│   - Historical: Nov is slow (-€8)                       │
│   - Competitors: Avg €72 (-€5)                          │
│   - Weather: Cold/rainy (-€2)                           │
│   - Occupancy: 45% low (-€5)                            │
│   AI Price                               = €65          │
│ Step 3: 28-day discount (-20%)           = €52          │
│ FINAL: €52/night × 28 = €1,456                         │
└─────────────────────────────────────────────────────────┘

Result: AI mode captures more nuanced market conditions
and suggests lower price to maximize booking probability.
```

#### Example 4: Repeat Customer

```
Booking: 5 nights in May, repeat customer
Camper: VW California, Base: €85

STEP 1: Base                             = €85
STEP 2: Manual (Spring +15%)             = €97.75
STEP 3: Discounts
  - No duration discount (< 7 days)
  - Repeat customer: -5%                 = €92.86
  
FINAL: €92.86/night × 5 = €464.30
└── Loyalty rewarded!
```

---

# 2. System Architecture

## 2.1 High-Level Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                     Frontend Layer                          │
├─────────────────────────────────────────────────────────────┤
│  Fleet Owner Dashboard  │  Super Admin Dashboard            │
│  - Pricing Settings     │  - AI Toggle                      │
│  - Price Suggestions    │  - Performance Overview           │
│  - Accept/Reject UI     │  - Outlier Detection              │
└──────────────┬──────────────────────────┬───────────────────┘
               │                          │
               ▼                          ▼
┌─────────────────────────────────────────────────────────────┐
│                      API Layer (Node.js)                    │
├─────────────────────────────────────────────────────────────┤
│  • Pricing Service API                                      │
│  • Owner Settings API                                       │
│  • Price History API                                        │
│  • Admin Control API                                        │
└──────────────┬──────────────────────────┬───────────────────┘
               │                          │
               ▼                          ▼
┌──────────────────────────┐   ┌──────────────────────────────┐
│  Rule-Based Engine       │   │  AI Prediction Service       │
│  (Node.js)               │   │  (Python/Flask)              │
├──────────────────────────┤   ├──────────────────────────────┤
│  • Seasonal Rules        │   │  • Model Inference           │
│  • Region Adjustments    │   │  • Confidence Scoring        │
│  • Amenity Modifiers     │   │  • Feature Engineering       │
│  • Min/Max Clamping      │   │  • Model Loading/Caching     │
└──────────────┬───────────┘   └───────────┬──────────────────┘
               │                           │
               ▼                           ▼
┌─────────────────────────────────────────────────────────────┐
│                    Data Layer                               │
├─────────────────────────────────────────────────────────────┤
│  PostgreSQL Database     │  Redis Cache  │  Model Storage   │
│  - Pricing Settings      │  - Predictions│  (S3/GCS)        │
│  - Price History         │  - API Cache  │  - Trained Models│
│  - Booking Data          │               │  - Versions      │
│  - Market Data           │               │                  │
└──────────────┬──────────────────────────┬───────────────────┘
               │                          │
               ▼                          ▼
┌─────────────────────────────────────────────────────────────┐
│                   Background Jobs                           │
├─────────────────────────────────────────────────────────────┤
│  • Daily Price Updates (Cron)                               │
│  • Model Retraining (Weekly/Monthly)                        │
│  • Performance Analytics                                    │
│  • Data Aggregation                                         │
└─────────────────────────────────────────────────────────────┘
```

### Component Interactions

**Price Suggestion Flow:**

```
1. Cron Job (Daily) → Pricing Service
2. Pricing Service → Rule-Based Engine (get base price)
3. Pricing Service → AI Service (get adjustment factor)
4. Pricing Service → Combine + Clamp (owner min/max)
5. Pricing Service → Store suggestion in DB
6. Frontend → Fetch suggestions
7. Owner → Accept/Reject
8. If Accepted → Update camper price
```

**Model Retraining Flow:**

```
1. Cron Job (Weekly) → Training Pipeline
2. Training Pipeline → Extract booking data
3. Training Pipeline → Feature engineering
4. Training Pipeline → Train new model
5. Training Pipeline → Validate performance
6. If improved → Save to Model Storage
7. AI Service → Load new model
8. Notify Admin → Retraining complete
```

---

## 2.2 Technology Stack

### Backend

| Component | Technology | Purpose |
|-----------|-----------|---------|
| **API Server** | Node.js + Express | Main application API |
| **AI Service** | Python + Flask | ML model inference |
| **Database** | PostgreSQL | Persistent data storage |
| **Cache** | Redis | API caching, prediction cache |
| **Message Queue** | Bull (Redis) | Background job processing |
| **Model Storage** | AWS S3 / Google Cloud Storage | Trained model artifacts |
| **ML Framework** | scikit-learn, XGBoost, LightGBM | Model training & inference |
| **Data Processing** | Pandas, NumPy | Feature engineering |

### Frontend

| Component | Technology | Purpose |
|-----------|-----------|---------|
| **Framework** | React.js | UI components |
| **State Management** | Redux / Context API | Global state |
| **Charts** | Recharts / Chart.js | Pricing visualizations |
| **Date Picker** | react-datepicker | Lock date selection |

### DevOps

| Component | Technology | Purpose |
|-----------|-----------|---------|
| **Containerization** | Docker | AI service isolation |
| **Orchestration** | Docker Compose / Kubernetes | Multi-service deployment |
| **CI/CD** | GitHub Actions | Automated testing & deployment |
| **Monitoring** | Prometheus + Grafana | Metrics & dashboards |
| **Logging** | Winston + ELK Stack | Centralized logging |

---

## 2.3 Data Models

### Pricing Settings

```sql
-- STEP 1: Base Settings (Foundation for every camper)
CREATE TABLE pricing_settings (
  id SERIAL PRIMARY KEY,
  fleet_owner_id INT REFERENCES users(id),
  camper_id INT REFERENCES campers(id),
  
  -- Base settings
  standard_price DECIMAL(10, 2) NOT NULL,    -- Base nightly rate
  min_price DECIMAL(10, 2) NOT NULL,         -- Floor (never go below)
  max_price DECIMAL(10, 2) NOT NULL,         -- Ceiling (never exceed)
  
  -- STEP 2: Dynamic pricing mode (choose one)
  pricing_mode VARCHAR(20) DEFAULT 'manual', -- 'manual' or 'auto'
  
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW(),
  CONSTRAINT valid_price_range CHECK (min_price <= standard_price AND standard_price <= max_price),
  UNIQUE (fleet_owner_id, camper_id)
);
```

### Manual Pricing Rules (Step 2 - Manual Mode)

```sql
-- STEP 2: Manual Pricing Rules (if pricing_mode = 'manual')
-- These are dynamic pricing adjustments (seasonal, weekend, events, etc.)
CREATE TABLE manual_pricing_rules (
  id SERIAL PRIMARY KEY,
  fleet_owner_id INT REFERENCES users(id),
  camper_id INT REFERENCES campers(id),      -- NULL = applies to all owner's campers
  rule_name VARCHAR(100) NOT NULL,
  rule_type VARCHAR(30) NOT NULL,            -- seasonal, weekend, holiday, custom
  is_active BOOLEAN DEFAULT true,
  priority INT DEFAULT 0,                    -- Higher priority rules apply first

  -- Date Range (for seasonal/holiday rules)
  start_month INT,                           -- 1-12
  start_day INT,                             -- 1-31
  end_month INT,                             -- 1-12
  end_day INT,                               -- 1-31
  recurrence VARCHAR(20),                    -- yearly, once

  -- Day of Week (for weekend premiums)
  days_of_week INT[],                        -- [5,6] = Friday, Saturday (0=Sunday)

  -- Adjustment
  adjustment_type VARCHAR(10) NOT NULL,      -- percentage, fixed
  adjustment_value DECIMAL(10, 2) NOT NULL,  -- +25 = +25%, -10 = -10%

  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW()
);

-- Example manual rules:
-- Seasonal
INSERT INTO manual_pricing_rules (fleet_owner_id, rule_name, rule_type, start_month, start_day, end_month, end_day, recurrence, adjustment_type, adjustment_value)
VALUES
(1, 'Summer Season', 'seasonal', 6, 1, 8, 31, 'yearly', 'percentage', 25),
(1, 'Christmas/NY', 'holiday', 12, 20, 1, 5, 'yearly', 'percentage', 40),
(1, 'Spring Season', 'seasonal', 4, 1, 5, 31, 'yearly', 'percentage', 15),
(1, 'Winter Discount', 'seasonal', 11, 1, 3, 31, 'yearly', 'percentage', -10);

-- Weekend/Day of week
INSERT INTO manual_pricing_rules (fleet_owner_id, rule_name, rule_type, days_of_week, adjustment_type, adjustment_value)
VALUES
(1, 'Weekend Premium', 'weekend', ARRAY[5,6], 'percentage', 15),
(1, 'Public Holiday', 'holiday', NULL, 'percentage', 20);
```

### Discounts (Step 3 - Always Apply)

```sql
-- STEP 3: Discounts (applied AFTER dynamic pricing, regardless of mode)
CREATE TABLE discount_rules (
  id SERIAL PRIMARY KEY,
  fleet_owner_id INT REFERENCES users(id),
  camper_id INT REFERENCES campers(id),      -- NULL = applies to all owner's campers
  discount_name VARCHAR(100) NOT NULL,
  discount_type VARCHAR(30) NOT NULL,        -- duration, repeat_customer, promo
  is_active BOOLEAN DEFAULT true,

  -- Duration-based
  min_nights INT,                            -- Minimum nights for discount
  
  -- Repeat customer
  min_previous_bookings INT,                 -- e.g., 2 = customer must have 2+ previous bookings

  -- Promo code (future)
  promo_code VARCHAR(50),
  valid_from DATE,
  valid_until DATE,

  -- Discount amount
  discount_type_value VARCHAR(10) NOT NULL,  -- percentage, fixed
  discount_value DECIMAL(10, 2) NOT NULL,    -- -10 = -10% discount

  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW()
);

-- Example discounts:
INSERT INTO discount_rules (fleet_owner_id, discount_name, discount_type, min_nights, discount_type_value, discount_value)
VALUES
(1, 'Weekly Discount', 'duration', 7, 'percentage', -10),
(1, '2-Week Discount', 'duration', 14, 'percentage', -15),
(1, 'Monthly Discount', 'duration', 28, 'percentage', -20),
(1, 'Long-Term Discount', 'duration', 90, 'percentage', -30);

-- Repeat customer
INSERT INTO discount_rules (fleet_owner_id, discount_name, discount_type, min_previous_bookings, discount_type_value, discount_value)
VALUES (1, 'Repeat Customer', 'repeat_customer', 2, 'percentage', -5);
```

### Rule Templates

```sql
-- Pre-defined templates for Manual Pricing Rules
CREATE TABLE manual_pricing_rule_templates (
  id SERIAL PRIMARY KEY,
  template_name VARCHAR(100) NOT NULL,
  description TEXT,
  rule_type VARCHAR(30) NOT NULL,
  start_month INT,
  start_day INT,
  end_month INT,
  end_day INT,
  recurrence VARCHAR(20),
  days_of_week INT[],
  adjustment_type VARCHAR(10) NOT NULL,
  adjustment_value DECIMAL(10, 2) NOT NULL
);

INSERT INTO manual_pricing_rule_templates (template_name, description, rule_type, start_month, end_month, recurrence, adjustment_type, adjustment_value)
VALUES
('Summer Peak', 'Jun-Aug peak season pricing', 'seasonal', 6, 8, 'yearly', 'percentage', 25),
('Christmas Holiday', 'Dec 20 - Jan 5 holiday pricing', 'holiday', 12, 1, 'yearly', 'percentage', 40),
('Spring Season', 'Apr-May spring pricing', 'seasonal', 4, 5, 'yearly', 'percentage', 15),
('Weekend Premium', 'Friday-Sunday premium', 'weekend', NULL, NULL, NULL, 'percentage', 15);

-- Pre-defined templates for Discounts
CREATE TABLE discount_rule_templates (
  id SERIAL PRIMARY KEY,
  template_name VARCHAR(100) NOT NULL,
  description TEXT,
  discount_type VARCHAR(30) NOT NULL,
  min_nights INT,
  min_previous_bookings INT,
  discount_type_value VARCHAR(10) NOT NULL,
  discount_value DECIMAL(10, 2) NOT NULL
);

INSERT INTO discount_rule_templates (template_name, description, discount_type, min_nights, discount_type_value, discount_value)
VALUES
('Weekly Discount', 'Standard 7+ day discount', 'duration', 7, 'percentage', -10),
('2-Week Discount', 'Standard 14+ day discount', 'duration', 14, 'percentage', -15),
('Monthly Discount', 'Standard 28+ day discount', 'duration', 28, 'percentage', -20),
('Loyalty Discount', 'Repeat customer discount', 'repeat_customer', NULL, 'percentage', -5);
```

### Price History

```sql
CREATE TABLE price_history (
  id SERIAL PRIMARY KEY,
  camper_id INT REFERENCES campers(id),
  old_price DECIMAL(10, 2),
  new_price DECIMAL(10, 2) NOT NULL,
  change_source VARCHAR(50) NOT NULL, -- 'AI', 'Owner', 'Rule', 'Manual'
  change_reason TEXT, -- JSON string with factors
  confidence_score DECIMAL(5, 4), -- 0-1 for AI suggestions
  suggested_at TIMESTAMP,
  applied_at TIMESTAMP DEFAULT NOW(),
  applied_by INT REFERENCES users(id), -- null if automated
  status VARCHAR(20) DEFAULT 'Applied', -- Suggested, Accepted, Rejected, Applied
  INDEX idx_camper_date (camper_id, applied_at)
);
```

### Locked Dates

```sql
CREATE TABLE locked_dates (
  id SERIAL PRIMARY KEY,
  camper_id INT REFERENCES campers(id),
  start_date DATE NOT NULL,
  end_date DATE NOT NULL,
  lock_reason VARCHAR(50) NOT NULL, -- 'Booking', 'Manual', 'System'
  booking_id INT REFERENCES bookings(id), -- nullable
  created_at TIMESTAMP DEFAULT NOW(),
  INDEX idx_camper_dates (camper_id, start_date, end_date)
);
```

### Market Data

```sql
CREATE TABLE market_data (
  id SERIAL PRIMARY KEY,
  region VARCHAR(100) NOT NULL,
  camper_type VARCHAR(50) NOT NULL,
  date DATE NOT NULL,
  median_price DECIMAL(10, 2),
  sample_size INT,
  data_source VARCHAR(50), -- 'Manual', 'Scraper', 'API'
  created_at TIMESTAMP DEFAULT NOW(),
  UNIQUE (region, camper_type, date)
);
```

### Event Calendar

```sql
CREATE TABLE event_calendar (
  id SERIAL PRIMARY KEY,
  name VARCHAR(200) NOT NULL,
  region VARCHAR(100) NOT NULL,
  start_date DATE NOT NULL,
  end_date DATE NOT NULL,
  impact_level VARCHAR(20), -- 'Low', 'Medium', 'High'
  price_impact_percent DECIMAL(5, 2), -- e.g., 12.00 for +12%
  created_by INT REFERENCES users(id),
  created_at TIMESTAMP DEFAULT NOW(),
  INDEX idx_region_dates (region, start_date, end_date)
);
```

### ML Training Data (View)

```sql
CREATE VIEW ml_training_data AS
SELECT
  b.id AS booking_id,
  b.camper_id,
  c.type AS camper_type,
  c.region,
  b.start_date,
  b.end_date,
  b.total_price,
  b.total_price / NULLIF(EXTRACT(DAY FROM (b.end_date - b.start_date)), 0) AS daily_price,
  EXTRACT(DOW FROM b.start_date) AS day_of_week,
  EXTRACT(MONTH FROM b.start_date) AS month,
  EXTRACT(DAY FROM (b.created_at - b.start_date)) AS lead_time_days,
  b.status,
  (SELECT COUNT(*) FROM bookings WHERE camper_id = b.camper_id AND start_date BETWEEN b.start_date - INTERVAL '30 days' AND b.start_date) AS bookings_last_30_days,
  (SELECT AVG(total_price / NULLIF(EXTRACT(DAY FROM (end_date - start_date)), 0)) FROM bookings WHERE camper_id = b.camper_id AND start_date < b.start_date) AS historical_avg_price
FROM bookings b
JOIN campers c ON b.camper_id = c.id
WHERE b.status = 'Confirmed';
```

---

## 2.4 API Design

### Owner Pricing Settings

#### POST /api/pricing/settings
**Create or update pricing settings**

**Request:**
```json
{
  "camper_id": 123,
  "min_price": 90.00,
  "max_price": 150.00,
  "pricing_mode": "Balanced",
  "adjustment_frequency": "Daily",
  "ai_enabled": true
}
```

**Response:**
```json
{
  "success": true,
  "settings": {
    "id": 456,
    "camper_id": 123,
    "min_price": 90.00,
    "max_price": 150.00,
    "pricing_mode": "Balanced",
    "adjustment_frequency": "Daily",
    "ai_enabled": true,
    "updated_at": "2025-11-25T10:30:00Z"
  }
}
```

#### GET /api/pricing/settings/:ownerId
**Retrieve pricing settings for owner**

**Response:**
```json
{
  "success": true,
  "settings": [
    {
      "camper_id": 123,
      "camper_name": "VW California Ocean",
      "standard_price": 85.00,
      "min_price": 65.00,
      "max_price": 150.00,
      "pricing_mode": "semi_auto",
      "ai_enabled": true,
      "current_price": 110.00
    }
  ]
}
```

### Pricing Rules (Layer 2)

#### GET /api/pricing/rules/:ownerId
**Get all pricing rules for owner**

**Response:**
```json
{
  "success": true,
  "rules": [
    {
      "id": 1,
      "rule_name": "Summer Season",
      "rule_type": "seasonal",
      "camper_id": null,
      "is_active": true,
      "start_month": 5,
      "start_day": 1,
      "end_month": 8,
      "end_day": 31,
      "recurrence": "yearly",
      "adjustment_type": "percentage",
      "adjustment_value": 20
    },
    {
      "id": 2,
      "rule_name": "Christmas/New Year",
      "rule_type": "seasonal",
      "camper_id": null,
      "is_active": true,
      "start_month": 12,
      "start_day": 1,
      "end_month": 1,
      "end_day": 5,
      "recurrence": "yearly",
      "adjustment_type": "percentage",
      "adjustment_value": 50
    },
    {
      "id": 3,
      "rule_name": "Monthly Discount",
      "rule_type": "duration",
      "camper_id": null,
      "is_active": true,
      "min_nights": 28,
      "adjustment_type": "percentage",
      "adjustment_value": -20
    }
  ]
}
```

#### POST /api/pricing/rules
**Create a new pricing rule**

**Request:**
```json
{
  "rule_name": "Summer Season",
  "rule_type": "seasonal",
  "camper_id": null,
  "start_month": 5,
  "start_day": 1,
  "end_month": 8,
  "end_day": 31,
  "recurrence": "yearly",
  "adjustment_type": "percentage",
  "adjustment_value": 20
}
```

**Response:**
```json
{
  "success": true,
  "rule": {
    "id": 4,
    "rule_name": "Summer Season",
    "rule_type": "seasonal",
    "is_active": true,
    "created_at": "2025-11-25T10:30:00Z"
  }
}
```

#### PUT /api/pricing/rules/:ruleId
**Update a pricing rule**

#### DELETE /api/pricing/rules/:ruleId
**Delete a pricing rule**

#### GET /api/pricing/rules/templates
**Get pre-defined rule templates**

**Response:**
```json
{
  "success": true,
  "templates": [
    {
      "id": 1,
      "template_name": "Summer Peak Season",
      "description": "Higher prices during summer months",
      "rule_type": "seasonal",
      "start_month": 5,
      "end_month": 8,
      "adjustment_type": "percentage",
      "adjustment_value": 25
    },
    {
      "id": 2,
      "template_name": "Weekly Stay Discount",
      "description": "Discount for bookings of 7+ nights",
      "rule_type": "duration",
      "min_nights": 7,
      "adjustment_type": "percentage",
      "adjustment_value": -10
    }
  ]
}
```

#### POST /api/pricing/rules/from-template
**Create rule from template**

**Request:**
```json
{
  "template_id": 1,
  "adjustment_value": 30
}
```

#### POST /api/pricing/calculate
**Calculate price with rules (preview)**

**Request:**
```json
{
  "camper_id": 123,
  "start_date": "2025-07-15",
  "end_date": "2025-07-22",
  "include_ai": true
}
```

**Response:**
```json
{
  "success": true,
  "calculation": {
    "camper_id": 123,
    "start_date": "2025-07-15",
    "end_date": "2025-07-22",
    "nights": 7,
    "breakdown": {
      "layer1_base": {
        "standard_price": 85.00,
        "subtotal": 85.00
      },
      "layer2_rules": [
        {
          "rule_name": "Summer Season",
          "rule_type": "seasonal",
          "adjustment": "+20%",
          "impact": 17.00,
          "subtotal": 102.00
        },
        {
          "rule_name": "Weekly Discount",
          "rule_type": "duration",
          "adjustment": "-10%",
          "impact": -10.20,
          "subtotal": 91.80
        }
      ],
      "layer3_ai": {
        "weather_impact": 5.00,
        "event_impact": 0.00,
        "occupancy_impact": 3.00,
        "confidence": 0.75,
        "subtotal": 99.80
      }
    },
    "final_price_per_night": 99.80,
    "total_price": 698.60,
    "min_max_check": {
      "min_price": 65.00,
      "max_price": 150.00,
      "within_bounds": true
    }
  }
}
```

### Price Suggestions

#### POST /api/pricing/suggest
**Get AI price suggestion for a camper**

**Request:**
```json
{
  "camper_id": 123,
  "date": "2025-12-15"
}
```

**Response:**
```json
{
  "success": true,
  "suggestion": {
    "camper_id": 123,
    "date": "2025-12-15",
    "current_price": 100.00,
    "base_price": 100.00,
    "suggested_price": 115.00,
    "market_median": 110.00,
    "owner_min": 90.00,
    "owner_max": 150.00,
    "confidence": 0.86,
    "pricing_mode": "Balanced",
    "adjustment_factors": [
      {
        "factor": "Local demand",
        "impact": "+12%",
        "description": "85% of similar campers in Amsterdam are booked this weekend"
      },
      {
        "factor": "Competitor pricing",
        "impact": "+6%",
        "description": "Competitor average rose 7% this week"
      },
      {
        "factor": "Weekend uplift",
        "impact": "+4%",
        "description": "Standard weekend premium"
      }
    ],
    "recommendation": "Consider accepting this suggestion. Strong demand signals and competitive pricing support a +15% increase.",
    "expires_at": "2025-11-26T10:30:00Z"
  }
}
```

#### POST /api/pricing/accept
**Accept AI price suggestion**

**Request:**
```json
{
  "camper_id": 123,
  "suggestion_id": 789,
  "apply_to_dates": ["2025-12-15", "2025-12-16"]
}
```

**Response:**
```json
{
  "success": true,
  "message": "Price updated successfully",
  "updated_dates": ["2025-12-15", "2025-12-16"],
  "new_price": 115.00
}
```

#### POST /api/pricing/reject
**Reject AI price suggestion**

**Request:**
```json
{
  "camper_id": 123,
  "suggestion_id": 789,
  "reason": "Price too high for this period"
}
```

**Response:**
```json
{
  "success": true,
  "message": "Suggestion rejected and logged"
}
```

### Price History

#### GET /api/pricing/history/:camperId
**Get price change history**

**Query Params:**
- `start_date` (optional)
- `end_date` (optional)
- `limit` (default: 50)

**Response:**
```json
{
  "success": true,
  "history": [
    {
      "id": 1001,
      "date": "2025-11-25T10:00:00Z",
      "old_price": 100.00,
      "new_price": 115.00,
      "change_source": "AI",
      "confidence_score": 0.86,
      "status": "Accepted",
      "reason": "Local demand +12%, Competitor avg +6%"
    }
  ],
  "total": 245,
  "page": 1
}
```

### Locked Dates

#### POST /api/pricing/lock-dates
**Manually lock dates**

**Request:**
```json
{
  "camper_id": 123,
  "start_date": "2025-12-01",
  "end_date": "2025-12-05",
  "reason": "Special event pricing manually set"
}
```

**Response:**
```json
{
  "success": true,
  "locked_dates": {
    "id": 567,
    "camper_id": 123,
    "start_date": "2025-12-01",
    "end_date": "2025-12-05",
    "lock_reason": "Manual"
  }
}
```

#### GET /api/pricing/locked-dates/:camperId
**Get locked dates for a camper**

**Response:**
```json
{
  "success": true,
  "locked_dates": [
    {
      "start_date": "2025-12-10",
      "end_date": "2025-12-15",
      "lock_reason": "Booking",
      "booking_id": 8901
    }
  ]
}
```

### Market Data

#### GET /api/pricing/market-data/:region
**Get market median price**

**Query Params:**
- `camper_type` (optional)
- `date` (optional, defaults to today)

**Response:**
```json
{
  "success": true,
  "market_data": {
    "region": "Amsterdam",
    "camper_type": "VW California",
    "median_price": 110.00,
    "sample_size": 45,
    "date": "2025-11-25",
    "last_updated": "2025-11-24T10:00:00Z"
  }
}
```

### Super Admin Controls

#### POST /api/admin/pricing/toggle
**Enable/disable AI pricing globally or by region**

**Request:**
```json
{
  "scope": "global", // or "region" or "camper_type"
  "scope_value": null, // region name or camper type if scoped
  "enabled": false
}
```

**Response:**
```json
{
  "success": true,
  "message": "AI pricing disabled globally"
}
```

#### GET /api/admin/pricing/performance
**Get platform-wide pricing performance**

**Query Params:**
- `start_date` (optional)
- `end_date` (optional)

**Response:**
```json
{
  "success": true,
  "performance": {
    "total_suggestions": 12450,
    "acceptance_rate": 0.68,
    "ai_period_stats": {
      "avg_occupancy": 0.78,
      "avg_daily_rate": 115.00,
      "total_revenue": 458000.00
    },
    "manual_period_stats": {
      "avg_occupancy": 0.72,
      "avg_daily_rate": 105.00,
      "total_revenue": 390000.00
    },
    "improvement": {
      "occupancy_lift": "+8.3%",
      "revenue_lift": "+17.4%"
    }
  }
}
```

---

# 3. AI-Driven Dynamic Pricing

---

## 3.1 ML Model Design

### Model Type: XGBoost Regressor

**Target Variable:** `price_adjustment_factor` (e.g., 1.15 for +15%, 0.92 for -8%)

### Features

#### Internal Features (from platform data)

| Feature | Description | Type |
|---------|-------------|------|
| `camper_type` | VW California, Mercedes Marco Polo, etc. | Categorical |
| `region` | Amsterdam, Rotterdam, Utrecht, etc. | Categorical |
| `day_of_week` | 0-6 (Monday-Sunday) | Numeric |
| `month` | 1-12 | Numeric |
| `is_weekend` | 0 or 1 | Binary |
| `is_holiday` | 0 or 1 | Binary |
| `lead_time_days` | Days between booking and start | Numeric |
| `occupancy_rate_30d` | % of days booked in last 30 days | Numeric |
| `avg_daily_rate` | Historical average for this camper | Numeric |
| `bookings_last_30d` | Number of bookings in last 30 days | Numeric |
| `cancellation_rate` | Cancellation rate for this camper | Numeric |
| `season` | Winter, Spring, Summer, Fall | Categorical |
| `amenities_score` | Derived from camper amenities | Numeric |

#### External Features (optional, Phase 2)

| Feature | Description | Type |
|---------|-------------|------|
| `competitor_median_price` | Market median for similar campers | Numeric |
| `event_impact` | Nearby event impact level | Numeric |
| `weather_score` | Weather attractiveness (0-1) | Numeric |

### Training Pipeline

```python
# Pseudocode

# 1. Data Extraction
def extract_training_data():
    query = """
    SELECT * FROM ml_training_data
    WHERE start_date >= NOW() - INTERVAL '12 months'
    """
    df = execute_query(query)
    return df

# 2. Feature Engineering
def engineer_features(df):
    df['is_weekend'] = df['day_of_week'].isin([5, 6]).astype(int)
    df['season'] = df['month'].map({12:0, 1:0, 2:0, 3:1, 4:1, 5:1, ...})
    df['occupancy_rate_30d'] = df['bookings_last_30d'] / 30.0
    
    # Calculate target (actual price vs historical avg)
    df['price_adjustment_factor'] = df['daily_price'] / df['historical_avg_price']
    
    return df

# 3. Train/Test Split
def split_data(df):
    # Time-based split (last 2 months as test)
    cutoff_date = df['start_date'].max() - timedelta(days=60)
    train = df[df['start_date'] < cutoff_date]
    test = df[df['start_date'] >= cutoff_date]
    return train, test

# 4. Model Training
def train_model(X_train, y_train):
    model = xgb.XGBRegressor(
        n_estimators=100,
        max_depth=6,
        learning_rate=0.1,
        objective='reg:squarederror'
    )
    model.fit(X_train, y_train)
    return model

# 5. Model Evaluation
def evaluate_model(model, X_test, y_test):
    predictions = model.predict(X_test)
    rmse = mean_squared_error(y_test, predictions, squared=False)
    mae = mean_absolute_error(y_test, predictions)
    r2 = r2_score(y_test, predictions)
    return {'rmse': rmse, 'mae': mae, 'r2': r2}

# 6. Model Saving
def save_model(model, version):
    timestamp = datetime.now().strftime('%Y%m%d_%H%M%S')
    filename = f'pricing_model_v{version}_{timestamp}.pkl'
    joblib.dump(model, filename)
    upload_to_s3(filename, 'models/' + filename)
```

### Inference Pipeline

```python
# Pseudocode

def predict_price_adjustment(camper_id, date):
    # Load model
    model = load_latest_model()
    
    # Get camper features
    camper = get_camper_details(camper_id)
    
    # Engineer features
    features = {
        'camper_type': camper.type,
        'region': camper.region,
        'day_of_week': date.weekday(),
        'month': date.month,
        'is_weekend': 1 if date.weekday() >= 5 else 0,
        'is_holiday': check_if_holiday(date),
        'lead_time_days': (date - datetime.now()).days,
        'occupancy_rate_30d': calculate_occupancy(camper_id, 30),
        'avg_daily_rate': get_historical_avg(camper_id),
        'bookings_last_30d': count_bookings(camper_id, 30),
        'season': get_season(date.month),
        'amenities_score': calculate_amenities_score(camper)
    }
    
    # Predict
    X = pd.DataFrame([features])
    adjustment_factor = model.predict(X)[0]
    
    # Calculate confidence (based on historical variance)
    confidence = calculate_confidence(features, model)
    
    return adjustment_factor, confidence

def calculate_confidence(features, model):
    # Simplified confidence calculation
    # Based on: data density, feature certainty, model performance
    
    # Get similar historical bookings
    similar_count = count_similar_bookings(features, window_days=90)
    
    # Confidence decreases with fewer similar examples
    if similar_count < 5:
        return 0.4
    elif similar_count < 20:
        return 0.6
    elif similar_count < 50:
        return 0.75
    else:
        return 0.85
```

### Cold Start Strategy

**Phase 1 (Months 1-3): Data Collection**
- Use rule-based pricing only
- Display "Learning Mode" indicator
- Collect booking data silently
- No AI suggestions shown to owners

**Phase 2 (Months 4-6): Soft Launch**
- Show AI suggestions with low confidence scores
- Conservative adjustments (±10% max)
- Clear "Beta" labeling
- Track acceptance rate

**Phase 3 (6+ months): Full Operation**
- Full AI suggestions with confidence scores
- Normal adjustment range (±20-30%)
- Performance comparison dashboards
- Continuous learning enabled

---

## 3.2 AI Training - Internal Data

This section provides a detailed, practical guide on how the AI model is trained using only internal platform data.

### 1. Data Sources (All from Jolly Campers Platform)

**A. Booking Data**
```
For each completed booking, we collect:
├── booking_id: 12345
├── camper_id: 789
├── start_date: 2025-06-15
├── end_date: 2025-06-22
├── total_price: €840
├── booking_created_at: 2025-05-20  (when customer booked)
├── status: "Completed"
└── customer_id: 456
```

**B. Camper Data**
```
For each camper:
├── camper_id: 789
├── type: "VW California Ocean"
├── year: 2022
├── region: "Amsterdam"
├── base_price: €120/night
├── amenities: ["Solar Panel", "Bike Rack", "Kitchen", "Heater"]
├── seats: 4
├── beds: 2
└── fleet_owner_id: 101
```

**C. Historical Pricing Data**
```
Every price change ever made:
├── camper_id: 789
├── date: 2025-06-15
├── price: €130
├── previous_price: €120
└── changed_by: "Owner" or "AI" or "Rule"
```

**D. Availability/Calendar Data**
```
├── camper_id: 789
├── date: 2025-06-15
├── status: "Booked" / "Available" / "Blocked"
└── booking_id: 12345 (if booked)
```

### 2. Feature Engineering (Transforming Raw Data into AI Features)

From the raw data above, we create **features** the AI can learn from:

#### Example: Training Data Row for One Booking

| Feature | Value | How We Calculate It |
|---------|-------|---------------------|
| `daily_price` | €120 | total_price ÷ number_of_days |
| `camper_type` | "VW California" | From camper data |
| `region` | "Amsterdam" | From camper data |
| `day_of_week` | 6 (Saturday) | Extract from start_date |
| `month` | 6 (June) | Extract from start_date |
| `is_weekend` | 1 | If day_of_week is 5 or 6 |
| `is_summer` | 1 | If month is 6, 7, or 8 |
| `is_holiday` | 0 | Check against Dutch holiday calendar |
| `lead_time_days` | 26 | Days between booking_created_at and start_date |
| `trip_duration` | 7 | end_date - start_date |
| `occupancy_30d` | 0.73 | Count booked days in last 30 days ÷ 30 |
| `bookings_last_30d` | 3 | Count bookings for this camper in last 30 days |
| `avg_price_last_90d` | €115 | Average daily price for this camper |
| `amenities_score` | 8 | Count of premium amenities |
| `camper_age` | 3 | Current year - camper year |
| `days_until_trip` | 26 | Same as lead_time_days |
| `region_avg_price` | €110 | Average price of all campers in Amsterdam |
| `type_avg_price` | €125 | Average price of all VW Californias |

### 3. Building the Training Dataset

#### SQL Query to Extract Training Data

```sql
SELECT
    b.id AS booking_id,
    b.camper_id,
    c.type AS camper_type,
    c.region,
    c.year AS camper_year,

    -- Price (TARGET VARIABLE)
    b.total_price / EXTRACT(DAY FROM (b.end_date - b.start_date)) AS daily_price,

    -- Date features
    EXTRACT(DOW FROM b.start_date) AS day_of_week,
    EXTRACT(MONTH FROM b.start_date) AS month,
    EXTRACT(DAY FROM (b.end_date - b.start_date)) AS trip_duration,

    -- Lead time (how far in advance did they book?)
    EXTRACT(DAY FROM (b.start_date - b.created_at)) AS lead_time_days,

    -- Historical performance for this camper
    (
        SELECT COUNT(*) FROM bookings
        WHERE camper_id = b.camper_id
        AND created_at BETWEEN b.created_at - INTERVAL '30 days' AND b.created_at
    ) AS bookings_last_30d,

    (
        SELECT AVG(total_price / EXTRACT(DAY FROM (end_date - start_date)))
        FROM bookings
        WHERE camper_id = b.camper_id
        AND created_at < b.created_at
    ) AS historical_avg_price,

    -- Occupancy rate
    (
        SELECT COUNT(DISTINCT date) / 30.0
        FROM availability
        WHERE camper_id = b.camper_id
        AND status = 'Booked'
        AND date BETWEEN b.start_date - INTERVAL '30 days' AND b.start_date
    ) AS occupancy_rate_30d

FROM bookings b
JOIN campers c ON b.camper_id = c.id
WHERE b.status = 'Completed'
  AND b.start_date >= NOW() - INTERVAL '12 months';
```

#### Example Training Dataset (what the AI sees)

| daily_price | camper_type | region | day_of_week | month | is_weekend | lead_time_days | occupancy_30d | bookings_last_30d |
|-------------|-------------|--------|-------------|-------|------------|----------------|---------------|-------------------|
| €120 | VW California | Amsterdam | 6 | 6 | 1 | 26 | 0.73 | 3 |
| €95 | VW California | Rotterdam | 1 | 2 | 0 | 5 | 0.40 | 1 |
| €150 | Mercedes Marco Polo | Amsterdam | 5 | 7 | 1 | 45 | 0.90 | 5 |
| €85 | VW California | Utrecht | 3 | 11 | 0 | 10 | 0.33 | 2 |
| €140 | VW California | Amsterdam | 6 | 8 | 1 | 60 | 0.87 | 4 |
| ... | ... | ... | ... | ... | ... | ... | ... | ... |

### 4. Training Process (Step by Step)

#### Step 1: Data Preparation

```python
import pandas as pd
from sklearn.model_selection import train_test_split
from sklearn.preprocessing import LabelEncoder
import xgboost as xgb

# Load data from database
df = pd.read_sql(training_query, connection)

print(f"Total bookings: {len(df)}")
# Output: Total bookings: 2,450

# Handle categorical variables
le_type = LabelEncoder()
le_region = LabelEncoder()
df['camper_type_encoded'] = le_type.fit_transform(df['camper_type'])
df['region_encoded'] = le_region.fit_transform(df['region'])

# Create derived features
df['is_weekend'] = df['day_of_week'].isin([5, 6]).astype(int)
df['is_summer'] = df['month'].isin([6, 7, 8]).astype(int)
df['is_winter'] = df['month'].isin([12, 1, 2]).astype(int)
```

#### Step 2: Define Features and Target

```python
# Features the model will learn from
FEATURES = [
    'camper_type_encoded',
    'region_encoded',
    'day_of_week',
    'month',
    'is_weekend',
    'is_summer',
    'is_winter',
    'lead_time_days',
    'trip_duration',
    'occupancy_rate_30d',
    'bookings_last_30d',
    'historical_avg_price',
]

# What we want to predict
TARGET = 'daily_price'

X = df[FEATURES]
y = df[TARGET]

# Split: 80% training, 20% testing
X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=42
)

print(f"Training samples: {len(X_train)}")  # 1,960
print(f"Testing samples: {len(X_test)}")    # 490
```

#### Step 3: Train the Model

```python
# Initialize XGBoost model
model = xgb.XGBRegressor(
    n_estimators=100,      # 100 decision trees
    max_depth=6,           # Tree depth
    learning_rate=0.1,     # How fast to learn
    objective='reg:squarederror'
)

# Train!
model.fit(X_train, y_train)

# Evaluate
from sklearn.metrics import mean_absolute_error, r2_score

predictions = model.predict(X_test)
mae = mean_absolute_error(y_test, predictions)
r2 = r2_score(y_test, predictions)

print(f"Mean Absolute Error: €{mae:.2f}")  # e.g., €8.50
print(f"R² Score: {r2:.3f}")               # e.g., 0.82 (82% accuracy)
```

#### Step 4: Understand What the Model Learned

```python
# Feature importance - what factors matter most?
importance = model.feature_importances_

for feature, imp in sorted(zip(FEATURES, importance), key=lambda x: -x[1]):
    print(f"{feature}: {imp:.3f}")
```

**Example Output:**
```
historical_avg_price:    0.245  ← Past price is most predictive
occupancy_rate_30d:      0.189  ← High demand = higher prices
month:                   0.156  ← Summer vs winter matters
is_weekend:              0.098  ← Weekends cost more
camper_type_encoded:     0.087  ← Premium campers = premium prices
lead_time_days:          0.076  ← Last minute bookings differ
region_encoded:          0.062  ← Amsterdam > other regions
...
```

### 5. Making Predictions (How AI Suggests Prices)

#### Example: Predict Price for a Specific Camper & Date

```python
def predict_price(camper_id, target_date):
    # Get camper details
    camper = get_camper(camper_id)

    # Build feature vector
    features = {
        'camper_type_encoded': le_type.transform([camper.type])[0],
        'region_encoded': le_region.transform([camper.region])[0],
        'day_of_week': target_date.weekday(),
        'month': target_date.month,
        'is_weekend': 1 if target_date.weekday() >= 5 else 0,
        'is_summer': 1 if target_date.month in [6, 7, 8] else 0,
        'is_winter': 1 if target_date.month in [12, 1, 2] else 0,
        'lead_time_days': (target_date - datetime.now()).days,
        'trip_duration': 3,  # assume average
        'occupancy_rate_30d': calculate_occupancy(camper_id, 30),
        'bookings_last_30d': count_recent_bookings(camper_id, 30),
        'historical_avg_price': get_avg_price(camper_id),
    }

    # Predict
    X = pd.DataFrame([features])
    predicted_price = model.predict(X)[0]

    return predicted_price


# Example usage
price = predict_price(camper_id=789, target_date=date(2025, 7, 15))
print(f"AI suggests: €{price:.2f}/night")  # Output: AI suggests: €135.00/night
```

### 6. What the AI Learns (Pattern Examples)

After training on real booking data, the AI learns patterns like:

| Pattern | What AI Learns |
|---------|----------------|
| **Seasonality** | July/August prices are 25-40% higher than January/February |
| **Weekend Premium** | Weekend bookings pay 10-15% more on average |
| **Lead Time** | Bookings made 30+ days ahead pay 8% more than last-minute |
| **High Demand Signal** | When occupancy > 80%, prices can go 20% higher |
| **Camper Type** | Mercedes Marco Polo commands 15% premium over VW California |
| **Regional Variance** | Amsterdam is 12% more expensive than Rotterdam |

### 7. Minimum Data Requirements

**For Model to Work Effectively:**
- 50+ campers in platform
- 100+ completed bookings
- 6+ months of booking history

**If Data is Insufficient:**

```
Phase 1 (Months 1-3): Data Collection
├── Use rule-based pricing only
├── Store all booking data
├── Show "Learning Mode" to owners
└── No AI suggestions yet

Phase 2 (Months 4-6): Basic Model
├── Train initial model
├── Show suggestions with LOW confidence (0.4-0.6)
├── Conservative adjustments only (±10%)
└── Label as "Beta"

Phase 3 (6+ months): Mature Model
├── Full AI suggestions
├── Higher confidence scores (0.7-0.9)
├── Normal adjustments (±20-30%)
└── Continuous weekly retraining
```

### 8. Training Summary

| Aspect | Details |
|--------|---------|
| **Data Needed** | Bookings, campers, calendar, price history |
| **Min Data** | ~100 bookings, 6 months history |
| **Features** | 12-15 features from internal data |
| **Model** | XGBoost Regressor |
| **Training Time** | ~5 minutes for 2,000 bookings |
| **Retraining** | Weekly (automated) |
| **Expected Accuracy** | 80-85% (MAE ~€8-12) |

---

## 3.3 Geographic Region Strategy

When a camper is in a small town with limited data, we use a hierarchical fallback approach.

### Hierarchical Region Fallback

```
┌─────────────────────────────────────────────────────────────┐
│              GEOGRAPHIC DATA FALLBACK                        │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   Level 1: Town/City (most specific)                        │
│   └── "Texel" → Only 3 campers, not enough data ❌          │
│                                                             │
│   Level 2: Province                                         │
│   └── "Noord-Holland" → 45 campers, good data ✅            │
│                                                             │
│   Level 3: Country                                          │
│   └── "Netherlands" → 200+ campers (fallback) ✅            │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### Data Model for Geographic Hierarchy

```sql
-- Each camper stores multiple region levels
camper_id: 789
town: "Schagen"           -- 2 campers (not enough)
province: "Noord-Holland" -- 45 campers (use this!)
country: "Netherlands"    -- 200 campers (fallback)
latitude: 52.7876
longitude: 4.7981
```

### Region Selection Logic

```python
def get_regional_data(camper):
    # Try most specific first
    town_data = get_campers_in_town(camper.town)
    if len(town_data) >= 10:
        return town_data, "town"

    # Fall back to province
    province_data = get_campers_in_province(camper.province)
    if len(province_data) >= 10:
        return province_data, "province"

    # Fall back to country
    return get_campers_in_country(camper.country), "country"
```

### Distance-Based Alternative

Use GPS coordinates and expand radius until enough data:

```python
def get_nearby_campers(lat, lng, min_sample=10):
    radius_km = 25  # Start with 25km

    while radius_km <= 200:  # Max 200km
        nearby = find_campers_within_radius(lat, lng, radius_km)

        if len(nearby) >= min_sample:
            return nearby, radius_km

        radius_km += 25  # Expand by 25km

    # Fallback to country-wide
    return get_all_campers(), "country"
```

**Example:**
```
Camper in Schagen (small town):
├── 25km radius  → 4 campers  ❌
├── 50km radius  → 12 campers ✅ (includes Alkmaar, Den Helder)
└── Use 50km radius data for pricing
```

### Confidence Adjustment by Region Level

```python
def adjust_confidence(base_confidence, region_level):
    adjustments = {
        "town": 1.0,       # Full confidence
        "province": 0.85,  # Slightly lower
        "country": 0.65,   # Much lower
    }
    return base_confidence * adjustments[region_level]
```

---

## 3.4 Data Sources for AI Training

### 3.4.1 External Data Sources Overview

External data can significantly improve pricing accuracy beyond internal platform data.

### Available External Data Sources

| Data Source | Update Frequency | API Cost | Implementation Hours | Impact on Pricing |
|-------------|------------------|----------|---------------------|-------------------|
| **Weather** | Daily | Free tier available | 8-12h | High (±15-25%) |
| **Events/Festivals** | Weekly | Free/Low | 12-16h | Very High (±20-50%) |
| **Competitors** | Weekly | Manual: Free | 20-30h | High (±10-20%) |
| **School Holidays** | Yearly | Free | 4-6h | High (±15-30%) |
| **Public Holidays** | Yearly | Free | 2-4h | Medium (±10-20%) |
| **Fuel Prices** | Weekly | Free | 4-6h | Low (±5-10%) |

### Weather Data

```python
weather_score = {
    'date': '2025-07-15',
    'temperature': 24,        # Celsius
    'rain_probability': 10,   # %
    'sunshine_hours': 12,
    'camping_score': 0.92     # Excellent camping weather
}
```
**Source:** OpenWeatherMap API (free tier: 1,000 calls/day)

### Events & Festivals

```python
event = {
    'name': 'Formula 1 Dutch Grand Prix',
    'location': 'Zandvoort',
    'dates': ['2025-08-29', '2025-08-30', '2025-08-31'],
    'attendance': 300000,
    'impact_radius_km': 100,
    'price_impact': 0.40  # +40%
}
```
**Sources:** Eventbrite API, Ticketmaster API, Manual admin input

### School Holidays

```python
holidays = [
    {'country': 'NL', 'region': 'Noord', 'type': 'Summer',
     'start': '2025-07-19', 'end': '2025-08-31'},
    {'country': 'DE', 'region': 'NRW', 'type': 'Summer',
     'start': '2025-07-14', 'end': '2025-08-26'},
]
```
**Sources:** Government websites, SchoolHolidays.eu API

### Recommended External Data Phases

**Phase 1 (MVP - Low effort, high impact):**
```
✅ School holidays (NL + DE + BE)     ~6 hours
✅ Public holidays (NL)               ~4 hours
✅ Manual event calendar (admin input) ~8 hours
─────────────────────────────────────
Total: ~18 hours
```

**Phase 2 (Post-MVP):**
```
⏳ Weather API integration           ~12 hours
⏳ Events API (Eventbrite)           ~16 hours
⏳ Competitor manual research tool    ~12 hours
─────────────────────────────────────
Total: ~40 hours
```

---

### 3.4.2 Competitor Pricing

Competitor pricing data is one of the most valuable external data sources for dynamic pricing.

### Target Competitor Platforms

| Platform | Market Share | Data Availability |
|----------|--------------|-------------------|
| **Goboony** | Large | Public listings |
| **Camptoo** | Medium | Public listings |
| **Yescapa** | Large (EU) | Public listings |
| **PaulCamper** | Medium | Public listings |

### Data Collection Per Listing

```
For each competitor listing, we collect:
├── Basic Info
│   ├── listing_id, platform, listing_url, last_scraped
│
├── Location
│   ├── city, province, country, latitude, longitude
│
├── Camper Details
│   ├── camper_type, brand, model, year, beds, seats, amenities
│
├── Pricing
│   ├── daily_price, weekly_price, minimum_days, service_fee
│
└── Performance (if visible)
    ├── reviews_count, rating, response_time
```

### Data Collection Methods

| Method | Pros | Cons | Implementation |
|--------|------|------|----------------|
| **Manual Research** | Safe, accurate | Time-consuming | ~8 hours |
| **Browser Extension** | 10x faster, fewer errors | Needs development | ~20 hours |
| **Automated Scraping** | Fully automated | Legal grey area | ~50-60 hours |

**Recommended:** Start with manual research + admin input form for MVP.

---

### Scraping Strategy

#### Search Parameters

```
Duration:    3 nights (short trips = more availability = more price data)
Lead times:  14, 28, 42, 56 days (2-8 weeks ahead)
Frequency:   Daily
Per region:  4 searches/day (one per lead time)
```

**Why these parameters:**
- **3 nights:** Shorter duration = more campers available = more price data points
- **2-8 weeks:** Realistic booking window, avoids last-minute low availability
- **4 samples:** Captures pricing patterns across different lead times

#### Daily Scrape Schedule

```
Daily Scrape (e.g., Dec 9):

For each region (Amsterdam, Rotterdam, etc.):
├── Search 1: Dec 23-26 (14 days ahead, 3 nights)
├── Search 2: Jan 6-9 (28 days ahead, 3 nights)
├── Search 3: Jan 20-23 (42 days ahead, 3 nights)
└── Search 4: Feb 3-6 (56 days ahead, 3 nights)

Record: Available listings with prices
Skip: Unavailable listings (no price shown)
```

#### Price Normalization

Different platforms handle fees differently:

```
Platform A:               Platform B:
├── €100/night            ├── €127/night (all-in)
├── +€50 cleaning fee     └── No extra fees
├── +€30 service fee
└── Total: €380/3 nights  └── Total: €381/3 nights

Both are ~€127/night effective price
```

**Normalization Formula:**
```
effective_price_per_night = (base_price × nights + cleaning_fee + service_fee) / nights
```

**Always use `effective_price_per_night` for comparisons and AI training.**

#### Data Points to Capture

```
Per listing:
├── listing_id, platform
├── region, camper_type, sleeps
├── base_price_per_night (shown price)
├── cleaning_fee (if shown separately)
├── service_fee (if shown separately)
├── total_price (if shown)
├── effective_price_per_night (calculated)
├── scrape_date
├── booking_start, booking_end
└── lead_time_days
```

#### Aggregation Logic

```python
def daily_competitor_scrape(region):
    LEAD_TIMES = [14, 28, 42, 56]  # days ahead
    DURATION = 3  # nights
    all_prices = []

    for lead_time in LEAD_TIMES:
        start = today + timedelta(days=lead_time)
        end = start + timedelta(days=DURATION)

        results = search_platform(region, start, end)

        for listing in results:
            if listing.price:
                # Normalize price
                effective = normalize_price(listing, DURATION)
                all_prices.append(effective)

    # Market summary
    return {
        'region': region,
        'date': today,
        'sample_count': len(all_prices),
        'market_median': median(all_prices),
        'market_avg': mean(all_prices),
    }
```

---

### Database Schema

```sql
-- Competitor listings (master data)
CREATE TABLE competitor_listings (
    id SERIAL PRIMARY KEY,
    platform VARCHAR(50) NOT NULL,       -- 'goboony', 'camptoo', etc.
    external_listing_id VARCHAR(100),
    listing_url VARCHAR(500),
    region VARCHAR(100),
    camper_type VARCHAR(100),
    brand VARCHAR(100),
    sleeps INT,
    is_active BOOLEAN DEFAULT true,
    first_seen_at TIMESTAMP DEFAULT NOW(),
    last_seen_at TIMESTAMP,
    UNIQUE (platform, external_listing_id)
);

-- Price samples (raw scraped data)
CREATE TABLE competitor_price_samples (
    id SERIAL PRIMARY KEY,
    listing_id INT REFERENCES competitor_listings(id),
    scrape_date DATE NOT NULL,

    -- Search parameters
    booking_start DATE NOT NULL,
    booking_end DATE NOT NULL,
    duration_nights INT NOT NULL,
    lead_time_days INT NOT NULL,

    -- Raw price data (what platform shows)
    base_price_per_night DECIMAL(10, 2),
    cleaning_fee DECIMAL(10, 2),
    service_fee DECIMAL(10, 2),
    total_price DECIMAL(10, 2),

    -- Normalized price (for comparison)
    effective_price_per_night DECIMAL(10, 2) NOT NULL,

    created_at TIMESTAMP DEFAULT NOW(),
    INDEX idx_region_date (scrape_date, booking_start),
    INDEX idx_lead_time (lead_time_days)
);

-- Aggregated market data (for AI training)
CREATE TABLE competitor_market_summary (
    id SERIAL PRIMARY KEY,
    scrape_date DATE NOT NULL,
    region VARCHAR(100) NOT NULL,
    camper_type VARCHAR(100),

    -- Aggregated from available listings
    sample_count INT,
    median_price DECIMAL(10, 2),
    avg_price DECIMAL(10, 2),
    min_price DECIMAL(10, 2),
    max_price DECIMAL(10, 2),
    p25_price DECIMAL(10, 2),
    p75_price DECIMAL(10, 2),

    created_at TIMESTAMP DEFAULT NOW(),
    UNIQUE (scrape_date, region, camper_type)
);
```

### Aggregating Competitor Data

```python
def aggregate_competitor_prices():
    """Run daily to create market summary for AI training"""
    query = """
    INSERT INTO market_price_summary
        (date, province, camper_type, avg_price, median_price,
         min_price, max_price, sample_size, p25_price, p75_price)
    SELECT
        CURRENT_DATE as date,
        cl.province,
        cl.camper_type,
        AVG(cph.daily_price) as avg_price,
        PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY cph.daily_price) as median_price,
        MIN(cph.daily_price) as min_price,
        MAX(cph.daily_price) as max_price,
        COUNT(*) as sample_size,
        PERCENTILE_CONT(0.25) WITHIN GROUP (ORDER BY cph.daily_price) as p25_price,
        PERCENTILE_CONT(0.75) WITHIN GROUP (ORDER BY cph.daily_price) as p75_price
    FROM competitor_listings cl
    JOIN competitor_price_history cph ON cl.id = cph.listing_id
    WHERE cph.recorded_at >= NOW() - INTERVAL '7 days'
      AND cl.is_active = true
    GROUP BY cl.province, cl.camper_type
    HAVING COUNT(*) >= 5;
    """
```

### New AI Features from Competitor Data

```python
FEATURES = [
    # ... existing internal features ...

    # NEW: Competitor/market features
    'market_median_price',        # Competitor median for same type/region
    'market_avg_price',           # Competitor average
    'price_vs_market',            # Our price / market median (ratio)
    'market_sample_size',         # How much competitor data we have
    'market_price_trend',         # Is market going up or down?
    'position_in_market',         # Percentile (are we cheap or expensive?)
]
```

### What the Model Learns from Competitor Data

```
Pattern 1: Booking Success vs Market Position
├── price_vs_market < 1.0 (below market) → 78% booking rate
├── price_vs_market = 1.0-1.1 (at market) → 65% booking rate
└── price_vs_market > 1.1 (above market) → 45% booking rate

Pattern 2: Optimal Price Position
├── Best conversion: 5-10% below market median
├── Premium campers can be 10-15% above market
└── New listings should start 10% below to gain reviews
```

### Price Suggestion with Market Data

```python
def suggest_price_with_market(camper_id, target_date):
    camper = get_camper(camper_id)
    internal_price = model.predict(internal_features)
    market = get_market_summary(target_date, camper.province, camper.type)

    if market and market.sample_size >= 10:
        # Blend internal prediction with market data
        suggested_price = (
            0.6 * internal_price +      # 60% weight on our model
            0.4 * market.median_price   # 40% weight on market
        )

        # Adjust for camper quality
        if camper.reviews_count < 5:
            target_position = 0.95  # New: 5% below median
        elif camper.rating >= 4.8:
            target_position = 1.08  # Premium: 8% above median
        else:
            target_position = 1.0   # Normal: at market

        return {
            'suggested_price': round(suggested_price, 2),
            'market_median': market.median_price,
            'market_sample_size': market.sample_size,
            'our_position': suggested_price / market.median_price
        }
    else:
        return {'suggested_price': round(internal_price, 2)}
```

### Fleet Owner Dashboard - Market Comparison

```
┌─────────────────────────────────────────────────────────────┐
│  YOUR PRICING vs MARKET                                     │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  Your VW California Ocean (Amsterdam)                       │
│                                                             │
│  Current Price: €120/night                                  │
│  Market Median: €125/night (34 similar campers)             │
│  Your Position: 4% below market ✅                          │
│                                                             │
│  ├────────────────────────────────────────────────┤         │
│  €95        €115       €125       €145        €165          │
│  min        p25      median       p75         max           │
│               ▲                                             │
│             You                                             │
│                                                             │
│  💡 Recommendation:                                          │
│  Your price is competitive. Consider raising to €125        │
│  to match market median. Your 4.9⭐ rating supports this.   │
│                                                             │
│  [Accept €125] [Keep €120] [Set Custom Price]               │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### Competitor Data Summary

| Aspect | Details |
|--------|---------|
| **Data Points per Listing** | ~15-20 fields |
| **Update Frequency** | Weekly (manual) or Daily (automated) |
| **Minimum Sample Size** | 10 competitors per region/type |
| **New Features for AI** | 5-6 market-based features |
| **Model Weight** | 30-40% market data, 60-70% internal |
| **Implementation (Manual)** | ~20 hours |
| **Implementation (Automated)** | ~50-60 hours |
| **Expected Accuracy Improvement** | +5-8% |

---

### 3.4.3 Weather Data

Weather significantly impacts camper rental demand. This section details how to integrate weather data into dynamic pricing.

### Important Limitation: 14-Day Forecast Window

> ⚠️ **Weather forecasts are only reliable for ~14 days ahead.**
>
> Weather-based pricing adjustments should **ONLY** apply when:
> - The booking **start date** is within 14 days from today
> - For bookings further out, use seasonal/historical weather patterns instead

```python
def should_use_weather_forecast(booking_start_date):
    days_until_start = (booking_start_date - date.today()).days

    if days_until_start <= 14:
        return True   # Use real-time forecast
    else:
        return False  # Use historical seasonal patterns
```

### Why Weather Matters for Camper Rentals

```
Weather Impact on Bookings:
├── ☀️ Sunny weekend forecast    → Demand ↑ 20-35%
├── 🌧️ Rainy week ahead         → Demand ↓ 15-25%
├── 🌡️ Heat wave (>28°C)        → High demand, premium pricing
├── ❄️ Cold snap (<5°C)         → Very low demand
└── 🌤️ Mixed weather            → Normal demand
```

**Key insight:** People book campers **3-14 days ahead**, so weather forecasts directly influence last-minute booking decisions!

### Weather Data Sources

| Provider | Free Tier | Paid Tier | Best For |
|----------|-----------|-----------|----------|
| **Open-Meteo** | Unlimited (free) | Free | Budget option ✅ |
| **OpenWeatherMap** | 1,000 calls/day | $40/mo | Good accuracy |
| **WeatherAPI** | 1M calls/month | $9/mo+ | Good alternative |

**Recommendation:** Use **Open-Meteo** - completely free, no API key needed!

### Data to Collect

```
For each region/date, we collect:
├── Basic Weather
│   ├── temperature_high: 24°C
│   ├── temperature_low: 15°C
│   └── weather_code: "partly_cloudy"
│
├── Precipitation
│   ├── rain_probability: 20%
│   └── rain_mm: 2.5
│
├── Sunshine
│   ├── sunshine_hours: 10
│   └── cloud_cover: 35%
│
├── Wind
│   └── wind_speed: 15 km/h
│
└── Derived Score
    └── camping_score: 0.82 (0-1, calculated)
```

### API Integration (Open-Meteo - Free)

```python
import requests
from datetime import date, timedelta

def get_weather_forecast(latitude, longitude, days=14):
    """
    Get weather forecast for a location.
    Free API, no key needed!
    Max reliable forecast: 14 days
    """
    url = "https://api.open-meteo.com/v1/forecast"

    params = {
        "latitude": latitude,
        "longitude": longitude,
        "daily": [
            "temperature_2m_max",
            "temperature_2m_min",
            "precipitation_sum",
            "precipitation_probability_max",
            "sunshine_duration",
            "wind_speed_10m_max",
            "weather_code"
        ],
        "timezone": "Europe/Amsterdam",
        "forecast_days": min(days, 14)  # Max 14 days reliable
    }

    response = requests.get(url, params=params)
    return response.json()
```

### Database Schema

```sql
-- Weather forecasts (updated daily)
CREATE TABLE weather_forecasts (
    id SERIAL PRIMARY KEY,
    region VARCHAR(100) NOT NULL,
    latitude DECIMAL(10, 8),
    longitude DECIMAL(11, 8),
    forecast_date DATE NOT NULL,

    -- Temperature
    temp_high DECIMAL(5, 2),
    temp_low DECIMAL(5, 2),

    -- Precipitation
    rain_probability INT,              -- 0-100%
    rain_mm DECIMAL(5, 2),

    -- Sunshine
    sunshine_hours DECIMAL(4, 2),
    cloud_cover INT,                   -- 0-100%

    -- Wind
    wind_speed DECIMAL(5, 2),          -- km/h

    -- Calculated score
    camping_score DECIMAL(3, 2),       -- 0.00-1.00

    -- Metadata
    fetched_at TIMESTAMP DEFAULT NOW(),

    UNIQUE (region, forecast_date),
    INDEX idx_region_date (region, forecast_date)
);

-- Historical weather averages (for bookings > 14 days out)
CREATE TABLE weather_historical_averages (
    id SERIAL PRIMARY KEY,
    region VARCHAR(100) NOT NULL,
    month INT NOT NULL,                -- 1-12
    day_of_month INT NOT NULL,         -- 1-31

    avg_temp_high DECIMAL(5, 2),
    avg_rain_probability INT,
    avg_sunshine_hours DECIMAL(4, 2),
    avg_camping_score DECIMAL(3, 2),

    -- Based on X years of data
    years_of_data INT,

    UNIQUE (region, month, day_of_month)
);
```

### Calculating Camping Score

```python
def calculate_camping_score(weather):
    """
    Calculate a 0-1 score for how good the weather is for camping.
    1.0 = Perfect camping weather
    0.0 = Terrible camping weather
    """
    score = 1.0

    # Temperature factor (ideal: 18-25°C)
    temp = weather['temp_high']
    if temp < 10:
        score *= 0.3      # Too cold
    elif temp < 15:
        score *= 0.6      # Cool
    elif temp < 18:
        score *= 0.85     # Okay
    elif temp <= 25:
        score *= 1.0      # Perfect!
    elif temp <= 30:
        score *= 0.9      # Warm but okay
    else:
        score *= 0.7      # Too hot

    # Rain factor
    rain_prob = weather['rain_probability']
    if rain_prob < 20:
        score *= 1.0      # Great
    elif rain_prob < 40:
        score *= 0.85     # Acceptable
    elif rain_prob < 60:
        score *= 0.65     # Risky
    else:
        score *= 0.4      # Likely rain

    # Sunshine factor
    sunshine = weather['sunshine_hours']
    if sunshine >= 10:
        score *= 1.0      # Sunny day
    elif sunshine >= 6:
        score *= 0.9      # Decent sun
    elif sunshine >= 3:
        score *= 0.75     # Cloudy
    else:
        score *= 0.6      # Very cloudy

    # Wind factor
    wind = weather['wind_speed']
    if wind < 20:
        score *= 1.0      # Calm
    elif wind < 35:
        score *= 0.85     # Breezy
    elif wind < 50:
        score *= 0.6      # Windy
    else:
        score *= 0.3      # Stormy

    return round(score, 2)
```

### Weather Feature in AI Model (With 14-Day Logic)

```python
def add_weather_features(df):
    """
    Add weather-based features to training/prediction data.
    Uses forecast for ≤14 days, historical averages for >14 days.
    """
    today = date.today()

    for idx, row in df.iterrows():
        booking_date = row['start_date']
        days_ahead = (booking_date - today).days

        if days_ahead <= 14:
            # Use real-time forecast (reliable)
            weather = get_weather_forecast_for_date(
                row['region'],
                booking_date
            )
            df.at[idx, 'weather_source'] = 'forecast'
            df.at[idx, 'weather_confidence'] = 0.9  # High confidence

        else:
            # Use historical averages (less reliable)
            weather = get_historical_weather_average(
                row['region'],
                booking_date.month,
                booking_date.day
            )
            df.at[idx, 'weather_source'] = 'historical'
            df.at[idx, 'weather_confidence'] = 0.5  # Lower confidence

        if weather:
            df.at[idx, 'camping_score'] = weather.camping_score
            df.at[idx, 'temp_high'] = weather.temp_high
            df.at[idx, 'rain_probability'] = weather.rain_probability

    return df
```

### Price Suggestion with Weather (14-Day Window)

```python
def suggest_price_with_weather(camper_id, target_date):
    camper = get_camper(camper_id)
    base_price = model.predict(internal_features)

    days_ahead = (target_date - date.today()).days

    # Only apply weather adjustment for bookings within 14 days
    if days_ahead <= 14:
        weather = get_weather_forecast_for_date(camper.region, target_date)

        if weather:
            if weather.camping_score >= 0.85:
                weather_multiplier = 1.15  # +15%
                reason = "☀️ Excellent camping weather forecast"
            elif weather.camping_score >= 0.7:
                weather_multiplier = 1.05  # +5%
                reason = "🌤️ Good weather expected"
            elif weather.camping_score >= 0.5:
                weather_multiplier = 1.0   # No change
                reason = "🌥️ Mixed weather expected"
            elif weather.camping_score >= 0.3:
                weather_multiplier = 0.92  # -8%
                reason = "🌧️ Rain likely - consider discount"
            else:
                weather_multiplier = 0.85  # -15%
                reason = "⛈️ Poor weather - discount recommended"

            return {
                'suggested_price': round(base_price * weather_multiplier, 2),
                'weather_adjustment': f"{(weather_multiplier - 1) * 100:+.0f}%",
                'camping_score': weather.camping_score,
                'weather_source': 'Real-time forecast',
                'reasoning': reason
            }

    else:
        # Booking is > 14 days out - no weather adjustment
        return {
            'suggested_price': round(base_price, 2),
            'weather_adjustment': 'N/A',
            'weather_source': 'Too far ahead for forecast',
            'reasoning': '📅 Booking is more than 14 days away - weather forecast not available'
        }
```

### What the Model Learns from Weather

| Pattern | What AI Learns |
|---------|----------------|
| **Good Weather → Premium** | camping_score > 0.85 → Can charge 15-25% premium |
| **Bad Weather → Discount** | camping_score < 0.5 → Need 10-20% discount |
| **Last-Minute Surge** | Good forecast 3 days ahead → Spike in bookings |
| **Weather Cancellations** | Bad forecast → Higher cancellation risk |

### Dashboard Display

```
┌─────────────────────────────────────────────────────────────┐
│  WEATHER-BASED PRICING                                      │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  July 15, 2025 - Amsterdam Area (in 5 days)                 │
│                                                             │
│  ┌─────────────────────────────────────────────────────┐    │
│  │  ☀️  24°C   💧 10%   ☀️ 11h sun   💨 12 km/h       │    │
│  │                                                     │    │
│  │  Camping Score: ████████████░░░░ 0.92 (Excellent!)  │    │
│  └─────────────────────────────────────────────────────┘    │
│                                                             │
│  Weather Impact: +15% premium justified                     │
│                                                             │
│  Base Price:      €120/night                                │
│  Weather Boost:   +€18 (excellent weather)                  │
│  Suggested:       €138/night                                │
│                                                             │
│  📈 Similar weather last month: 85% booking rate            │
│                                                             │
│  [Accept €138] [Keep €120] [Set Custom]                     │
│                                                             │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│  BOOKING > 14 DAYS AWAY                                     │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  August 15, 2025 - Amsterdam Area (in 45 days)              │
│                                                             │
│  ⏳ Weather forecast not yet available                       │
│                                                             │
│  Based on historical August averages:                       │
│  • Typical temp: 22°C                                       │
│  • Typical rain chance: 25%                                 │
│  • Historical camping score: 0.78                           │
│                                                             │
│  💡 Weather-based adjustment will apply automatically       │
│     when booking is within 14 days of start date.           │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### 14-Day Calendar View

```
┌─────────────────────────────────────────────────────────────┐
│  WEATHER FORECAST - NEXT 14 DAYS                            │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  Mon   Tue   Wed   Thu   Fri   Sat   Sun                    │
│  ─────────────────────────────────────────                  │
│   ☀️    ☀️    🌤️    🌧️    🌧️    🌤️    ☀️                   │
│  24°   26°   22°   18°   17°   20°   23°                    │
│  0.95  0.92  0.78  0.42  0.38  0.72  0.88                   │
│  +15%  +15%  +5%  -12%  -15%   0%  +10%                     │
│                                                             │
│  Mon   Tue   Wed   Thu   Fri   Sat   Sun                    │
│  ─────────────────────────────────────────                  │
│   🌤️    ☀️    ☀️    🌤️    🌤️    ☀️    ☀️                   │
│  21°   23°   25°   24°   22°   24°   26°                    │
│  0.82  0.88  0.92  0.85  0.80  0.90  0.95                   │
│  +8%  +10%  +15%  +10%  +5%  +12%  +15%                     │
│                                                             │
│  ─────────────────────────────────────────                  │
│  Day 15+: Weather forecast not available                    │
│  Using historical seasonal averages instead                 │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### API Cost Analysis

**For Netherlands (12 regions, 14-day forecast):**

| Scenario | Daily Calls | Monthly Calls | Cost |
|----------|-------------|---------------|------|
| **Daily update** | 12 | 360 | **FREE** (Open-Meteo) |
| **Hourly update** | 288 | 8,640 | **FREE** (Open-Meteo) |

**Open-Meteo is completely free with no API key required!**

### Weather Data Summary

| Aspect | Details |
|--------|---------|
| **Forecast Window** | **14 days maximum** (reliable) |
| **Data Source** | Open-Meteo API (free) |
| **Update Frequency** | Daily at 6 AM |
| **Regions Tracked** | 12 (Dutch provinces) |
| **API Cost** | **$0/month** |
| **New AI Features** | 4-5 weather features |
| **Implementation Time** | ~12 hours |
| **Pricing Impact** | ±15-25% within 14-day window |

### When Weather Pricing Applies

| Booking Window | Weather Data Used | Price Impact |
|----------------|-------------------|--------------|
| **0-7 days ahead** | Real-time forecast (very accurate) | ±15-25% |
| **8-14 days ahead** | Real-time forecast (accurate) | ±10-15% |
| **15-30 days ahead** | Historical averages | ±5% (low confidence) |
| **31+ days ahead** | Seasonal patterns only | No weather adjustment |

---

### 3.4.4 Local Occupancy Data

Local occupancy is one of the most valuable signals for dynamic pricing because it directly measures demand. This section covers both internal (platform) and external (market-wide) occupancy data.

### Internal Occupancy (Platform Data)

Internal occupancy measures how many campers on the Jolly Campers platform are booked in a given region.

#### What is Internal Occupancy?

```
Internal Occupancy = Booked campers in region / Total campers in region

Example:
├── Amsterdam has 50 campers on Jolly Campers
├── This weekend, 42 are booked
└── Internal occupancy = 42/50 = 84% ← HIGH DEMAND SIGNAL!
```

#### Why It's Valuable

```
✅ Easy to implement - We already have all the booking data
✅ Real-time signal - Updates daily with new bookings
✅ Direct demand measure - Not a proxy, actual bookings
✅ No external API needed - Zero cost
✅ High correlation with price - Demand directly affects willingness to pay
✅ Regional granularity - Different regions have different demand
```

#### Database Schema

```sql
-- Pre-calculated daily occupancy by region (for fast queries)
CREATE TABLE regional_occupancy (
    id SERIAL PRIMARY KEY,
    region VARCHAR(100) NOT NULL,
    date DATE NOT NULL,

    -- Occupancy metrics
    total_campers INT,
    booked_campers INT,
    occupancy_rate DECIMAL(5, 4),      -- 0.0000 to 1.0000

    -- Additional context
    available_campers INT,
    blocked_campers INT,               -- Owner-blocked (maintenance, etc.)

    -- Trend
    occupancy_7d_avg DECIMAL(5, 4),    -- Rolling 7-day average
    occupancy_trend VARCHAR(20),       -- 'Rising', 'Falling', 'Stable'

    calculated_at TIMESTAMP DEFAULT NOW(),
    UNIQUE (region, date),
    INDEX idx_region_date (region, date)
);

-- Historical occupancy for training
CREATE TABLE regional_occupancy_history (
    id SERIAL PRIMARY KEY,
    region VARCHAR(100) NOT NULL,
    date DATE NOT NULL,
    occupancy_rate DECIMAL(5, 4),
    year INT,
    month INT,
    day_of_week INT,
    is_holiday BOOLEAN,
    is_school_holiday BOOLEAN,

    UNIQUE (region, date)
);
```

#### Calculation Logic

```python
def calculate_local_occupancy(region, target_date, days_window=7):
    """
    Calculate what % of campers in a region are booked.
    """
    query = """
    SELECT
        COUNT(DISTINCT CASE WHEN a.status = 'Booked' THEN c.id END) as booked_campers,
        COUNT(DISTINCT c.id) as total_campers
    FROM campers c
    LEFT JOIN availability a ON c.id = a.camper_id
        AND a.date BETWEEN %s AND %s
    WHERE c.region = %s
        AND c.is_active = true
    """

    result = execute(query, [target_date, target_date + timedelta(days=days_window), region])

    if result.total_campers > 0:
        return round(result.booked_campers / result.total_campers, 2)
    return None
```

#### Daily Calculation Job

```python
def calculate_daily_regional_occupancy():
    """
    Run daily to pre-calculate occupancy for all regions.
    Schedule: Every day at 6 AM
    """
    regions = get_all_regions()

    for region in regions:
        for days_ahead in range(60):  # Next 60 days
            target_date = date.today() + timedelta(days=days_ahead)

            total = count_active_campers(region)
            booked = count_booked_campers(region, target_date)
            occupancy_rate = booked / total if total > 0 else 0

            # Calculate trend
            yesterday = get_occupancy(region, target_date - timedelta(days=1))
            if occupancy_rate > yesterday + 0.05:
                trend = 'Rising'
            elif occupancy_rate < yesterday - 0.05:
                trend = 'Falling'
            else:
                trend = 'Stable'

            upsert_regional_occupancy(region, target_date, occupancy_rate, trend)
```

#### AI Features from Internal Occupancy

```python
FEATURES = [
    # ... existing features ...

    # Internal occupancy features
    'local_occupancy_rate',           # Current occupancy in region
    'local_occupancy_7d_avg',         # 7-day rolling average
    'local_occupancy_trend',          # Rising/Falling/Stable (encoded)
    'occupancy_vs_historical',        # Current vs historical average
]
```

#### What the Model Learns

| Occupancy Level | Signal | Pricing Action |
|-----------------|--------|----------------|
| **> 85%** | Very high demand | +15-25% premium |
| **70-85%** | Good demand | +5-10% |
| **50-70%** | Normal | Standard pricing |
| **30-50%** | Low demand | -5-10% discount |
| **< 30%** | Very low demand | -15-20% discount |

#### Price Suggestion with Internal Occupancy

```python
def suggest_price_with_occupancy(camper_id, target_date):
    camper = get_camper(camper_id)
    base_price = model.predict(internal_features)

    occupancy = get_regional_occupancy(camper.region, target_date)

    if occupancy:
        rate = occupancy.occupancy_rate
        trend = occupancy.occupancy_trend

        if rate >= 0.85:
            if trend == 'Rising':
                multiplier = 1.25  # +25%
                reason = "🔥 Very high demand (85%+ booked, rising)"
            else:
                multiplier = 1.15  # +15%
                reason = "📈 High demand (85%+ booked)"
        elif rate >= 0.70:
            multiplier = 1.08
            reason = "✅ Good demand (70-85% booked)"
        elif rate >= 0.50:
            multiplier = 1.0
            reason = "➡️ Normal demand (50-70% booked)"
        elif rate >= 0.30:
            multiplier = 0.92
            reason = "📉 Low demand - consider discount"
        else:
            multiplier = 0.85
            reason = "⚠️ Very low demand - discount recommended"

        return {
            'suggested_price': round(base_price * multiplier, 2),
            'occupancy_rate': rate,
            'occupancy_trend': trend,
            'reasoning': reason
        }

    return {'suggested_price': round(base_price, 2)}
```

#### Dashboard Display

```
┌─────────────────────────────────────────────────────────────┐
│  LOCAL DEMAND - AMSTERDAM REGION                            │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  July 15-22, 2025                                           │
│                                                             │
│  ┌─────────────────────────────────────────────────────┐    │
│  │  Regional Occupancy                                 │    │
│  │                                                     │    │
│  │  ████████████████████░░░░░  84%  📈 Rising         │    │
│  │                                                     │    │
│  │  42 of 50 campers booked                           │    │
│  │  Only 8 available in your area!                    │    │
│  └─────────────────────────────────────────────────────┘    │
│                                                             │
│  Demand Signal: 🔥 VERY HIGH                                │
│  Recommended Action: Raise prices +15-20%                   │
│                                                             │
│  [Accept Suggestion] [Keep Current] [Set Custom]            │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

#### Internal Occupancy Summary

| Aspect | Details |
|--------|---------|
| **Data Source** | Internal platform bookings |
| **Calculation** | Daily cron job at 6 AM |
| **Forecast Window** | 60 days ahead |
| **New AI Features** | 4-5 occupancy features |
| **Implementation Time** | ~8-10 hours |
| **Cost** | **$0** |
| **Accuracy Impact** | +8-12% (very valuable!) |

---

### External Occupancy (Market-Wide Data)

External occupancy measures demand across ALL camper rental platforms - not just Jolly Campers. This is harder to obtain because competitors don't share their booking data.

#### The Challenge

```
Internal Occupancy (Easy):
└── We know: "42 of our 50 Amsterdam campers are booked" ✅

External/Market Occupancy (Hard):
└── We want: "How many of ALL campers across ALL platforms are booked?"
    ├── Goboony: ??? booked
    ├── Yescapa: ??? booked
    ├── Camptoo: ??? booked
    └── PaulCamper: ??? booked

Problem: Competitors don't share their booking data!
```

#### What We CAN See (Publicly Available)

Most competitor platforms show availability calendars on their public listing pages:

```
Competitor Listing Page:
┌─────────────────────────────────────────────────────────────┐
│  Availability Calendar:                                     │
│  July 2025                                                  │
│  Mon Tue Wed Thu Fri Sat Sun                                │
│   1   2   3   4   5   6   7                                 │
│   ✓   ✓   ✓   ✗   ✗   ✗   ✗  ← We can see this!           │
│                                                             │
│  ✓ = Available    ✗ = Unavailable (booked or blocked)      │
└─────────────────────────────────────────────────────────────┘
```

**Key limitation:** "Unavailable" could mean:
- Actually booked (real demand)
- Owner-blocked (maintenance, personal use)
- Minimum stay conflict

#### Method 1: Manual Sampling (Recommended for MVP)

```
Process:
1. Identify 50-100 representative competitor listings per region
2. Staff checks availability calendars weekly/monthly
3. Record: "X of 50 sampled listings are unavailable this weekend"
4. Estimate market occupancy from sample

Pros:
✅ Legal (public data)
✅ No technical complexity
✅ Good enough for trends

Cons:
❌ Time-consuming (~2-4 hours/week)
❌ Sample may not be representative
❌ Can't distinguish booked vs blocked
```

**Effort:** ~4 hours/week staff time

#### Method 2: Price Changes as Demand Proxy (Recommended)

Instead of actual bookings, use competitor price changes as a demand signal:

```python
def estimate_market_demand_from_prices(region, target_date):
    """
    If competitors are raising prices, market demand is likely high.
    """
    current_prices = get_competitor_prices(region, target_date)
    prices_4w_ago = get_competitor_prices(region, target_date - timedelta(days=28))

    if current_prices and prices_4w_ago:
        avg_current = sum(current_prices) / len(current_prices)
        avg_4w_ago = sum(prices_4w_ago) / len(prices_4w_ago)

        price_change = (avg_current - avg_4w_ago) / avg_4w_ago

        if price_change > 0.15:
            return "Very High"  # Prices up 15%+
        elif price_change > 0.05:
            return "High"
        elif price_change > -0.05:
            return "Normal"
        elif price_change > -0.15:
            return "Low"
        else:
            return "Very Low"

    return "Unknown"
```

#### Method 3: Calendar Scraping (Not Recommended)

```python
# ⚠️ May violate terms of service - NOT RECOMMENDED

def scrape_competitor_availability(listing_url):
    """Scrape availability calendar from competitor listing."""
    response = requests.get(listing_url)
    # Parse calendar...
    # Legal/ethical concerns!
```

**⚠️ Legal/Ethical Concerns:**
- Most platforms prohibit scraping in Terms of Service
- Could result in IP blocking
- May have legal implications

#### Database Schema for External Estimates

```sql
-- Market occupancy estimates (from sampling/proxies)
CREATE TABLE market_occupancy_estimates (
    id SERIAL PRIMARY KEY,
    region VARCHAR(100) NOT NULL,
    date DATE NOT NULL,

    -- Sample-based estimate
    sample_size INT,
    unavailable_count INT,
    estimated_occupancy DECIMAL(5, 4),

    -- Confidence level
    confidence VARCHAR(20),           -- 'High', 'Medium', 'Low'
    data_source VARCHAR(50),          -- 'Manual Sample', 'Price Proxy'

    collected_at TIMESTAMP DEFAULT NOW(),
    UNIQUE (region, date)
);

-- Market demand indicators (proxy data)
CREATE TABLE market_demand_indicators (
    id SERIAL PRIMARY KEY,
    region VARCHAR(100) NOT NULL,
    date DATE NOT NULL,

    -- Price-based proxy
    competitor_avg_price DECIMAL(10, 2),
    price_change_7d DECIMAL(5, 4),
    price_change_30d DECIMAL(5, 4),

    -- Derived demand level
    demand_level VARCHAR(20),         -- 'Very High', 'High', 'Normal', 'Low'

    calculated_at TIMESTAMP DEFAULT NOW(),
    UNIQUE (region, date)
);
```

#### Combining Internal + External Signals

```python
def get_combined_demand_signal(region, target_date):
    """
    Combine internal occupancy with external demand indicators.
    Weight internal data more heavily (we trust it).
    """
    internal = get_regional_occupancy(region, target_date)
    external_price = get_market_demand_from_prices(region, target_date)
    external_sample = get_market_occupancy_estimate(region, target_date)

    weights = {
        'internal_occupancy': 0.60,    # 60% weight (most reliable)
        'price_proxy': 0.25,           # 25% weight
        'sample_estimate': 0.15        # 15% weight
    }

    signals = []

    if internal:
        signals.append(('internal', internal.occupancy_rate, weights['internal_occupancy']))

    if external_price:
        demand_to_score = {
            'Very High': 0.95, 'High': 0.75, 'Normal': 0.55,
            'Low': 0.35, 'Very Low': 0.15
        }
        signals.append(('price', demand_to_score.get(external_price, 0.5), weights['price_proxy']))

    if external_sample:
        signals.append(('sample', external_sample.estimated_occupancy, weights['sample_estimate']))

    if signals:
        total_weight = sum(s[2] for s in signals)
        combined_score = sum(s[1] * s[2] for s in signals) / total_weight

        return {
            'combined_demand_score': round(combined_score, 2),
            'internal_occupancy': internal.occupancy_rate if internal else None,
            'external_signal': external_price,
            'confidence': 'High' if internal else 'Medium'
        }

    return None
```

#### External Occupancy Summary

| Aspect | Details |
|--------|---------|
| **Data Source** | Competitor calendars / price changes |
| **Accuracy** | Estimated (60-80%) |
| **Cost** | $0 (manual) to $50/month (tools) |
| **Implementation Time** | ~20-40 hours |
| **Legal Risk** | Medium (if scraping) |
| **Recommendation** | Use price proxy instead |

---

### Internal vs External Comparison

| Aspect | Internal Occupancy | External Occupancy |
|--------|-------------------|-------------------|
| **Data Source** | Our bookings | Competitor calendars |
| **Accuracy** | 100% accurate | Estimated (60-80%) |
| **Cost** | $0 | $0-50/month |
| **Implementation** | ~8 hours | ~20-40 hours |
| **Legal Risk** | None | Medium (if scraping) |
| **Value** | Very High | Medium-High |
| **Recommendation** | ✅ Implement first | ⏳ Phase 2 |

### Recommended Implementation Order

```
Priority Order:
1. ✅ Internal occupancy (our platform) - Do this first!
2. ✅ Competitor price changes as proxy - Easy, no legal issues
3. ⏳ Manual sampling (50 listings/region/month) - If needed
4. ❌ Automated scraping - Not recommended (legal risk)
```

**Internal occupancy gives you 80% of the value with 20% of the effort.**

---

### 3.4.5 Events & Festivals (PredictHQ)

Events significantly impact camper rental demand. This section covers automated event data collection using PredictHQ API.

### Why Events Matter for Pricing

```
Event Impact Examples:
├── 🏎️ Formula 1 Dutch GP (Zandvoort)     → +40-60% prices in 100km radius
├── 🎵 Pinkpop Festival (Limburg)          → +30-50% prices nearby
├── 🎭 King's Day (Nationwide)             → +25-35% everywhere
├── ⚽ UEFA Euro matches                   → +20-30% in host cities
├── 🌷 Keukenhof Season (March-May)        → +15-25% in Noord-Holland
└── 🎪 Local town festivals                → +10-20% in immediate area
```

### API Comparison: Why PredictHQ Wins

| Feature | Ticketmaster | Eventbrite | PredictHQ |
|---------|--------------|------------|-----------|
| Event name | ✅ | ✅ | ✅ |
| Date/location | ✅ | ✅ | ✅ |
| **Attendance** | ❌ No | ❌ No | ✅ `phq_attendance` |
| **Importance rank** | ❌ No | ❌ No | ✅ `rank` (0-100) |
| **Multi-day impact** | ❌ No | ❌ No | ✅ `impact_patterns` |
| **Industry-specific** | ❌ No | ❌ No | ✅ `accommodation` vertical |
| **Predicted spend** | ❌ No | ❌ No | ✅ `predicted_event_spend` |
| Manual work needed | Yes (venue DB) | Yes (venue DB) | **None** ✅ |
| Cost | Free | Free | ~$200+/mo |

> **Note:** Ticketmaster and Eventbrite do NOT return event capacity or attendance numbers.
> PredictHQ is the only API that provides this data automatically.

### PredictHQ API Response Example

```json
{
  "title": "Formula 1 Dutch Grand Prix",
  "category": "sports",
  "rank": 85,
  "local_rank": 95,
  "phq_attendance": 305000,

  "impact_patterns": [
    {
      "vertical": "accommodation",
      "impacts": [
        { "date_local": "2025-08-27", "value": 30500, "position": "leading" },
        { "date_local": "2025-08-28", "value": 152500, "position": "leading" },
        { "date_local": "2025-08-29", "value": 244000, "position": "event_day" },
        { "date_local": "2025-08-30", "value": 305000, "position": "event_day" },
        { "date_local": "2025-08-31", "value": 244000, "position": "event_day" },
        { "date_local": "2025-09-01", "value": 91500, "position": "lagging" }
      ]
    }
  ],

  "predicted_event_spend": 45000000,
  "predicted_event_spend_industries": {
    "accommodation": 8500000,
    "hospitality": 22000000,
    "transportation": 14500000
  }
}
```

### Key PredictHQ Fields for Pricing

| Field | Description | Use for Pricing |
|-------|-------------|-----------------|
| `rank` | 0-100 importance score | Higher rank = higher price premium |
| `local_rank` | Local importance | Regional pricing adjustment |
| `phq_attendance` | Predicted attendance | Demand indicator |
| `impact_patterns` | Multi-day impact | Different pricing for leading/event/lagging days |
| `impact_patterns.vertical` | Industry (accommodation, hospitality) | Use `accommodation` for campers |
| `impact_patterns.position` | leading, event_day, lagging | Highest premium on event_day |

### Database Schema for Events

```sql
-- Events from PredictHQ
CREATE TABLE predicthq_events (
    id VARCHAR(50) PRIMARY KEY,
    title VARCHAR(300),
    category VARCHAR(50),
    start_date DATE,
    end_date DATE,
    latitude DECIMAL(10, 8),
    longitude DECIMAL(11, 8),
    rank INT,
    local_rank INT,
    phq_attendance INT,
    predicted_spend_accommodation DECIMAL(12, 2),
    fetched_at TIMESTAMP DEFAULT NOW()
);

-- Pre-calculated daily event impact per region
CREATE TABLE daily_event_impact (
    id SERIAL PRIMARY KEY,
    region VARCHAR(100) NOT NULL,
    date DATE NOT NULL,

    -- Aggregated metrics
    total_events INT,
    max_rank INT,
    max_attendance INT,
    total_attendance INT,
    combined_impact_value INT,

    -- Primary event
    primary_event_id VARCHAR(50),
    primary_event_title VARCHAR(300),

    -- Impact position
    has_event_day BOOLEAN,
    has_leading_day BOOLEAN,
    has_lagging_day BOOLEAN,

    calculated_at TIMESTAMP DEFAULT NOW(),
    UNIQUE (region, date)
);
```

### Fetching Events from PredictHQ

```python
import requests
from datetime import date, timedelta

PREDICTHQ_TOKEN = "YOUR_API_TOKEN"

def fetch_predicthq_events(lat, lng, radius_km=100, days_ahead=90):
    """
    Fetch events from PredictHQ API.
    """
    url = "https://api.predicthq.com/v1/events/"

    headers = {
        "Authorization": f"Bearer {PREDICTHQ_TOKEN}",
        "Accept": "application/json"
    }

    params = {
        "within": f"{radius_km}km@{lat},{lng}",
        "active.gte": date.today().isoformat(),
        "active.lte": (date.today() + timedelta(days=days_ahead)).isoformat(),
        "category": "concerts,festivals,sports,performing-arts,community,conferences",
        "rank.gte": 30,  # Filter out minor events
        "limit": 100
    }

    response = requests.get(url, headers=headers, params=params)
    return response.json().get('results', [])
```

### Daily Event Sync Job

```python
def sync_predicthq_events_daily():
    """
    Daily job: Fetch events and calculate regional impact.
    Run at 6 AM.
    """
    regions = [
        {"name": "Noord-Holland", "lat": 52.5206, "lng": 4.7885},
        {"name": "Zuid-Holland", "lat": 52.0116, "lng": 4.3571},
        {"name": "Noord-Brabant", "lat": 51.4826, "lng": 5.2321},
        # ... all EU + UK regions
    ]

    for region in regions:
        events = fetch_predicthq_events(region['lat'], region['lng'])

        for event in events:
            # Store event
            upsert_event(event)

            # Extract and store daily impact values
            for pattern in event.get('impact_patterns', []):
                if pattern['vertical'] == 'accommodation':
                    for impact in pattern['impacts']:
                        upsert_daily_impact(
                            region=region['name'],
                            date=impact['date_local'],
                            event=event,
                            impact_value=impact['value'],
                            position=impact['position']
                        )
```

### AI Features from Event Data

```python
EVENT_FEATURES = [
    'event_max_rank',              # Highest event rank (0-100)
    'event_total_attendance',      # Sum of all event attendance
    'event_combined_impact',       # PHQ combined impact value
    'event_count',                 # Number of events on this date
    'has_event_day',               # Binary: Is it event day?
    'has_leading_day',             # Binary: Is it leading day?
    'has_lagging_day',             # Binary: Is it lagging day?
    'days_to_nearest_major_event', # Days until next rank >= 70 event
]

def add_event_features(df):
    """Add event features to training dataset."""
    for idx, row in df.iterrows():
        region = row['region']
        booking_date = row['start_date']

        impact = get_daily_event_impact(region, booking_date)

        if impact:
            df.at[idx, 'event_max_rank'] = impact['max_rank']
            df.at[idx, 'event_total_attendance'] = impact['total_attendance']
            df.at[idx, 'event_combined_impact'] = impact['combined_impact_value']
            df.at[idx, 'event_count'] = impact['total_events']
            df.at[idx, 'has_event_day'] = 1 if impact['has_event_day'] else 0
            df.at[idx, 'has_leading_day'] = 1 if impact['has_leading_day'] else 0
            df.at[idx, 'has_lagging_day'] = 1 if impact['has_lagging_day'] else 0
        else:
            # No events - set defaults
            df.at[idx, 'event_max_rank'] = 0
            df.at[idx, 'event_total_attendance'] = 0
            df.at[idx, 'event_combined_impact'] = 0
            df.at[idx, 'event_count'] = 0
            df.at[idx, 'has_event_day'] = 0
            df.at[idx, 'has_leading_day'] = 0
            df.at[idx, 'has_lagging_day'] = 0

    return df
```

### Training Data Example with Event Features

| daily_price | region | month | **event_max_rank** | **event_attendance** | **has_event_day** | **position** | booked? |
|-------------|--------|-------|-------------------|---------------------|-------------------|--------------|---------|
| €165 | Noord-Holland | 8 | **85** | **305000** | **1** | event_day | ✅ Yes |
| €145 | Noord-Holland | 8 | **85** | **150000** | **0** | leading | ✅ Yes |
| €95 | Zuid-Holland | 2 | **0** | **0** | **0** | - | ✅ Yes |
| €160 | Limburg | 6 | **72** | **60000** | **1** | event_day | ✅ Yes |

### What the Model Learns from Events

```
Pattern 1: High Rank Events → Higher Prices
├── event_max_rank >= 80 → +25-40% price premium
├── event_max_rank >= 60 → +15-25% premium
├── event_max_rank >= 40 → +10-15% premium
└── event_max_rank < 40 → +5% or less

Pattern 2: Event Position Matters!
├── has_event_day = 1    → Highest premium (+30%)
├── has_leading_day = 1  → Good premium (+20%)
└── has_lagging_day = 1  → Moderate premium (+10%)

Pattern 3: Attendance Correlates with Demand
├── attendance > 50,000  → Very high demand
├── attendance > 20,000  → High demand
├── attendance > 5,000   → Moderate demand
└── attendance < 5,000   → Low impact

Pattern 4: Multiple Events Compound
├── event_count >= 3     → Extra +5-10% premium
└── Total attendance more important than count
```

### Price Suggestion with Event Impact

```python
def suggest_price_with_events(camper_id, target_date):
    camper = get_camper(camper_id)
    base_price = model.predict(internal_features)

    event_impact = get_daily_event_impact(camper.region, target_date)

    explanation = []
    if event_impact and event_impact['total_events'] > 0:
        primary = event_impact.get('primary_event')

        if event_impact['has_event_day']:
            explanation.append(f"🔥 Event day: {primary['title']} (rank {event_impact['max_rank']})")
        elif event_impact['has_leading_day']:
            explanation.append(f"📈 People arriving for: {primary['title']}")
        elif event_impact['has_lagging_day']:
            explanation.append(f"📉 People leaving: {primary['title']}")

        if event_impact['total_attendance']:
            explanation.append(f"👥 Expected attendance: {event_impact['total_attendance']:,}")

    return {
        'suggested_price': round(base_price, 2),
        'event_impact': {
            'events': event_impact.get('total_events', 0),
            'max_rank': event_impact.get('max_rank', 0),
            'attendance': event_impact.get('total_attendance', 0),
            'is_event_day': event_impact.get('has_event_day', False),
            'primary_event': event_impact.get('primary_event', {}).get('title')
        },
        'explanation': explanation
    }
```

### Dashboard: Event Impact Calendar

```
┌─────────────────────────────────────────────────────────────┐
│  EVENT IMPACT CALENDAR - AMSTERDAM AREA                     │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  August 2025                                                │
│  Mon   Tue   Wed   Thu   Fri   Sat   Sun                    │
│  ─────────────────────────────────────────                  │
│  25    26    27    28    29    30    31                     │
│   ·    📈    📈    📈    🔥    🔥    🔥   ← F1 Zandvoort   │
│        +15%  +20%  +25%  +40%  +40%  +35%                   │
│                                                             │
│  Legend:                                                    │
│  🔥 Event Day (+30-40%)                                     │
│  📈 Leading Day (+15-25%)                                   │
│  📉 Lagging Day (+10-15%)                                   │
│  · Normal                                                   │
│                                                             │
│  🏎️ Formula 1 Dutch Grand Prix                              │
│  📅 Aug 29-31, 2025                                         │
│  👥 305,000 expected attendance                             │
│  📊 Rank: 85/100                                            │
│  💰 Accommodation spend: €8.5M                              │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### Free Alternative: Holidays + Known Events

If PredictHQ budget is not available, use free APIs for partial coverage:

```python
# Free option: Public holidays (Nager.Date API - no key needed)
def get_public_holidays(country_code, year):
    url = f"https://date.nager.at/api/v3/PublicHolidays/{year}/{country_code}"
    response = requests.get(url)
    return response.json()

# Supported countries: NL, GB, DE, FR, BE, ES, IT, AT, CH, etc.

# Plus hard-coded major recurring events
MAJOR_RECURRING_EVENTS = [
    {"name": "Formula 1 Dutch GP", "month": 8, "impact": 40, "region": "Noord-Holland"},
    {"name": "Pinkpop Festival", "month": 6, "impact": 30, "region": "Limburg"},
    {"name": "Glastonbury", "month": 6, "impact": 40, "region": "Somerset"},
    {"name": "British Grand Prix", "month": 7, "impact": 40, "region": "Northamptonshire"},
]
```

### Events Data Summary

| Approach | Cost | Coverage | Manual Work | Accuracy |
|----------|------|----------|-------------|----------|
| **PredictHQ** | ~$200-500/mo | 95%+ | None ✅ | Very High |
| **Free APIs + Known Events** | $0 | ~60-70% | Low | Medium |
| **Manual only** | $0 | Variable | High | Medium |

### Recommended Implementation

```
Phase 1 (MVP - Free):
├── Public holidays API (Nager.Date)        ~4 hours
├── School holidays (hard-coded)            ~2 hours
├── Major recurring events (hard-coded)     ~2 hours
└── Total: ~8 hours, $0/month

Phase 2 (Enhanced - If Budget Allows):
├── PredictHQ integration                   ~12 hours
├── Daily sync job                          ~4 hours
├── Event impact calendar UI                ~8 hours
└── Total: ~24 hours, ~$200-500/month
```

**PredictHQ ROI:** If even 2-3 bookings per month benefit from better event-based pricing, the subscription pays for itself.

---

# 4. Implementation & Deployment

---

## 4.1 Implementation Phases

### Phase 1: Foundation (Weeks 1-3)

**Goal:** Set up core infrastructure and owner controls

**Tasks:**
1. Database schema setup (pricing_settings, price_history, locked_dates)
2. API endpoints for owner settings (CRUD)
3. Frontend: Pricing settings page
4. Rule-based pricing engine (baseline)
5. Min/max validation and clamping logic
6. Manual price history tracking

**Deliverables:**
- Owners can set min/max prices
- Rule-based pricing works
- Price history is tracked

### Phase 2: Rule-Based Engine (Weeks 4-5)

**Goal:** Implement sophisticated rule-based pricing

**Tasks:**
1. Seasonal pricing rules configuration
2. Region-based adjustments
3. Amenity-based modifiers
4. Weekend/holiday uplifts
5. Last-minute and long-term discount rules
6. Frontend: Rule configuration UI

**Deliverables:**
- Fully functional rule-based pricing
- Owners can configure custom rules
- Automated price updates (daily cron)

### Phase 3: Market Data Integration (Week 6)

**Goal:** Collect and display market data

**Tasks:**
1. Market data table setup
2. Admin UI for manual market data entry
3. Event calendar setup and UI
4. Display market median on dashboard
5. Basic competitor comparison

**Deliverables:**
- Admin can input market data
- Event calendar operational
- Market median displayed to owners

### Phase 4: AI Model MVP (Weeks 7-10)

**Goal:** Train and deploy initial ML model

**Tasks:**
1. Set up Python Flask service
2. Data extraction and feature engineering
3. Train initial XGBoost model
4. Model storage setup (S3/GCS)
5. Prediction API endpoint
6. Model versioning
7. Cold start logic

**Deliverables:**
- Working ML model (even if limited data)
- Prediction service operational
- Model can be retrained

### Phase 5: Hybrid Integration (Weeks 11-12)

**Goal:** Combine rules + AI + owner controls

**Tasks:**
1. Price suggestion engine (combine rule + AI)
2. Apply pricing mode multipliers
3. Generate adjustment reasons
4. Confidence score calculation
5. API endpoints for suggestions
6. Accept/reject logic

**Deliverables:**
- Owners receive AI price suggestions
- Suggestions respect all constraints
- Accept/reject workflow functional

### Phase 6: Fleet Owner Dashboard (Weeks 13-15)

**Goal:** Build comprehensive pricing dashboard

**Tasks:**
1. Price suggestion panel with charts
2. Comparison view (current vs suggested vs market)
3. Adjustment factors display
4. Price history timeline
5. Performance metrics widgets
6. Lock date calendar picker
7. Mobile-responsive design

**Deliverables:**
- Full-featured pricing dashboard
- Intuitive accept/reject UX
- Performance tracking visible

### Phase 7: Super Admin Dashboard (Week 16)

**Goal:** Admin oversight and control

**Tasks:**
1. Global AI toggle
2. Platform-wide performance dashboard
3. Outlier detection
4. Regional/camper-type controls
5. Alert system for anomalies

**Deliverables:**
- Super admin can monitor AI performance
- Global controls operational
- Outlier alerts working

### Phase 8: Monitoring & Retraining (Weeks 17-18)

**Goal:** Set up continuous learning

**Tasks:**
1. Booking data collection pipeline
2. Weekly retraining cron job
3. Model performance validation
4. Automated model deployment
5. Monitoring dashboards (Grafana)
6. Logging and alerts

**Deliverables:**
- Automated weekly retraining
- Performance monitoring active
- Admin notified of retraining results

### Phase 9: Testing & Refinement (Weeks 19-20)

**Goal:** Comprehensive testing and optimization

**Tasks:**
1. Unit tests for pricing logic
2. Integration tests for APIs
3. ML model validation
4. Performance testing
5. UI/UX testing
6. Bug fixes and refinements

**Deliverables:**
- All tests passing
- System ready for production
- Documentation complete

---

# 5. Operations & Governance

---

## 5.1 Technical Considerations

### Scalability

**Current Design:**
- Handles 1,000+ campers
- Daily batch updates (not real-time)
- Model inference cached (Redis)
- Prediction latency < 200ms

**Future Scaling:**
- Horizontal scaling of API servers
- Read replicas for database
- Model serving via dedicated ML platform (TensorFlow Serving, MLflow)
- Distributed training (Apache Spark)

### Performance Optimization

1. **Caching Strategy:**
   - Cache price suggestions for 24 hours
   - Cache market data for 1 week
   - Cache model predictions by feature hash

2. **Database Optimization:**
   - Index on camper_id, date columns
   - Materialized view for ML training data
   - Partition price_history by month

3. **Model Optimization:**
   - Load model once at service startup
   - Batch predictions when possible
   - Feature pre-computation (cache occupancy rates)

### Error Handling

**Fallback Hierarchy:**
1. AI service unavailable → Use rule-based pricing
2. Market data unavailable → Use historical averages
3. Model prediction fails → Use last known good price
4. Database connection fails → Retry with exponential backoff

**Error Logging:**
- All pricing decisions logged
- Failed predictions logged with context
- Model performance anomalies alerted

### Data Quality

**Validation Rules:**
- Price suggestions must be within owner min/max
- Confidence score between 0-1
- Market data sample size > 10
- Training data excludes outliers (>3 std dev)

**Monitoring:**
- Track acceptance rate by confidence score
- Alert if acceptance rate drops < 40%
- Monitor model drift (RMSE increase)

---

## 5.2 Security & Compliance

### Access Control

**Role-Based Permissions:**
- **Fleet Owner:** Can view/edit own camper pricing settings only
- **Super Admin:** Can view all data, toggle AI globally, input market data
- **System:** Can execute automated price updates

### Data Privacy

- No PII in ML training data
- Aggregate data only for market insights
- GDPR-compliant data retention (delete old price history after 2 years)

### Audit Trail

- All price changes logged with timestamp, user, reason
- All AI suggestions logged (accepted/rejected)
- All model retraining events logged
- All admin actions logged

### API Security

- JWT authentication on all endpoints
- Rate limiting (100 requests/min per user)
- Input validation and sanitization
- SQL injection prevention (parameterized queries)

---

## 5.3 Monitoring & Observability

### Key Metrics

**Business Metrics:**
- AI suggestion acceptance rate
- Average occupancy (AI vs manual periods)
- Revenue lift from AI pricing
- Owner satisfaction (survey data)

**Technical Metrics:**
- Prediction latency (p50, p95, p99)
- Model accuracy (RMSE, MAE)
- API response times
- Error rates by endpoint

**ML Metrics:**
- Model drift detection (data distribution changes)
- Prediction confidence distribution
- Feature importance changes
- Retraining frequency and success rate

### Dashboards

**Grafana Dashboards:**
1. **Business Dashboard:**
   - Acceptance rate over time
   - Revenue comparison
   - Occupancy trends
   - Top performing regions

2. **Technical Dashboard:**
   - API latency heatmap
   - Error rate by endpoint
   - Database query performance
   - Cache hit rates

3. **ML Dashboard:**
   - Model accuracy over time
   - Prediction distribution
   - Feature importance
   - Training job status

### Alerts

**Critical Alerts:**
- AI service down (PagerDuty)
- Model accuracy drops > 20% (Email + Slack)
- Acceptance rate < 30% (Slack)
- Database connection failed (PagerDuty)

**Warning Alerts:**
- Model retraining failed (Slack)
- API latency > 500ms (Slack)
- Unusual pricing pattern detected (Email)

---

## 5.4 Deployment Plan

### Infrastructure Requirements

**Compute:**
- API Server: 2 vCPUs, 4GB RAM (auto-scale 1-5 instances)
- AI Service: 2 vCPUs, 8GB RAM (1-2 instances)
- Database: PostgreSQL (4 vCPUs, 16GB RAM, 100GB storage)
- Redis: 2GB RAM
- Model Storage: 10GB S3/GCS

**Estimated Monthly Cost:**
- Compute: $150-200
- Database: $100-150
- Storage: $5-10
- **Total: ~$300/month**

### Deployment Steps

1. **Pre-deployment:**
   - Set up cloud infrastructure (AWS/GCP)
   - Configure database and Redis
   - Set up model storage bucket
   - Deploy monitoring stack

2. **Initial Deployment:**
   - Deploy API server (Node.js)
   - Deploy AI service (Python Flask)
   - Run database migrations
   - Load initial model (if available)

3. **Data Seeding:**
   - Import historical booking data
   - Seed pricing settings for existing owners
   - Import any existing market data

4. **Soft Launch:**
   - Enable for 10% of fleet owners (pilot group)
   - Monitor closely for 2 weeks
   - Gather feedback

5. **Full Rollout:**
   - Enable for all fleet owners
   - Announce feature via email/blog
   - Provide documentation and support

### Rollback Plan

If issues arise:
1. Toggle AI pricing off globally (super admin dashboard)
2. System falls back to rule-based pricing
3. Investigate and fix issues
4. Re-enable gradually

---

# 6. Planning & Success

---

## 6.1 Future Enhancements

### Phase 2 Features (3-6 months post-launch)

1. **External Data Integration:**
   - Weather API (OpenWeatherMap)
   - Automated competitor scraping (Scrapy)
   - Fuel price API
   - Event APIs (Eventbrite, local tourism)

2. **Advanced ML:**
   - Reinforcement learning (learn from acceptance feedback)
   - Multi-model ensemble
   - Deep learning (LSTM for time series)

3. **Advanced UI:**
   - Bulk pricing operations
   - Auto-accept rules
   - Mobile app integration
   - Push notifications for suggestions

4. **Personalization:**
   - Pricing per renter segment (new vs repeat customers)
   - Loyalty discounts
   - Custom pricing rules per date range

### Phase 3 Features (6-12 months post-launch)

1. **AI Explainability:**
   - SHAP values for feature importance
   - "Why did price change?" detailed explanations
   - Counterfactual explanations

2. **A/B Testing:**
   - Test different pricing strategies
   - Measure impact of specific factors
   - Optimize pricing modes

3. **Dynamic Discounts:**
   - Last-minute discounts (48-hour rule)
   - Long-term rental optimization
   - Package deals with add-ons

4. **Market Intelligence:**
   - Competitor monitoring dashboard
   - Market trend reports
   - Demand forecasting

---

## 6.2 Success Criteria

### MVP Success Metrics (3 months post-launch)

- ✅ **Adoption Rate:** 60%+ of fleet owners enable AI pricing
- ✅ **Acceptance Rate:** 50%+ of AI suggestions accepted
- ✅ **Occupancy Lift:** +5% average occupancy for AI users
- ✅ **Revenue Lift:** +10% revenue for AI users
- ✅ **System Uptime:** 99.5% uptime
- ✅ **Model Accuracy:** RMSE < 15% of mean price

### Long-Term Success Metrics (12 months post-launch)

- ✅ **Adoption Rate:** 80%+ of fleet owners
- ✅ **Acceptance Rate:** 70%+ of suggestions
- ✅ **Occupancy Lift:** +10% average occupancy
- ✅ **Revenue Lift:** +20% revenue
- ✅ **Owner Satisfaction:** 4.0+ / 5.0 rating
- ✅ **Model Accuracy:** RMSE < 10% of mean price

---

## 6.3 Risks & Mitigation

| Risk | Impact | Probability | Mitigation |
|------|--------|-------------|------------|
| Insufficient training data | High | Medium | Cold start strategy, rule-based fallback |
| Low acceptance rate | High | Medium | Clear explanations, conservative initial suggestions |
| Model drift over time | Medium | High | Weekly retraining, monitoring alerts |
| AI service downtime | Medium | Low | Fallback to rule-based, 99.9% SLA target |
| Owner distrust of AI | Medium | Medium | Transparency, opt-in approach, manual overrides |
| Competitor price manipulation | Low | Low | Manual review of market data, outlier detection |
| Regulatory changes (pricing) | Medium | Low | Legal review, flexible rule engine |

---

## Conclusion

This architecture and development plan provides a comprehensive roadmap for implementing the Hybrid Dynamic Pricing System. The phased approach allows for:

1. **Early value delivery** (rule-based pricing in weeks 1-5)
2. **Risk mitigation** (gradual AI introduction with fallbacks)
3. **Owner trust** (full transparency and control)
4. **Continuous improvement** (learning from real data over time)

The system is designed to be:
- **Scalable** (handles growth to thousands of campers)
- **Reliable** (fallback mechanisms at every layer)
- **Maintainable** (clear separation of concerns, comprehensive monitoring)
- **Extensible** (easy to add new features and data sources)

With a total estimated development time of **186 hours** spread over **18-20 weeks**, this system will provide significant value to both fleet owners (increased revenue, reduced manual work) and the platform (increased bookings, competitive advantage).




