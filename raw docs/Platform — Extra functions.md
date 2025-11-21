# **Platform \- KPI dashboard development**

## **Purpose**

To build a **live KPI dashboard** integrated into the new **Platform** booking system.  
 The dashboard will give **Super Admins** and **Fleet Owners** a clear, real-time overview of:

* Fleet performance

* Bookings

* Finances

* Customer communication and satisfaction

* Growth and partner activity

---

## **Technical overview**

### **Data Sources**

* Platform database (bookings, users, vehicles, payouts)

* Google Analytics (website traffic, conversion)

* Payment system (Stripe, Mollie, etc.)

* Marketing platforms (Meta Ads, Google Ads)

* Review/feedback data (internal or external forms)

* Messaging data (in-app communication system)

### **Dashboard Tool Options**

* **Preferred:** Native admin dashboard (React or Vue front-end, API-fed data)

### **Update Frequency**

* Booking and financial metrics → daily

* Marketing and growth metrics → weekly to monthly

---

## **USER ROLES & DASHBOARD ACCESS**

There are **two user dashboards**:

1. **Super Admin Dashboard**

2. **Fleet Owner Dashboard**

---

## **1\. SUPER ADMIN DASHBOARD**

**Purpose:**  
 To provide Michael and co-founders with **complete visibility** into platform performance — including bookings, fleet utilisation, finances, customer experience, and communication.

**Access:**  
 Full visibility across all data. Can export, generate reports, and drill down by owner, camper, or timeframe.

---

### **A. Fleet & Booking Performance**

| KPI | Description | Formula | Data Source |
| ----- | ----- | ----- | ----- |
| Occupancy Rate (%) | % of total available nights booked | (Booked nights ÷ Total available nights) × 100 | Bookings |
| Average Booking Length (days) | Avg. nights per booking | Total booked nights ÷ Total bookings | Bookings |
| Utilization per Camper | Days rented per camper | SUM(booked\_days) GROUP BY vehicle\_id | Bookings \+ Vehicles |
| Revenue per Camper (€) | Income per vehicle | SUM(total\_price) GROUP BY vehicle\_id | Transactions |
| Cancellation Rate (%) | % of cancelled bookings | (Cancelled ÷ Confirmed) × 100 | Bookings |
| Lead Time (days) | Avg. time between booking and pickup | AVG(pickup\_date \- booking\_date) | Bookings |
| Repeat Booking Rate (%) | Returning customers ratio | (Returning renters ÷ Total renters) × 100 | Users \+ Bookings |

---

### **B. Financial & Marketing Performance**

| KPI | Description | Formula | Data Source |
| ----- | ----- | ----- | ----- |
| Gross Booking Value (GBV) | Total income before fees | SUM(total\_booking\_amount) | Transactions |
| Commission Revenue (€) | Platform earnings | SUM(total\_booking\_amount × commission\_rate) | Transactions |
| Owner Payout (€) | Total payouts to owners | SUM(owner\_payouts) | Payouts |
| Average Daily Rate (ADR) | Avg. daily rental rate | Total booking revenue ÷ Total booked nights | Bookings |
| Conversion Rate (%) | Visitors to confirmed bookings | (Confirmed bookings ÷ Visitors) × 100 | GA \+ Platform |
| Cost per Acquisition (CPA) | Ad spend per confirmed booking | Total ad spend ÷ Confirmed bookings | Ads |
| Customer Lifetime Value (CLV) | Avg. value per renter | Avg. booking value × Avg. bookings per renter | Users \+ Bookings |
| Top Performing Channels | Bookings by source | GROUP BY source\_channel | Bookings |

---

### **C. Customer & Communication Performance (NEW)**

| KPI | Description | Formula / Definition | Data Source |
| ----- | ----- | ----- | ----- |
| Average Response Time (hrs) | Avg. time between message and reply | AVG(reply\_time \- received\_time) | Messages |
| First Response SLA Compliance (%) | % of messages replied within 2 hours | (Replies ≤ 2h ÷ Total messages) × 100 | Messages |
| Average Resolution Time (hrs) | Avg. time to close/resolved thread | AVG(last\_message \- first\_message) | Messages |
| Unanswered Message Rate (%) | % of messages unreplied after 24h | (Unanswered ÷ Total received) × 100 | Messages |
| Response Rate (%) | % of messages replied to | (Replied ÷ Received) × 100 | Messages |
| Active Conversations | Active chats per day/week | COUNT(DISTINCT conversation\_id WHERE active) | Messages |
| Support Ticket Resolution Time (hrs) | Avg. time to resolve support issues | AVG(resolution\_time) | Support |
| Traveller Satisfaction (CSAT) | Avg. post-trip or post-message rating | AVG(cs\_rating) | Feedback |
| Dispute Response Time (hrs) | Time to first admin response to dispute | AVG(response\_time\_dispute) | Support |

**Super Admin Goal:**  
 Track owner responsiveness, identify delayed replies, measure communication quality, and ensure high traveller satisfaction.

---

### **D. Growth & Partner Metrics**

