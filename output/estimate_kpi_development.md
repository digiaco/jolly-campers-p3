# KPI Development Estimate

**Total Hours: 360**

**Note:** Assuming all data are non-aggregated.

---

## Summary

This document outlines the development estimate for KPI tracking and analytics dashboards across the platform. The system includes:

- Admin Dashboard with comprehensive analytics
- Fleet Owner Dashboard with owner-specific metrics
- Data tracking and reporting capabilities
- Export, drill-down, and filtering by owner/fleet, camper vehicle, timeframe, timezone, currency, and locations

---

## Admin Dashboard API

### Fleet & Booking Performance

| Task | Hours | Description | Formula | Notes |
|------|-------|-------------|---------|-------|
| Occupancy Rate (%) | 4 | % of total available nights booked | (Booked nights ÷ Total available nights) × 100 | |
| Average Booking Length (days) | 4 | Avg. nights per booking | Total booked nights ÷ Total bookings | |
| Utilization per Camper | 4 | Days rented per camper | SUM(booked_days) GROUP BY vehicle_id | |
| Revenue per Camper (€) | 4 | Income per vehicle | SUM(total_price) GROUP BY vehicle_id | |
| Cancellation Rate (%) | 4 | % of cancelled bookings | (Cancelled ÷ Confirmed) × 100 | |
| Lead Time (days) | 4 | Avg. time between booking and pickup | AVG(pickup_date - booking_date) | |
| Repeat Booking Rate (%) | 5 | Returning customers ratio | (Returning renters ÷ Total renters) × 100 | |

**Subtotal: 29 hours**

---

### Financial & Marketing Performance

| Task | Hours | Description | Formula | Notes | Questions |
|------|-------|-------------|---------|-------|-----------|
| Gross Booking Value (GBV) | 4 | Total income before fees | SUM(total_booking_amount) | Group by currency | Is it possible to have multiple currency for each fleet admin? How do we show the report for locations? |
| Commission Revenue (€) | 4 | Platform earnings | SUM(total_booking_amount × commission_rate) | Group by currency | What to do when there are multi currencies? |
| Owner Payout (€) | 4 | Total payouts to owners | SUM(owner_payouts) | Group by currency. Group by countries | |
| Average Daily Rate (ADR) | 4 | Avg. daily rental rate | Total booking revenue ÷ Total booked nights | Group by currency | Or group by currency |
| Conversion Rate (%) | 12 | Visitors to confirmed bookings | (Confirmed bookings ÷ Visitors) × 100 | Group by currency. Need to have visitors metrics<br>• Can use Google Analytics to track unique visitors<br>• Call Google Analytics to get the number of unique visitors | |
| Cost per Acquisition (CPA) | 12 | Ad spend per confirmed booking | Total ad spend ÷ Confirmed bookings | Group by currency. Needs to have ads metrics and pixel<br>• Use ad platform conversion tracking (Meta Pixel, Google Ads Conversion Tag)<br>• When a user clicks on an ad, the platform appends a click ID (e.g., gclid, fbclid)<br>• You capture that ID and persist it in the booking session<br>• On booking confirmation, fire the conversion event with the original click ID back to the ad platform<br>• This allows the ad platform to track CPA natively, attribute correctly, and optimize campaigns<br>• 12 hrs for one ad platform, and additional 5 hour for each ad platform<br><span style="color: red;">**Revisit estimate:** This could easily exceed 12 hours for the first platform. Consider 15-20 hours for robust implementation with proper error handling, testing across platforms, and documentation.</span> | |
| Customer Lifetime Value (CLV) | 4 | Avg. value per renter | Avg. booking value × Avg. bookings per renter | Group by currency | |
| Top Performing Channels | 4 | Bookings by source | GROUP BY source_channel | Group by currency. Need to have source channel info | |

**Subtotal: 48 hours**

---

### Customer & Communication Performance (Data Store)

| Task | Hours | Description |
|------|-------|-------------|
| first_conversation_initiated | 4 | Waiting message start time |
| first responded_at, response_time | 4 | • Define what "first reply"<br>• Reply time<br>• Calculate the reply time and save to db |
| conversation_start_time | 2 | |
| conversation_reply_time, response time | 2 | |
| resolution time | 4 | Define what resolution is |

