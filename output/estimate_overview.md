# Jolly Campers V3 - Project Estimate Overview

**Last Updated:** November 24, 2025

---

## Summary

This document provides a comprehensive cost estimate for the Jolly Campers V3 project, including development, design, quality assurance, project management, and business analysis.

---

## Detailed Breakdown

| Item | Hours | Rate ($/hr) | Amount |
|------|-------|-------------|---------|
| Development | 1,487 | $45 | $66,915.00 |
| Development - KPI | 360 | $45 | $16,200.00 |
| Development - Notification | 246 | $45 | $11,070.00 |
| Development - Digital Handover | 277 | $45 | $12,465.00 |
| Development - Dynamic Pricing (Phase 1) | 54 | $45 | $2,430.00 |
| Design (10%) | 242 | $40 | $9,680.00 |
| QA (15%) | 364 | $40 | $14,560.00 |
| Management (15%) | 364 | $40 | $14,560.00 |
| Business Analyst (10%) | 242 | $50 | $12,100.00 |

**Sub Total:** $159,980.00

**Risk Buffer (20%):** $31,996.00

**Grand Total:** $191,976.00

---

## Hours Breakdown

### Development Hours: 2,424

- **Core Development:** 1,487 hours
- **KPI Development:** 360 hours
- **Notification Development:** 246 hours
- **Digital Handover Development:** 277 hours
- **Dynamic Pricing Development (Phase 1):** 54 hours (additional items only - rules/pricing already in Core)

### Supporting Roles

Based on total development hours (2,424):

- **Design (10%):** 242 hours
- **QA (15%):** 364 hours
- **Project Management (15%):** 364 hours
- **Business Analysis (10%):** 242 hours

**Total Project Hours:** 3,636 hours

---

## Cost by Role

| Role | Hours | Rate ($/hr) | Total |
|------|-------|-------------|-------|
| Developer | 2,424 | $45 | $109,080.00 |
| Designer | 242 | $40 | $9,680.00 |
| QA Engineer | 364 | $40 | $14,560.00 |
| Project Manager | 364 | $40 | $14,560.00 |
| Business Analyst | 242 | $50 | $12,100.00 |

**Subtotal:** $159,980.00

---

## Risk Management

A **20% risk buffer** ($31,996.00) has been included to account for:

- Unforeseen technical challenges
- Scope clarifications and changes
- Integration complexities
- Third-party service dependencies
- Testing and bug fixes beyond standard QA

---

## Project Components

### 1. Core Development (1,487 hours)

Main application development including:
- DevOps & Infrastructure
- System Services (Image, Language, Price, Availability)
- Payment Integration
- Public Webpage
- User Account & Dashboard
- Fleet Admin Panel
- Admin Panel
- Messaging & Communication
- Reviews & Ratings
- AI Chat Assistance
- And more (see detailed breakdown in estimate_development.md)

### 2. KPI Development (360 hours)

Analytics and reporting dashboard including:
- Admin Dashboard with comprehensive analytics
- Fleet Owner Dashboard
- Data tracking and reporting capabilities
- Export and filtering features
- See detailed breakdown in estimate_kpi_development.md

### 3. Notification Development (246 hours)

Comprehensive notification system including:
- In-App Notification System (59 hours)
- Email Service (9 hours)
- Super Admin Notifications (61 hours)
- Fleet Owner Notifications (69 hours)
- Traveler Notifications (48 hours)
- See detailed breakdown in estimate_notification_development.md

### 4. Digital Handover Development (277 hours)

Digital pickup and return process including:
- Pickup Process/Digital Check-In (120 hours)
  - Interactive vehicle diagrams with damage marking
  - Circle-based marking system with mobile/tablet support
  - Touch gesture handling (pinch-to-zoom, tap, drag)
  - Mobile camera integration and image optimization
  - Multiple photo uploads for vehicle
  - Notes per mark
  - Equipment confirmation with dynamic additional items and cost tracking
  - Dual digital signatures (owner and traveller) with mobile optimization
  - Comprehensive form review with all captured data
- Return Process/Digital Check-Out (86 hours)
  - Vehicle verification with pickup comparison (odometer, fuel, battery, water, waste)
  - Automatic calculation of distance, charges, and warnings
  - Load and display pickup marks
  - Add new marks for new damage
  - Comparison view with toggle between pickup, return, and combined views
  - Visual diffing and photo comparison galleries
  - Agreement compliance with dynamic cost management
  - Post-return receipt upload capability
  - Advanced deposit resolution (full/partial refund, additional charges, hold)
  - Complex payment integration with multiple scenarios
  - Comprehensive final review with complete before/after comparison and financial summary
- Digital Document Generation (20 hours)
- Data Model & Backend (56 hours)
  - Comprehensive database schema with 6+ tables and complex relationships
  - 14+ API endpoints with full validation, authentication, business logic
  - File upload service with image optimization
  - Form state management
- Frontend UI/UX (42 hours)
- Notifications Integration (6 hours)
- Multi-Language Support - EN & NL (7 hours)
- See detailed breakdown in estimate_digital_handover_development.md