| KPI | Description | Formula | Data Source |
| ----- | ----- | ----- | ----- |
| New Owners Onboarded | New owner profiles | COUNT(owner\_id WHERE created\_at) | Users |
| New Campers Listed | Newly added vehicles | COUNT(vehicle\_id WHERE created\_at) | Vehicles |
| Active Owners (%) | Owners with ≥1 booking | (Active owners ÷ Total owners) × 100 | Bookings \+ Users |
| Average Owner Earnings (€) | Avg. payout per owner | Total payouts ÷ Active owners | Payouts |
| Referral Rate (%) | % of new users via referral | (Referred ÷ New users) × 100 | Referrals |
| Seasonality Index | Monthly demand vs average | (Monthly bookings ÷ Yearly avg.) | Bookings |

---

### **E. Dashboard Layout Suggestion (Super Admin)**

**Top Summary Bar (Quick View):**

* Occupancy Rate

* GBV

* ADR

* Conversion Rate

* Avg. Rating

* Avg. Response Time

**Main Panels:**

* Fleet Utilisation Heatmap (bookings per camper/day)

* Revenue by Camper (bar chart)

* Marketing Source Breakdown (pie chart)

* Owner Performance Table (utilisation, payouts, ratings)

* Communication Panel (response time, SLA, CSAT)

* Customer Experience Panel (ratings, disputes, resolution)

**Filters:**

* Date range

* Camper model

* Owner / Region

* Channel (Direct, Goboony, etc.)

---

## **2\. FLEET OWNER DASHBOARD**

**Purpose:**  
To provide fleet owners with a clear overview of their campers, earnings, bookings, and communication performance without showing platform-wide data.

**Access:**  
 View only their own vehicles, bookings, and messages. No other owners’ data.

---

### **A. Fleet Summary**

| KPI | Description | Formula / Definition | Data Source |
| ----- | ----- | ----- | ----- |
| Total Campers | Total owned campers | COUNT(vehicles WHERE owner\_id \= X) | Vehicles |
| Total Nights Booked | Nights booked per camper | SUM(booked\_nights) | Bookings |
| Occupancy Rate (%) | % of available nights booked | (Booked ÷ Available) × 100 | Bookings |
| Average Booking Duration | Avg. nights per booking | Total booked nights ÷ Total bookings | Bookings |
| Total Revenue (€) | Gross & net earnings | SUM(total\_price), SUM(payouts) | Transactions |
| Upcoming Bookings | Future confirmed bookings | COUNT(bookings WHERE start\_date \> NOW()) | Bookings |
| Cancellation Rate (%) | Cancelled vs confirmed bookings | (Cancelled ÷ Confirmed) × 100 | Bookings |
| Lead Time (days) | Avg. days between booking & pickup | AVG(pickup\_date \- booking\_date) | Bookings |

---

### **B. Financial Summary**

| KPI | Description | Formula / Definition | Data Source |
| ----- | ----- | ----- | ----- |
| Total Earnings (Month/YTD) | Owner’s income | SUM(payouts) | Payouts |
| Pending Payouts | Yet-to-be-paid balances | SUM(pending) | Payouts |
| Deposits Held | Active deposits | SUM(deposits WHERE status=held) | Bookings |
| Additional Charges | Fuel/cleaning/damage fees | SUM(extra\_charges) | Transactions |
| Average Daily Rate | Avg. daily income | Total revenue ÷ Booked nights | Bookings |

---

### **C. Communication & Traveller Interaction (NEW)**

| KPI | Description | Formula / Definition | Data Source |
| ----- | ----- | ----- | ----- |
| Average Response Time (hrs) | Time from traveller message to reply | AVG(reply\_time \- message\_received) | Messages |
| First Response Rate (%) | % of replies sent within 2h | (Replies ≤ 2h ÷ Total first messages) × 100 | Messages |
| Total Conversations | All chats with travellers | COUNT(DISTINCT conversation\_id) | Messages |
| Unread Messages | Messages awaiting reply | COUNT(messages WHERE unread \= true) | Messages |
| Response Rate (%) | % of received messages replied to | (Replies ÷ Received) × 100 | Messages |
| Message-to-Booking Conversion (%) | Chats that led to bookings | (Bookings\_from\_chat ÷ Total conversations) × 100 | Bookings \+ Messages |
| Traveller Rating After Communication | Avg. rating post-chat | AVG(cs\_rating) | Reviews |
| Support Escalations | Issues escalated to admin | COUNT(escalations) | Support |

**Fleet Owner Goal:**  
 Encourage responsiveness, track their own communication speed, and understand how it impacts bookings and reviews.

---

### **D. Camper Insights**

| KPI | Description | Formula / Definition | Data Source |
| ----- | ----- | ----- | ----- |
| Occupancy per Camper (%) | % of days booked | (Booked days ÷ Available days) × 100 | Bookings |
| Revenue per Camper (€) | Earnings per camper | SUM(revenue) GROUP BY camper | Transactions |
| Average Rating | Avg. traveller rating | AVG(rating\_score) | Reviews |
| Days Until Next Booking | Countdown | MIN(start\_date \- NOW()) | Bookings |
| Maintenance Alerts | Service or inspection reminders | IF(service\_due\_date ≤ 30d) | Maintenance |

---

### **E. Dashboard Layout (Fleet Owner)**

**Top Summary Cards:**

* Total Campers

* Occupancy Rate

* Total Revenue

* Average Response Time

* Average Rating

**Main Panels:**

* Revenue by Camper (bar chart)

* Bookings Calendar

* Communication Overview (response rate, unread messages, conversion)