**Subtotal: 16 hours**

---

### Customer & Communication Performance (Reporting)

| Task | Hours | Description | Formula | Notes |
|------|-------|-------------|---------|-------|
| Average Response Time (hrs) | 4 | Avg. time between message and reply | AVG(reply_time - received_time) | |
| First Response SLA Compliance (%) | 4 | % of messages replied within 2 hours | (Replies ≤ 2h ÷ Total messages) × 100 | |
| Average Resolution Time (hrs) | 4 | Avg. time to close/resolved thread | AVG(last_message - first_message) | |
| Unanswered Message Rate (%) | 4 | % of messages unreplied after 24h | (Unanswered ÷ Total received) × 100 | |
| Response Rate (%) | 4 | % of messages replied to | (Replied ÷ Received) × 100 | |
| Active Conversations | 4 | Active chats per day/week | COUNT(DISTINCT conversation_id WHERE active) | |
| Support Ticket Resolution Time (hrs) | 4 | Avg. time to resolve support issues | AVG(resolution_time) | |
| Traveller Satisfaction (CSAT) | 0 | Avg. post-trip or post-message rating | AVG(cs_rating) | Need to have CSAT BE and FE |
| Dispute Response Time (hrs) | 0 | Time to first admin response to dispute | AVG(response_time_dispute) | Need to support dispute feature |

**Subtotal: 28 hours**

---

### Growth & Partner Metrics

| Task | Hours | Description | Formula | Notes |
|------|-------|-------------|---------|-------|
| New Owners Onboarded | 4 | New owner profiles | COUNT(owner_id WHERE created_at) | |
| New Campers Listed | 4 | Newly added vehicles | COUNT(vehicle_id WHERE created_at) | |
| Active Owners (%) | 4 | Owners with ≥1 booking | (Active owners ÷ Total owners) × 100 | |
| Average Owner Earnings (€) | 4 | Avg. payout per owner | Total payouts ÷ Active owners | |
| Track referral | 0 | Visitor source<br>• none / organic<br>• search engine (google, etc)<br>• social media (instagram, facebook, etc)<br>• Assuming we are using Google Analytics and we can query the data from GA | | |
| Referral Rate (%) | 8 | % of new users via referral | (Referred ÷ New users) × 100 | • Need to track source on registration<br>• Social media, not the actual referral system<br>• Need to define the referred users, the social media users<br>• Can possibly reduce time |
| Seasonality Index | 4 | Monthly demand vs average | (Monthly bookings ÷ Yearly avg.) | |

**Subtotal: 28 hours**

---

## Admin Dashboard - All (UI)

### Top Summary Bar (Quick View)

| Task | Hours | Description |
|------|-------|-------------|
| Occupancy Rate | 2 | Data / selected timeframe, past N days. All fleet owner data combined into one number. |
| GBV | 2 | Data / selected timeframe, past N days. All fleet owner data combined into one number. |
| ADR | 2 | Data / selected timeframe, past N days. All fleet owner data combined into one number. |
| Conversion Rate | 2 | Data / selected timeframe, past N days. All fleet owner data combined into one number. |
| Avg. Rating | 2 | Data / selected timeframe, past N days. All fleet owner data combined into one number. |
| Avg. Response Time | 2 | Data / selected timeframe, past N days. All fleet owner data combined into one number. |
| Marketing Source Breakdown (pie chart) | 4 | Assuming we can track this data with GA and pull the data from GA | **Revisit** |

**Subtotal: 16 hours**

---

### Growth & Partner Metrics UI