<span style="color: red;">Note: Some features require clarification with client (see detailed estimate)</span>

### 5. Dynamic Pricing Development - Phase 1 (54 hours)

<span style="color: blue;">**Note:** Manual pricing rules (54h) and quote calculation (20h) are already in Core Development → Rules Management & Price Service. This section only includes **additional** dynamic pricing features.</span>

**Base Settings (10 hours)** - NEW
- Standard price, min/max boundaries per camper
- Pricing mode toggle (Manual/Auto - Auto disabled in Phase 1)

**Additional Pricing Logic (6 hours)** - NEW
- Min/max clamping in rule engine
- Discount stacking logic
- Three-step integration, price history tracking

**Scraper Base Infrastructure (20 hours)** - NEW
- Search strategy: 3-night duration, 2-8 weeks lead time (4 samples)
- Price normalization across platforms (cleaning/service fees)
- Module interface (supports HTTP and browser-based scrapers), scheduler, alerting
- Per-source competitor scrapers estimated separately (8-24h+ each depending on complexity)

**Dashboard UI (10 hours)** - NEW
- Pricing overview and settings
- Price calculator/preview
- Price history view

**Already in Core Development (not counted here):**
- Rules Management (54h): Seasonal, day-of-week, duration-based pricing
- Price Service (20h): Quote calculation with rules

See detailed breakdown in estimate_dynamic_pricing.md

<span style="color: red;">**Note:** Phase 1 focuses on Manual mode + competitor scraper infrastructure (time-sensitive data). Per-source scraper hours vary significantly (8-24h+) based on: URL parameter support, need for browser automation, price breakdown location, and anti-bot measures. Weather and events data collection (~40 hours) deferred to Phase 2 since historical data is available. AI/Auto mode (~118 hours total) starts after 6+ months of competitor data collection.</span>

### 6. Design (256 hours)

UI/UX design for all application components at 10% of development effort

### 7. Quality Assurance (384 hours)

Testing and quality assurance at 15% of development effort, including:
- Functional testing
- Integration testing
- User acceptance testing
- Performance testing
- Security testing

### 8. Project Management (384 hours)

Project coordination and management at 15% of development effort, including:
- Sprint planning and coordination
- Stakeholder communication
- Progress tracking and reporting
- Risk management
- Team coordination

### 9. Business Analysis (256 hours)

Requirements analysis and documentation at 10% of development effort, including:
- Requirements gathering and clarification
- User story refinement
- Process documentation
- Stakeholder workshops
- Acceptance criteria definition

---

## Payment Terms & Schedule

**Note:** Payment terms and milestone schedule to be defined based on project timeline and deliverables.

Typical structure:
- Initial deposit upon contract signing
- Milestone-based payments throughout development
- Final payment upon project completion and acceptance

---

## Assumptions

This estimate is based on the following assumptions:

1. Requirements are clearly defined in the accompanying detailed estimate documents
2. Client provides timely feedback and approvals
3. All third-party services (Stripe, Twilio, Google Maps, etc.) are accessible and functional
4. Development environment and tools are readily available
5. No major scope changes beyond items marked for clarification
6. Standard working hours and availability of team members
7. Items marked with red flags ("To clarify with client") will be clarified before development begins

---

## Exclusions

The following are **NOT** included in this estimate:

- Third-party service fees (Stripe, Twilio, SendGrid, Mailchimp, hosting, etc.)
- Infrastructure costs (servers, databases, CDN, storage)
- Domain registration and SSL certificates
- Ongoing maintenance and support after project completion
- Training and documentation beyond development handover
- Features marked as "Future Phase" or "TBD" in detailed estimates
- Advanced Digital Handover features (Offline Mode, AI Photo Tagging, GPS Tracking, Smart Comparison, Signature Verification) - these are future phase scope and not included in current estimate
- Dynamic Pricing Phase 2 (AI/Auto mode) - ~102 hours including:
  - AI model development and training (40 hours)
  - Event data integration with PredictHQ (24 hours)
  - Auto mode dashboard with AI suggestions (20 hours)
  - Monitoring and model retraining workflow (18 hours)
- ML infrastructure setup (if not already available) - would add 15-20 hours
- Advanced AI features (reinforcement learning, personalized pricing, AI explainability)
- Additional languages beyond English and Dutch (if required)
- Mobile native applications (iOS/Android apps)

---

## Next Steps

1. Review detailed estimates:
   - estimate_development.md
   - estimate_kpi_development.md
   - estimate_notification_development.md
   - estimate_digital_handover_development.md
   - estimate_dynamic_pricing.md

2. Clarify items marked with red flags in the detailed estimates

3. Finalize scope and priorities

4. Define project timeline and milestones

5. Agree on payment terms and schedule

6. Begin development upon contract signing

---

## Notes

- All estimates are in USD
- Hours are approximate and may vary based on actual implementation complexity
- The 20% risk buffer provides flexibility for unforeseen challenges
- Regular progress reviews will be conducted to ensure alignment with estimates
- Any significant scope changes will require a change request and re-estimation

---

## Contact

For questions or clarifications regarding this estimate, please contact the project team.