* Traveller Feedback (recent reviews \+ CSAT)

* Maintenance Reminders

**Filters:**

* Camper

* Date range

* Booking status

---

## **Summary of Communication KPI Visibility**

| Communication KPI | Super Admin | Fleet Owner |
| ----- | ----- | ----- |
| Avg. Response Time | ✅ | ✅ |
| Response Rate | ✅ | ✅ |
| SLA Compliance | ✅ | ✅ |
| Unanswered Message Rate | ✅ | ✅ |
| Active Conversations | ✅ | ✅ |
| Message-to-Booking Conversion | ✅ | ✅ |
| CSAT (Communication Rating) | ✅ | ✅ |
| Support Ticket Resolution Time | ✅ | ❌ |
| Dispute Response Time | ✅ | ❌ |
| Top Slow Responders | ✅ | ❌ |

---

### **⚙️ Automation Notes**

* **API endpoints:** `/bookings`, `/vehicles`, `/owners`, `/transactions`, `/reviews`, `/messages`, `/support`

* **Daily cron job:** refresh booking \+ transaction data

* **Weekly cron job:** refresh marketing \+ owner data

* **Data handling:** store computed KPIs in `kpi_snapshots` table for historical tracking

* **Error handling:** flag incomplete data (missing payouts, unconfirmed bookings, etc.)

# 

# 

# 

# 

# **NOTIFICATION SYSTEM – COMPLETE SPEC**

## **OVERVIEW**

**Delivery Channels**

| Channel | Use Case |
| ----- | ----- |
| **In-app** | Default for all roles (dashboard bell icon \+ list) |
| **Email** | For important operational updates & confirmations |
| **Push (optional)** | For mobile/PWA users (booking status, urgent issues) |
| **SMS (optional)** | For check-in/out reminders or urgent travel issues |

**Notification Types**

* 🔵 *Informational* (FYI, performance updates)

* 🟢 *Action Required* (something needs attention)

* 🟠 *Warning / Risk* (potential issue, missed deadline)

* 🔴 *Critical / Urgent* (immediate response needed)

---

## **1\. SUPER ADMIN NOTIFICATIONS**

Purpose: give full operational visibility and alerts for platform health, financials, disputes, and onboarding.

| Category | Trigger | Notification Example | Channel | Priority |
| ----- | ----- | ----- | ----- | ----- |
| **New Booking** | Any booking confirmed | “🎉 New booking confirmed for \[Camper Name\] (€\[Amount\])” | In-app, Email | 🟢 |
| **Cancellation** | Booking cancelled | “⚠️ \[Traveller Name\] cancelled a booking for \[Camper Name\]” | In-app, Email | 🟠 |
| **Dispute Opened** | Fleet owner or traveller files dispute | “🚨 Dispute opened on booking \#\[ID\] — review details” | In-app, Email | 🔴 |
| **Late Return** | Vehicle not checked in on time | “🕒 \[Camper Name\] return overdue — contact owner” | In-app, Push | 🔴 |
| **Deposit Held** | Deposit flagged or held | “💰 Deposit for booking \#\[ID\] held for review” | In-app | 🟠 |
| **New Fleet Owner** | New account created | “👋 \[Owner Name\] has registered a new fleet account” | In-app | 🔵 |
| **Inactive Fleet Owner** | No new bookings in 30+ days | “📉 \[Owner Name\] has had no bookings in 30 days — check listing performance” | In-app, Email | 🟢 |
| **Revenue Milestone** | Platform crosses revenue threshold | “💶 Congratulations\! Platform reached €100K GBV this quarter” | In-app | 🔵 |
| **System Alert** | Error in payout, data sync, or API | “⚙️ Error in payout sync for \[Owner Name\] — review in dashboard” | In-app | 🔴 |
| **Message Response Delay** | Owner/Traveller response \>24h | “⏳ \[Owner Name\] has delayed responses — average reply time 27h” | In-app | 🟠 |
| **Owner Feedback Alert** | Negative review (\<3★) | “❗ \[Traveller Name\] left a 2★ review for \[Camper Name\]” | In-app | 🟠 |
| **New Destination Added** | CMS update | “🌍 New travel destination page published: \[Country Name\]” | In-app | 🔵 |

---

## **2\. FLEET OWNER NOTIFICATIONS**

Purpose: support fleet management, income tracking, renter communication, and operational reminders.