| Task | Hours | Description | Notes |
|------|-------|-------------|-------|
| New Owners Onboarded | 4 | • Bar chart or table, New owners count, group by number of days, for the past N sets of day groups<br>• ie, last 7 days, 30 days, 12 months | |
| New Campers Listed | 4 | • Bar chart or table, New campers listed count, group by number of days, for the past N sets of day groups<br>• ie, last 7 days, 30 days, 12 months | |
| Active Owners (%) | 4 | • Bar chart or table, Active owners count, group by number of days, for the past N sets of day groups<br>• ie, last 7 days, 30 days, 12 months | |
| Average Owner Earnings (€) | 6 | • Bar chart or table, Average owner earnings, group by number of days, for the past N sets of day groups<br>• ie, last 7 days, 30 days, 12 months | |
| Referral Rate (%) | 4 | • Bar chart or table, New owners count, group by number of days, for the past N sets of day groups<br>• ie, last 7 days, 30 days, 12 months | Need to check where to get the data |
| Seasonality Index | 4 | • Bar chart or table, Monthly index number, group by month, for the past N months. | |

**Subtotal: 26 hours**

---

### Financial & Marketing Performance UI

| Task | Hours | Description | Notes |
|------|-------|-------------|-------|
| Gross Booking Value (GBV) | 2 | • Bar chart or table, Group by number of days, for the past N sets of day groups.<br>• ie, last 7 days, 30 days, 12 months | |
| Commission Revenue (€) | 2 | • Bar chart or table, Group by number of days, for the past N sets of day groups.<br>• ie, last 7 days, 30 days, 12 months | |
| Owner Payout (€) | 2 | • Bar chart or table, Group by number of days, for the past N sets of day groups.<br>• ie, last 7 days, 30 days, 12 months | |
| Top Performing Channels | 0 | • Bar chart or table, Group by number of days, for the past N sets of day groups.<br>• ie, last 7 days, 30 days, 12 months | To check where to get the data |
| Conversion Rate (%) | 0 | Data on ads platform | **Revisit** |
| Cost per Acquisition (CPA) | 0 | Data on ads platform | **Revisit** |

**Subtotal: 6 hours**

---

### Main Panels

**Filters:**
- Date range
- Camper model
- Owner / Region
- Channel (Direct, Goboony, etc.)

| Panel | Hours | Description | Notes |
|-------|-------|-------------|-------|
| Owner Performance Table (utilisation, payouts, ratings) | 0 | | |
| • Owner utilisation | 0 | | Same as occupancy rate? |
| • Owner payouts | 0 | | Already covered in owner payout |
| • Owner ratings | 0 | | To revisit |
| Communication Panel (response time, SLA, CSAT) | 0 | | See below |
| Customer Experience Panel (ratings, disputes, resolution) | 0 | | See below |

**Subtotal: 0 hours**

---

## Admin Dashboard - Group by Fleet Owner

### Financial & Marketing Performance

| Task | Hours | Description | Notes |
|------|-------|-------------|-------|
| Fleet Utilisation Heatmap (bookings per camper/day) | 4 | Heatmap view, camper vs time (during selected timeframe, group by day/month) vs color depth | Filtered by fleet owner |
| Revenue by Fleet Owner (bar chart) | 0 | | Same as GBV? |
| Gross Booking Value (GBV) | 2 | • Group by owner, during a selected period, ie, last N days, N months.<br>• Data in table format | |
| Commission Revenue (€) | 2 | • Group by owner, during a selected period, ie, last N days, N months.<br>• Data in table format | |
| Owner Payout (€) | 2 | • Group by owner, during a selected period, ie, last N days, N months.<br>• Data in table format | |
| Average Daily Rate (ADR) | 2 | • Group by owner, during a selected period, ie, last N days, N months.<br>• Data in table format | |
| Customer Lifetime Value (CLV) | 2 | • Group by owner, during a selected period, ie, last N days, N months.<br>• Data in table format | |

**Subtotal: 14 hours**

---

### Customer & Communication Performance (UI)

**Chat types:**
- admin <-> travelers chat
- admin <-> fleet owners chat
- fleet owners <-> travelers chat