| Category | Trigger | Notification Example | Channel | Priority |
| ----- | ----- | ----- | ----- | ----- |
| **New Booking** | Camper booked | “🎉 New booking confirmed for \[Camper Name\] (\[Start Date\] – \[End Date\])” | In-app, Email | 🟢 |
| **Upcoming Pickup** | 24h before check-in | “🚗 \[Traveller Name\] picking up \[Camper Name\] tomorrow” | In-app, Push | 🟢 |
| **Check-In Started** | Traveller begins Goform | “📝 \[Traveller Name\] started check-in for \[Camper Name\]” | In-app | 🟢 |
| **Return Due Soon** | 24h before return | “🕒 \[Camper Name\] due for return tomorrow — prepare inspection form” | In-app, Push | 🟢 |
| **Late Return** | Return not completed after due time | “⚠️ \[Traveller Name\] has not returned \[Camper Name\] yet” | Push, Email | 🔴 |
| **Deposit Released** | After successful return | “💰 Deposit for \[Traveller Name\] released successfully” | In-app | 🟢 |
| **Deposit Held** | Pending issue | “⚠️ Deposit for booking \#\[ID\] held due to reported damage” | In-app | 🟠 |
| **New Message** | Traveller sends message | “💬 New message from \[Traveller Name\]” | In-app, Push | 🟢 |
| **Unreplied Message Reminder** | \>2h without response | “⏳ You have an unanswered message from \[Traveller Name\]” | In-app | 🟠 |
| **Low Response Rate Alert** | \<80% replies in 24h | “📭 Your response rate has dropped — faster replies help boost your ranking\!” | In-app, Email | 🟠 |
| **Maintenance Reminder** | Based on mileage/dates | “🔧 \[Camper Name\] service due in 7 days” | In-app | 🟢 |
| **Insurance Expiry Warning** | 14 days before expiry | “🛡️ Insurance for \[Camper Name\] expires soon” | Email | 🟠 |
| **Payout Completed** | Payout to owner processed | “💸 Your payout of €\[Amount\] has been processed” | In-app, Email | 🟢 |
| **New Review Received** | Traveller leaves review | “⭐ \[Traveller Name\] left a 5★ review on \[Camper Name\]” | In-app | 🟢 |
| **Negative Review Alert** | \<3★ | “⚠️ Low rating received for \[Camper Name\] — check feedback” | In-app, Email | 🟠 |
| **Performance Summary** | Weekly digest | “📊 This week: \[X\] bookings, \[Y\]% occupancy, \[Z\]€ revenue” | Email | 🟢 |

---

## **3\. TRAVELLER NOTIFICATIONS**

Purpose: guide renters smoothly through the booking, check-in, and trip experience.

| Category | Trigger | Notification Example | Channel | Priority |
| ----- | ----- | ----- | ----- | ----- |
| **Booking Confirmation** | Booking successful | “🎉 Your booking is confirmed\! \[Camper Name\] is ready for your trip.” | In-app, Email | 🟢 |
| **Deposit Reminder** | 24h before pickup | “💰 Don’t forget your deposit due at pickup” | In-app, Email | 🟢 |
| **Pickup Reminder** | 24h before | “🚐 Ready to roll? Your camper pickup is tomorrow at \[Time/Location\].” | Push, Email | 🟢 |
| **Check-In Started** | Owner begins process | “📝 Please complete your check-in with \[Owner Name\]” | In-app | 🟢 |
| **Trip Ongoing** | Mid-trip check | “✨ Enjoying your trip? Remember, we’re here for support if needed.” | Push | 🔵 |
| **Return Reminder** | 24h before due | “⏰ Time to head back — your return for \[Camper Name\] is tomorrow.” | In-app, Push | 🟢 |
| **Late Return Alert** | If overdue | “⚠️ You’re late returning \[Camper Name\]. Contact \[Owner Name\] immediately.” | Push | 🔴 |
| **Deposit Released** | After return approved | “💰 Your deposit has been refunded — thanks for travelling with us\!” | In-app, Email | 🟢 |
| **New Message** | Owner replies | “💬 \[Owner Name\] replied to your message” | In-app, Push | 🟢 |
| **Unreplied Message Reminder** | No reply in 2h | “⏳ Did you see \[Owner Name\]’s message?” | In-app | 🟠 |
| **Trip Review Request** | 24h after return | “⭐ How was your trip with \[Camper Name\]? Leave a quick review.” | Email, In-app | 🟢 |
| **Special Offers / Promotions** | Seasonal or geo-targeted | “🍂 10% off autumn camper adventures — book your next trip today\!” | In-app, Email | 🔵 |
| **Travel Inspiration** | Automated monthly | “🚐 Discover 5 new routes to explore in Portugal this season.” | Email | 🔵 |

---

## **4\. ADMIN PANEL FOR NOTIFICATIONS**

Super Admin controls:

* Notification templates (editable text)

* Toggle per user type (on/off for email, push)

* Scheduling (digest vs real-time)

* Filtering (per country, language, or fleet)

* “Resend notification” option for manual use

---

## **5\. SMART LOGIC IDEAS** 

| Feature | Description |
| ----- | ----- |
| **Notification Center** | Central dashboard tab for all alerts (filter by unread, role, date) |
| **Auto-clear Rules** | Archive read notifications after 30 days |
| **Smart Prioritization** | Critical notifications stay pinned until resolved |
| **Linked Actions** | Clicking a notification opens relevant booking, message, or payout |
| **Custom Notification Rules** | Allow fleet owners to choose which alerts they want (e.g. only new bookings \+ messages) |
| **Digest Summary Emails** | Weekly summaries for owners and admins (performance KPIs \+ key alerts) |

# **DIGITAL PICKUP & RETURN PROCESS**

*(formerly check-in and check-out)*

## **Purpose**

To standardize, digitize, and simplify the pickup and return flow between Traveller and Fleet Owner, while ensuring:

* Transparent vehicle condition documentation (before and after)

* Secure deposit handling

* Automated form generation and data syncing

* Compliance for international users (multi-language support)

---

## **CORE FLOW OVERVIEW**

**Step 1: Pickup (Start of Rental)**  
 Initiated by Fleet Owner.

**Step 2: Return (End of Rental)**  
 Initiated by Fleet Owner.

Both processes use a shared digital “Goform” (rental record) that updates in real time between both parties and syncs automatically with booking data.

---

## **PICKUP PROCESS (DIGITAL CHECK-IN)**

**Trigger:**  
 Fleet Owner clicks “Start Pickup” in the platform dashboard or app.

### **1\. Verification**

* Verify booking ID and renter details.

* Confirm traveller identity (ID upload or manual check).

* Confirm deposit received or collected via the platform.

**Data Captured:**

* Booking ID

* Traveller name \+ ID verification photo

* Deposit confirmation (checkbox \+ amount)

---

### **2\. Vehicle Overview**

* Camper selected from the owner's fleet (auto-filled).

* Odometer reading (manual entry \+ photo).

* Fuel level (dropdown: Empty–Full \+ photo upload).

* Battery %, water levels, waste tank, LPG (if applicable).

**Data Captured:**

`vehicle_id`  
`odometer_start`  
`fuel_start`  
`battery_level_start`  
`water_level_start`  
`waste_tank_start`  
`timestamp_start`

---

### **3\. Condition Check**

* Step-by-step guided checklist (exterior → interior → equipment).

* Each section includes:

  * Checkbox for “OK” / “Damage present”

  * Optional photo upload

  * Notes field

**Example Sections:**

* Exterior body panels

* Roof and pop-top

* Interior walls and upholstery

* Kitchen and appliances

* Electrical and lights

* Awning, table, accessories

If damage is found, mark with a red flag and automatically alert Super Admin.

---

### **4\. Equipment Confirmation**

Traveller and Owner confirm that all included items are present (pre-defined list per camper):

* Kitchen kit

* Power cables

* Camping chairs/tables

* Manuals and documents

Checkbox confirmation required.

---

### **5\. Sign and Confirm**

Both parties review and sign digitally (drawn signature or click-to-sign).

**Generated Data:**

* Pickup timestamp

* GPS location (optional)

* Digital signatures (owner and traveller)

* PDF/JSON record of the pickup form

**System Actions:**

* Booking status changes to `active`.

* PDF saved under booking ID.

* Email sent to both parties with digital copy.

---

## **RETURN PROCESS (DIGITAL CHECK-OUT)**

**Trigger:**  
 Fleet Owner clicks “Start Return” in the dashboard/app.

### **1\. Vehicle Verification**

* Camper auto-selected from ongoing booking.

* Odometer reading (end).

* Fuel level (end \+ photo).

* Battery, water, and waste levels recorded.

**Data Captured:**

`odometer_end`  
`fuel_end`  
`battery_level_end`  
`water_level_end`  
`timestamp_end`

---

### **2\. Return Condition Check**

The system automatically compares the pickup condition form.

* Highlights previously flagged areas.

* The owner inspects and logs new damages (if any).

* Photos and comments required for new issues.

Optional: add damage type and estimated cost.

---

### **3\. Agreement Compliance**

Quick yes/no questions:

* Fuel refilled?

* Mileage within limit?

* Returned on time?

* Cleanliness satisfactory?

If “no,” the owner can:

* Add additional costs (fuel, cleaning, late fee).

* Attach receipts or notes.

---

### **4\. Deposit Resolution**

* If all is fine → “Release Deposit.”

* If not → “Hold Deposit” and automatically notify Super Admin with evidence.

* All actions timestamped.

**System Actions:**

* Deposit transaction updated (via Stripe/Mollie API).

* Notification sent to Traveller and Admin.

---

### **5\. Sign and Confirm Return**

Both parties review and sign the final condition report.

**Generated Data:**

* Return timestamp

* Both signatures

* Final condition summary (before vs after)

* Financial summary (rental fee, extras, deposit resolution)

**System Actions:**

* Booking marked as `completed`.

* PDF summary emailed to both parties.

* Record archived to `rental_history` table.

---

## **DIGITAL DOCUMENT OUTPUTS**

**Pickup Form (Goform\_Start.pdf)**

* Vehicle info and condition.

* Signatures and timestamp.

* Deposit record.

**Return Form (Goform\_End.pdf)**

* Condition comparison table.

* Extra costs or damage details.

* Deposit resolution.

* Final signatures.

**Storage Path:**  
 `/storage/bookings/[booking_id]/forms/`

---

## **NOTIFICATIONS (CONNECTED TO THIS FLOW)**

**Traveller:**

* “Your pickup process has started — please verify the condition.”

* “Pickup completed successfully.”

* “Return scheduled for tomorrow.”

* “Return completed — deposit released.”

**Fleet Owner:**

* “Pickup form started — complete with traveller.”

* “Return due tomorrow for \[Camper Name\].”

* “Return process complete — deposit released.”

* “Damage reported — awaiting admin review.”

**Super Admin:**

* “Damage report received from \[Owner Name\] for booking \#\[ID\].”

* “Deposit held pending dispute.”

---

## **ADVANCED FEATURES (FUTURE ENHANCEMENTS)**

| Feature | Description |
| ----- | ----- |
| Offline Mode | Forms work without internet; syncs when back online. |
| Photo Auto-Tagging | AI labels images (e.g. “scratch on left door”). |
| Signature Verification | Optional ID match for legal compliance. |
| GPS Tracking | Location check at pickup and return. |
| Smart Comparison View | Before/after damage overlay. |
| PDF Export Branding | Add platform and owner logo automatically. |