| Task | Hours | Description |
|------|-------|-------------|
| Average Response Time (hrs) | 4 | • Group by owner, during a selected period, ie, last N days, N months.<br>• Data in table format |
| First Response SLA Compliance (%) | 4 | • Group by owner, during a selected period, ie, last N days, N months.<br>• Data in table format |
| Average Resolution Time (hrs) | 4 | • Group by owner, during a selected period, ie, last N days, N months.<br>• Data in table format |
| Unanswered Message Rate (%) | 4 | • Group by owner, during a selected period, ie, last N days, N months.<br>• Data in table format |
| Response Rate (%) | 4 | • Group by owner, during a selected period, ie, last N days, N months.<br>• Data in table format |
| Active Conversations | 4 | • Group by owner, during a selected period, ie, last N days, N months.<br>• Data in table format |
| Support Ticket Resolution Time (hrs) | 4 | • Group by owner, during a selected period, ie, last N days, N months.<br>• Data in table format |
| Traveller Post Message Satisfaction (CSAT) | 2 | • Group by owner, during a selected period, ie, last N days, N months.<br>• Data in table format |
| Traveller Post Trip Satisfaction (CSAT) | 2 | • Group by owner, during a selected period, ie, last N days, N months.<br>• Data in table format<br>• travelers <-> fleet owner |
| Dispute Response Time (hrs) | 2 | • Group by owner, during a selected period, ie, last N days, N months.<br>• Data in table format<br>• travelers <-> fleet owner |

**Subtotal: 34 hours**

---

### Fleet & Booking Performance

**Notes:**
- Group by owner, during a selected period, ie, last N days, N months
- No aggregation, pulling live data, can be slow if data grows
- Data in table format

| Task | Hours | Description |
|------|-------|-------------|
| Occupancy Rate (%) | 2 | |
| Average Booking Length (days) | 2 | Assuming we count the booking start date that's within the selected timeframe |
| Cancellation Rate (%) | 2 | Assuming we count the booking dated, not the start date, during the selected period. |
| Lead Time (days) | 2 | Assuming we count the booking dated, not the start date, during the selected period. |
| Repeat Booking Rate (%) | 2 | Assuming we count the booking dated, not the start date, during the selected period. |

**Subtotal: 10 hours**

---

## Admin Dashboard - Filter by Owner

### Fleet & Booking Performance

**Notes:**
- Filtered by selected owner
- Group by camper, during a selected period
- Non pagination
- ie. last N days, N months
- Filter by fleet owner, shows the list of campers for the owner

| Task | Hours | Description |
|------|-------|-------------|
| Utilization per Camper | 2 | |
| Revenue per Camper (€) | 2 | |
| Revenue by Camper (bar chart) | 3 | • Camper x data / selected timeframe<br>• Non-aggregated, pull live data<br>• Can be slow when there are many owners<br>• Show top N campers, without pagination<br>• Not suitable for a large fleet, unless we choose the top N campers<br>• Filter by fleet owner |

**Subtotal: 7 hours**

---

## Fleet Owner Dashboard API

### Fleet Summary

| Task | Hours | Description | Formula | Notes |
|------|-------|-------------|---------|-------|
| Total Campers | 4 | Total owned campers | COUNT(vehicles WHERE owner_id = X) | Accessible by admin |
| Total Nights Booked | 4 | Nights booked per camper | SUM(booked_nights) | Accessible by admin |
| Occupancy Rate (%) | 0 | % of available nights booked | (Booked ÷ Available) × 100 | Same as the feature in admin section |
| Average Booking Duration | 0 | Avg. nights per booking | Total booked nights ÷ Total bookings | Same as the feature in admin section |
| Total Revenue (€) | 0 | Gross & net earnings | SUM(total_price), SUM(payouts) | Same as the feature in admin section |
| Upcoming Bookings | 4 | Future confirmed bookings | COUNT(bookings WHERE start_date > NOW()) | Accessible by admin |
| Cancellation Rate (%) | 0 | Cancelled vs confirmed bookings | (Cancelled ÷ Confirmed) × 100 | Same as the feature in admin section |
| Lead Time (days) | 0 | Avg. days between booking & pickup | AVG(pickup_date - booking_date) | Same as the feature in admin section |

**Subtotal: 12 hours**

---

### Financial Summary