---

## **DATA MODEL REFERENCES**

**Tables Needed:**

* `bookings`

* `vehicles`

* `users`

* `rental_forms` (pickup/return)

* `rental_damages`

* `transactions`

* `notifications`

---

## **MULTINATIONAL SUPPORT**

* Multi-language UI (EN, NL, DE, FR, ES).

* Date/time and currency auto-localization.

* Localized templates for PDF forms and notifications.

* Optional digital signature compliance (eIDAS for EU).

# Hybrid Dynamic Pricing System — Overview

### **Objective**

Create a dynamic pricing engine that:

1. **Respects each fleet owner’s minimum and maximum prices** (owner control).

2. **Learns automatically from platform performance data** (internal AI).

3. **Adapts to market conditions** (external competitor index \+ seasonality).

4. **Improves accuracy over time** as more bookings and user behaviour data are collected.

---

## **1\. Pricing Layers**

The hybrid model uses three pricing layers that blend rules and AI predictions:

| Layer | Source | Description | Example |
| ----- | ----- | ----- | ----- |
| **Floor / Ceiling** | Fleet Owner | Owner defines min and max price range | Min €90, Max €150 |
| **Base Price (Rule-Based)** | Platform | Seasonal baseline per camper type, region, and season | Spring: €110/night |
| **Dynamic Adjustment (AI)** | AI Model | Applies % increase/decrease based on demand, events, and market data | \+12% during holidays |

**Final Formula Example:**

`Final_Price = clamp(`  
   `Base_Price × (1 + AI_Adjustment),`  
   `Owner_Min,`  
   `Owner_Max`  
`)`

If the AI suggests €160 but the owner’s max is €150 → system caps it at €150.  
 If demand drops and AI suggests €80 → it stays at €90 (owner’s min).

---

## **2\. Data Sources (for AI Training & Dynamic Adjustments)**

| Data Type | Examples | Source |
| ----- | ----- | ----- |
| **Internal** | Booking trends, occupancy, lead times, seasonality, cancellations | Platform database |
| **External (Market)** | Competitor prices, region demand index, event calendar, fuel prices | Public APIs / scraped listings |
| **Contextual** | Weather, public holidays, local events, school breaks | Event & weather APIs |
| **Owner Settings** | Min/max prices, camper type, amenities | Fleet owner input |

---

## **3\. Learning Over Time (Self-Improving)**

The model evolves as data grows:

| Phase | Time | Description |
| ----- | ----- | ----- |
| **Phase 1 – Cold Start** | Month 1–3 | Uses rule-based \+ owner pricing; AI collects initial data |
| **Phase 2 – Warm Model** | Month 4–6 | AI starts suggesting adjustments (±10–15%) |
| **Phase 3 – Mature Model** | 6+ months | AI predicts optimal price daily based on occupancy, lead time, market data |
| **Phase 4 – Continuous Learning** | Ongoing | Reinforcement learning adjusts strategy per region and camper type |

---

## **4\. Owner Controls (Transparency & Flexibility)**

Owners remain fully in control.  
 They can set:

| Setting | Description | Example |
| ----- | ----- | ----- |
| **Min/Max Price** | Hard limits | Min €90, Max €150 |
| **Pricing Mode** | How aggressive the AI should be | Conservative / Balanced / Aggressive |
| **Adjustment Frequency** | How often AI updates | Daily / Weekly |
| **Manual Lock Dates** | Prevent changes before specific trips | Lock 10 days before booking |
| **Override Option** | Owners can manually accept or reject AI suggestions | “Accept Suggested Price” button |

---

## **5\. AI Adjustment Factors**

Each AI price recommendation can include a **reason code** for transparency (displayed in dashboard):

| Factor | Example | Weight |
| ----- | ----- | ----- |
| Local occupancy ↑ | “85% of similar campers in Amsterdam are booked this weekend” | \+10% |
| Market price ↑ | “Competitor average rose 7% this week” | \+5% |
| Weather ↓ | “Rain expected for 3 days” | \-4% |
| Early bird trend | “Bookings made 45+ days in advance show strong demand” | \+8% |
| Event nearby | “Festival in Utrecht (3–6 May)” | \+12% |
| Low platform conversion | “Few inquiries in last 7 days” | \-6% |

---

## **6\. Developer Implementation Overview**

### **System Architecture**

`[Platform DB] → [Data Preprocessor] → [Hybrid Pricing Engine]`  
                                `↘`  
                   `[External Market Feed]`

### **Key Components**

1. **Pricing Rules Engine**  
    Applies owner min/max, static rules, and base seasonal adjustments.

2. **AI Model (XGBoost or LightGBM)**  
    Predicts ideal adjustment factor based on input features.

3. **Pricing Service API**  
    Endpoint `/api/pricing/suggest` returns suggested price and explanation.

4. **Dashboard Integration**

   * Displays current base price

   * AI-suggested price

   * Market median

   * Confidence score

   * “Accept/Reject” buttons

---

## **7\. Example API Response**

`{`  
  `"camper_id": 45,`  
  `"base_price": 100,`  
  `"owner_min": 90,`  
  `"owner_max": 150,`  
  `"suggested_price": 115,`  
  `"market_median": 110,`  
  `"confidence": 0.86,`  
  `"adjustment_reason": [`  
    `"Local demand +12%",`  
    `"Competitor avg +6%",`  
    `"Weekend uplift"`  
  `],`  
  `"pricing_mode": "Balanced"`  
`}`

---

## **8\. Platform Data Feedback Loop**

Every confirmed booking → stored with:

* Date and price

* Lead time

* Region

* Conversion rate

* Occupancy snapshot

→ Used to retrain the model weekly or monthly for improved predictions.

---

## **9\. Super Admin Oversight**

Super Admin Dashboard gets additional controls:

* Global AI activity toggle

* Performance comparison: AI vs manual pricing

* Average occupancy lift from AI pricing

* Outlier detection (too low/high compared to market)

* Adjustment override for specific regions or time periods

---

## **10\. Developer Implementation Roadmap**

| Phase | Task | Description |
| ----- | ----- | ----- |
| 1 | Owner pricing limits | Add min/max price fields in owner settings |
| 2 | Rule-based engine | Create baseline seasonal pricing engine |
| 3 | Market data integration | Pull competitor & region price data |
| 4 | AI model (MVP) | Predict adjustment factor per listing |
| 5 | Hybrid integration | Combine rule \+ AI \+ owner constraints |
| 6 | Dashboard UI | Add “Suggested Price” panel & override controls |
| 7 | Monitoring | Track performance of AI vs manual pricing |
| 8 | Continuous learning | Weekly retraining \+ confidence tracking |

---

## **11\. Future Extensions**

* Dynamic weekend uplift & event-based pricing

* Personalized pricing per renter segment (loyalty discounts)

* Integration with external pricing APIs (if available)

* AI explainability layer → “why did price change?”

# 

# **FRONTEND SCOPE (v2)**

### **Platform Frontend (Marketplace for Camper Rentals)**

**Design Style:** Mix between Booking.com and Goboony  
**Markets:** Multinational (multi-language, multi-currency support)

---

## **1\. CORE PRINCIPLES**

* App-like experience (mobile-first: \~82% mobile users)

* Responsive and fast (lazy loading, efficient queries)

* Localized content (languages, currencies, destinations)

* SEO optimized (structured metadata, schema markup)

* Accessibility compliant (WCAG 2.1)

* Modular and scalable for future integrations (AI, payments, verification)

---

## **2\. FRONTEND PAGE STRUCTURE (SITEMAP)**

### **2.1 HOMEPAGE**

**Header/Menu**

* Language selector (flag icon in circle)

* How it works

* Contact Help Center

* Register

* Login

**Hero Section**

* Search bar: Pickup point | Dates | Travellers

**Sections**

* Offers section (tiles with current promotions)

* Trending destinations (Norway | Spain | Germany)

* Browse by camper type

* Top campers

* Why this platform? (trust & transparency)

* Sign up and save 10% on first rental

**Footer 1**  
 Titles:  
 Camper in Cities | Popular Provinces | Tips & Tricks

* Camper in Cities: Amsterdam | Utrecht | Eindhoven | Rotterdam

* Popular Provinces: Noord-Holland | Limburg | Gelderland

**Footer 2**  
 Titles:  
 Help Center | About | Terms | Partners

---

### **2.2 HELP CENTER**

**Split into Two Sections:**

**Travelers**

* Manage your trip: Payments, cancellations, disputes

**Fleet Owners**

* Intro to the platform

* Manage bookings

* Income and payments

**Subpages:**

* Terms & Conditions

* Accessibility Statement

---

### **2.3 ABOUT PAGE**

* About the platform

* How we work

* Partner with us

---

### **2.4 SEARCH RESULTS PAGE**

**Filters:**

* Price range

* Unlimited kilometers

* Review

* What’s onboard (Airco, Shower, Toilet, Isofix, Winter tyres)

* While driving (Reverse camera, Navigation)

* Number of travellers

* Camper type

* Pet-friendly

* Transmission (Automatic / Manual)

* Age of driver

* Year of construction

* Safety (Roadside assist, Replacement camper)

**Smart Ranking (AI-driven):**

* 40% search relevance

* 30% fleet owner quality metrics

* 20% conversion rate

* 10% popularity (clicks, wishlists)

**Visible Badges:**

* “Highly Responsive Owner”

* “Top Rated Camper”

* “Trending Camper in Region”

* “Recently Booked”

**UI Components:**

* Map view toggle

* List/grid switch

* Lazy loading

* Wishlist save button

---

### **2.5 CAMPER DETAIL PAGE**

**Sections:**

* Photos & Video gallery

* Description and technical specs

* Reviews and average rating (prominent)

* Pricing breakdown (daily, service, cleaning fees)

* Calendar with real-time availability

* Pickup & drop-off location map

* Insurance and cancellation policy

* House rules

* Host profile (with response rate and rating)

* Contact or chat with owner button

* Discounts

**Smart Components:**

* Owner badges (e.g., Quick Responder, Superhost)

* Similar campers (“Travellers also booked…”)

* Dynamic price recommendation widget (AI-driven pricing)

* Add-ons (bedding, bikes, pet fee)

---

### **2.6 CHECKOUT FLOW**

**Steps:**

1. Select camper (dates carried forward from homepage)

2. Add-ons & extras

3. Review booking summary

4. Traveller details (login first)