| Task | Hours | Description | Formula | Notes |
|------|-------|-------------|---------|-------|
| Total Earnings (Month/YTD) | 4 | Owner's income | SUM(payouts) | |
| Pending Payouts | 4 | Yet-to-be-paid balances | SUM(pending) | |
| Deposits Held | 4 | Active deposits | SUM(deposits WHERE status=held) | |
| Additional Charges | 4 | Fuel/cleaning/damage fees | SUM(extra_charges) | |
| Average Daily Rate | 0 | Avg. daily income | Total revenue ÷ Booked nights | Same as the feature in admin section |

**Subtotal: 16 hours**

---

### Communication & Traveller Interaction

| Task | Hours | Description | Formula | Notes |
|------|-------|-------------|---------|-------|
| Average Response Time (hrs) | 1 | Time from traveller message to reply | AVG(reply_time - message_received) | Similar to the feature in admin section |
| First Response Rate (%) | 1 | % of replies sent within 2h | (Replies ≤ 2h ÷ Total first messages) × 100 | Similar to the feature in admin section |
| Total Conversations | 2 | All chats with travellers | COUNT(DISTINCT conversation_id) | |
| Unread Messages | 1 | Messages awaiting reply | COUNT(messages WHERE unread = true) | Similar to the feature in admin section |
| Response Rate (%) | 1 | % of received messages replied to | (Replies ÷ Received) × 100 | Similar to the feature in admin section |
| Message-to-Booking Conversion (%) | 8 | Chats that led to bookings | (Bookings_from_chat ÷ Total conversations) × 100 | Track conversation ID in booking? |
| Traveller Rating After Communication | 0 | Avg. rating post-chat | AVG(cs_rating) | Similar to the feature in admin section |
| Support Escalations | 0 | Issues escalated to admin | COUNT(escalations) | Need to support dispute feature |

**Subtotal: 14 hours**

---

### Camper Insights

| Task | Hours | Description | Formula | Notes |
|------|-------|-------------|---------|-------|
| Occupancy per Camper (%) | 4 | % of days booked | (Booked days ÷ Available days) × 100 | |
| Revenue per Camper (€) | 2 | Earnings per camper | SUM(revenue) GROUP BY camper | Similar to GBV? |
| Average Rating | 4 | Avg. traveller rating | AVG(rating_score) | |
| Days Until Next Booking | 4 | Countdown | MIN(start_date - NOW()) | |
| Maintenance Alerts | 4 | Service or inspection reminders | IF(service_due_date ≤ 30d) | |

**Subtotal: 18 hours**

---

## Fleet Owner Dashboard Layout (UI)

### Top Summary Cards

| Task | Hours | Description |
|------|-------|-------------|
| Total Campers | 2 | One number |
| Occupancy Rate | 2 | Data / selected timeframe, past N days. All data combined into one number. |
| Total Revenue | 2 | Data / selected timeframe, past N days. All data combined into one number. |
| Average Response Time | 2 | Data / selected timeframe, past N days. All data combined into one number. |
| Average Rating | 2 | Data / selected timeframe, past N days. All data combined into one number. |

**Subtotal: 10 hours**

---

### Inherited Metrics from Admin Dashboard

| Task | Hours | Description |
|------|-------|-------------|
| All metrics from "Admin Dashboard - Group by Fleet Owner" | 8 | • Can see the metrics for the current fleet owner, if applicable<br>• Query all the metrics at once instead of loading each metrics one by one |
| All metrics from "Admin Dashboard - Filter by Owner" | 8 | • Can see the metrics for the current fleet owner, if applicable |

**Subtotal: 16 hours**

---

### Main Panels

**Filters (if applicable):**
- Camper
- Date range
- Booking status

| Panel | Hours | Description |
|-------|-------|-------------|
| Revenue by Camper (bar chart) | 4 | |
| Bookings Calendar | 0 | N/A (Not analytics) |
| Communication Overview (response rate, unread messages, conversion) | 8 | |
| Traveller Feedback (recent reviews + CSAT) | 0 | |
| Maintenance Reminders | 0 | Not analytics |

**Subtotal: 12 hours**

---

## Total Hours: 360