5. Payment (Stripe/Mollie integration; saved cards supported)

6. Booking confirmation

**Features:**

* Dynamic pricing validation

* Separate deposit & total display

* Instant booking or owner approval

* Secure payment

* Redirect to “Trip Dashboard” after booking

---

### **2.7 DESTINATION & INSPIRATION PAGES**

*(Single reusable template)*

**Purpose:** Inspire travel and boost organic SEO.  
 **Structure:**

* “Explore by Country” (e.g., Netherlands, Germany, Spain)

* “Explore by Region” (e.g., Algarve, Bavaria, Fjords)

* “Camper Routes & Ideas” (multi-day trips)

* “Seasonal Inspiration” (winter trips, summer getaways)

* Dynamic content cards linking to listings

**Each destination page includes:**

* Map of attractions

* Recommended camper types

* Trip duration estimate

* Popular travel dates

* Weather information

* CTA: “Find a camper for this trip”

---

### **2.8 TRAVELLER DASHBOARD**

**Sections:**

* Upcoming Trips (status, countdown, actions)

* Past Trips (reviews, receipts)

* Messages (chat with fleet owners)

* Payments (saved cards, refund history)

* Profile & Verification (driver’s license, ID)

* Notifications (booking updates, offers)

**Smart Features:**

* Check-in reminders

* Upsell modules (insurance upgrades, add-ons)

---

### **2.9 FLEET OWNER DASHBOARD**

**Sections:**

* Fleet Overview (active, maintenance)

* Booking Requests (approve/decline)

* Earnings & Payouts

* Messaging (real-time chat)

* Camper Management (edit listings, pricing, photos)

* Reviews & Ratings

* Profile (response time, badges)

**Dynamic Features:**

* Smart notifications (“New booking”, “Low response rate”)

* AI pricing suggestions

* Calendar sync (iCal, Google Calendar)

---

### **2.10 MESSAGING CENTER**

* Unified inbox (real-time messaging)

* File uploads (ID, booking proof)

* Message read/reply indicators

* Quick reply templates

* Response time tracking (feeds AI metrics)

---

### **2.11 NOTIFICATIONS SYSTEM**

**For Travellers:**

* Booking confirmation / pending

* Payment received

* Check-in reminders

* “Your trip starts soon”

* Review request after trip

**For Fleet Owners:**

* New booking request

* Traveller message

* Payment processed

* Check-in / return reminders

* Review received

* Response rate alerts

**Delivery Methods:**

* In-app popups

* Email

* Optional push notifications

---

### **2.12 REVIEW & RATING FLOW**

* Star rating (1–5)

* Comment box

* Optional tags (“Clean”, “Friendly”, “Reliable”)

* Public & private feedback

* AI moderation (to filter abusive content)

---

### **2.13 VERIFICATION & TRUST PAGES**

**Travellers:**

* ID & driver’s license upload

* Deposit policy explanation

**Fleet Owners:**

* Business/individual verification

* Insurance proof upload

* Bank account validation for payouts

---

### **2.14 MULTI-CURRENCY & LOCALIZATION**

* Auto-detect currency and language

* Manual selector in header

* Live exchange rate API integration

* Localized tax rules (e.g., VAT by region)

---

### **2.15 TRUST, INSURANCE & LEGAL PAGES**

Footer \> Legal:

* Insurance Policy

* Damage & Deposit Policy

* Privacy Policy

* Cookie Policy

* Terms of Service

* Accessibility Statement

---

### **2.16 REFERRAL / LOYALTY SYSTEM (Future Addition)**

* “Invite a Friend” page with unique referral links

* Loyalty dashboard (discount tiers, badges)

---

### **2.17 BLOG / CONTENT HUB**

**Categories:**

* Travel Guides

* Vanlife Tips

* Owner Guides

* Camper Conversion Stories

**Each Post Includes:**

* High-quality images

* Related camper listings

* Author bio

* SEO metadata

---

## **3\. SMART FRONTEND SYSTEM (AI-ASSISTED UI)**

**Purpose:** Use data to dynamically personalize and optimize the platform.

**Data Inputs:**

* Fleet owner metrics (response time, acceptance, cancellation)

* Traveller engagement (clicks, wishlists, bookings)

* Review data

* Occupancy & pricing trends

* Location-specific demand

**Dynamic Features:**

* Smart ranking on search results

* Personalized homepage sections (“Recommended for you”)

* Trending & top owner carousels

* Real-time “Last booked” and “In high demand” tags

* Automated remarketing (“Your favorite camper is available again”)

* Promotion of top-performing fleet owners with best response metrics

---

## **4\. CHECKOUT & POST-BOOKING FLOW (END-TO-END)**

1. Camper selected → dynamic pricing check

2. Traveller login/creation

3. Add-ons selection

4. Payment

5. Booking confirmation

6. Pre-trip notifications (automated)

7. Check-in & return (digital process integrated via dashboard)

8. Review request

---

## **5\. DIGITAL PICKUP & RETURN PROCESS (Integrated into Dashboards)**

**Pickup Flow:**

* Digital checklist

* Photo upload (condition proof)

* Odometer reading input

* Fuel level capture

* e-Signature confirmation

**Return Flow:**

* Repeat condition photo upload

* Damage or discrepancy report

* Final odometer entry

* e-Signature for return

* Auto trigger for security deposit handling

