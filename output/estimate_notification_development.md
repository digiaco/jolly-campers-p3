# Notification Development Estimate

## Summary
**Total Hours: 246**

Breakdown:
- [InApp Notification System](#inapp-notification-system): 59 hours
- [Email Service](#email-service): 9 hours
- [Super Admin Notification](#super-admin-notification): 61 hours
- [Fleet Owner Notification](#fleet-owner-notification): 69 hours
- [Traveler Notification](#traveler-notification): 48 hours

---

## InApp Notification System

**Total Hours: 59**

| Feature | Task | Hours | Description |
|---------|------|-------|-------------|
| Notification | Bell notification (UI) | 3 | Show unread indicator (dot or count). If count exceeds 99, display "99+". Mark notification as read when it scrolls into view |
| | Notification List API | 6 | • API to return the last N notifications<br>• Support unread count |
| | Notification Table & Modal UI | 10 | • UI to display dropdown/modal of recent notifications<br>• Clicking shows full content or opens the related page |
| | Notification Routing & Links | 4 | • Each notification can link to a booking, chat, etc.<br>• Routes to the corresponding page when clicked |
| | Mark as Read | 2 | • API to mark notification as read (triggered when notification scrolls into view)<br>• Update unread bubble |
| | Mark all as Read | 2 | • API to mark all notifications as read<br>• Update unread bubble count |
| | Pagination | 4 | • API pagination support (cursor or offset-based)<br>• UI infinite scroll or load more functionality |
| | Unread Bubble Logic | 2 | Show unread dot/badge until opened or marked as read |
| | Template system | 2 | Can use handlebars or mustache |
| WebSocket Integration | WebSocket Setup (Backend) | 4 | Setup socket server (e.g., socket.io) and user session handling |
| | Redis Pub/Sub Integration | 4 | Use Redis for centralized pub/sub between server instances |
| | Event Emitters for Notifications | 4 | Emit events for new messages, bookings, cancellations, etc. |
| | WebSocket Frontend Listener | 3 | Listen to WebSocket on client - Update UI in real-time |
| | Reconnect, Heartbeat, Cleanup | 3 | Handle edge cases, socket cleanup, reconnect logic |
| | Store notifications in DB | 2 | Persist notifications for history / reload |
| | WebSocket connection (client) | 2 | Connect to backend WebSocket server |
| | WebSocket message handling | 2 | Push incoming notifications to local state |

---

## Email Service

**Total Hours: 9**

| Task | Hours | Description | Notes |
|------|-------|-------------|-------|
| Basic Email Template | 4 | • Develop reusable HTML email template (header, footer, shared styles)<br>• Store in a third-party email platform (e.g., SendGrid or Mailchimp) to support future Zapier phase-out | |
| Offline Message Notification | 5 | • Email notification to fleet admin or user when a message is received and the recipient is offline<br>• Include both English and Dutch versions<br>• Trigger & Data 3hr, template 2hr | <span style="color: red;">Clarify with client if this is needed</span> |

---

## Super Admin Notification

**Total Hours: 61**

**Note:** 
- Trigger notification, prepare data: 2hr (for event-based triggers)
- Cron job triggers: 4-7hr (varies by complexity - see individual notes for breakdown)
- Email template: 2hr
- In-app template: 1hr
- Testing handled separately in overview section

| Feature | Task | Hours (Trigger / Email / In-App) | Total Hours | Message Template | Channels | Notes |
|---------|------|-------------------------------|-------------|------------------|----------|-------|
| New Booking | Any booking confirmed | 2 / 2 / 1 | 5 | 🎉 New booking confirmed for [Camper Name] (€[Amount]) | In-app, Email | Many messages |
| Cancellation | Booking cancelled | 2 / 2 / 1 | 5 | ⚠️ [Traveller Name] cancelled a booking for [Camper Name] | In-app, Email | **Requires cancellation feature** for users/fleet owners |
| Dispute Opened | Fleet owner or traveller files dispute | 2 / 2 / 1 | 5 | 🚨 Dispute opened on booking #[ID] — review details | In-app, Email | **Requires dispute filing feature** - button/workflow for fleet owner or traveller to initiate dispute |
| Late Return | Vehicle not checked in on time | 5 / - / 1 | 6 | 🕒 [Camper Name] return overdue — contact owner | In-app, Push (Not in this phase) | **Requires cron job** to check for overdue returns<br><br>**Implementation steps:**<br>1. Query bookings past return date/time (1hr)<br>2. Handle timezone per location (1hr)<br>3. Check return/check-in completion status (1hr)<br>4. Prevent duplicate notifications (0.5hr)<br>5. Trigger notifications to Super Admin (0.5hr)<br>6. Buffer for edge cases (1hr) |
| Deposit Held | Deposit flagged or held | 2 / - / 1 | 3 | 💰 Deposit for booking #[ID] held for review | In-app | **Requires deposit management workflow** for flagging/holding deposits |
| New Fleet Owner | New account created | 2 / - / 1 | 3 | 👋 [Owner Name] has registered a new fleet account | In-app | |
| Fleet Sign Up Pending | Fleet owner registration pending approval | 2 / 2 / 1 | 5 | 📋 [Owner Name] has registered a new fleet account — pending approval | In-app, Email | **Requires fleet approval workflow**<br>• Email both fleet admin and system admin<br>• Includes English and Dutch versions |
| Fleet Sign Up Approval | Fleet owner account approved | 2 / 2 / 1 | 5 | ✅ [Owner Name]'s fleet account has been approved | In-app, Email | **Requires fleet approval workflow**<br>• Email both fleet admin and system admin<br>• Includes English and Dutch versions |
| Inactive Fleet Owner | No new bookings in 30+ days | 4 / 2 / 1 | 7 | 📉 [Owner Name] has had no bookings in 30 days — check listing performance | In-app, Email | **Requires cron job** to check for inactive fleet owners<br>**Requires booking tracking system**<br><br>**Implementation steps:**<br>1. Query fleet owners with last booking >30 days ago and calculate days inactive (1.5hr)<br>2. Filter active fleet owners only (0.5hr)<br>3. Prevent duplicate notifications (0.5hr)<br>4. Trigger notifications with days inactive data (1hr)<br>5. Buffer for edge cases (0.5hr) |
| Revenue Milestone | Platform crosses revenue threshold | 5 / - / 1 | 6 | 💶 Congratulations! Platform reached €100K GBV this quarter | In-app | **Requires cron job** to check revenue thresholds<br>**Requires revenue tracking/reporting system**<br><br>**Implementation steps:**<br>1. Query total GBV for current period and calculate revenue metrics (2hr)<br>2. Compare against milestone thresholds (1hr)<br>3. Track which milestones already notified (1hr)<br>4. Trigger notification when threshold crossed (0.5hr)<br>5. Buffer for edge cases (0.5hr) |
| System Alert | Error in payout, data sync, or API | 2 / - / 1 | 3 | ⚙️ Error in payout sync for [Owner Name] — review in dashboard | In-app | Not able to cover all cases. In this case, we will cover payout error. Others can be added later with additional cost |
| Message Response Delay | Owner/Traveller response >24h | 4 / - / 1 | 5 | ⏳ [Owner Name] has delayed responses — average reply time 27h | In-app | **Requires cron job** to check for delayed responses<br>**Requires message tracking system**<br>When the owner takes too much time to reply to a traveler<br><br>**Implementation steps:**<br>1. Query messages and calculate average response time per owner/traveller (1.5hr)<br>2. Handle timezone considerations (0.5hr)<br>3. Prevent duplicate notifications (0.5hr)<br>4. Trigger notifications with delay metrics (0.5hr)<br>5. Buffer for edge cases (1hr) |
| Owner Feedback Alert | Negative review (<3★) | 2 / - / 1 | 3 | ❗ [Traveller Name] left a 2★ review for [Camper Name] | In-app | **Requires review/rating system**<br>When a traveler leaves a negative review to the fleet owner |
| New Destination Added | CMS update | - | - | 🌍 New travel destination page published: [Country Name] | In-app | **Requires CMS integration**<br><span style="color: red;">To clarify with client</span> |

---

## Fleet Owner Notification

**Total Hours: 69**

**Note:** 
- Trigger notification, prepare data: 2hr (for event-based triggers)
- Cron job triggers: 2-4hr (varies by complexity - see individual notes for breakdown)
- Email template: 2hr
- In-app template: 1hr
- Testing handled separately in overview section

| Feature | Task | Hours (Trigger / Email / In-App) | Total Hours | Message Template | Channels | Notes |
|---------|------|-------------------------------|-------------|------------------|----------|-------|
| New Booking | Camper booked | - | 2 | 🎉 New booking confirmed for [Camper Name] ([Start Date] – [End Date]) | In-app, Email | **Repeat** - Reuses Super Admin templates |
| Booking Update | booking updated | 2 / 2 / 1 | 5 | | In-app, Email | **Requires booking update feature** for modifying bookings |
| Cancellation | Booking cancelled | - | 2 | | In-app, Email | **Repeat** - Reuses Super Admin templates<br>**Requires cancellation feature** |
| Upcoming Pickup | 24h before check-in | 4 / - / 1 | 5 | 🚗 [Traveller Name] picking up [Camper Name] tomorrow | In-app, Push (Not in this phase) | **Requires cron job**<br><br>**Implementation steps:**<br>1. Query bookings with pickup date 24h from now (1hr)<br>2. Handle timezone per location (1hr)<br>3. Filter confirmed bookings only (0.5hr)<br>4. Prevent duplicate notifications (0.5hr)<br>5. Trigger notifications to Fleet Owner (1hr) |
| Check-In Started | Traveller begins Goform | - | 0 | 📝 [Traveller Name] started check-in for [Camper Name] | In-app | Check-in is a process when owner and traveler start together in the office<br>**Requires check-in form/process (Goform)**<br><span style="color: red;">Clarify with client if this notification is needed</span> |
| Return Due Soon | 24h before return | 4 / - / 1 | 5 | 🕒 [Camper Name] due for return tomorrow — prepare inspection form | In-app, Push (Not in this phase) | **Requires cron job**<br><br>**Implementation steps:**<br>1. Query bookings with return date 24h from now (1hr)<br>2. Handle timezone per location (1hr)<br>3. Filter active bookings only (0.5hr)<br>4. Prevent duplicate notifications (0.5hr)<br>5. Trigger notifications to Fleet Owner (1hr) |
| Late Return | Return not completed after due time | 3 / 1 / 0 | 4 | ⚠️ [Traveller Name] has not returned [Camper Name] yet | Push (Not in this phase), Email | **Requires cron job**<br><br>**Implementation steps:**<br>1. Query bookings past return date/time (1hr)<br>2. Handle timezone per location (1hr)<br>3. Check return/check-in completion status (1hr)<br>4. Prevent duplicate notifications (0.5hr)<br>5. Trigger notifications to Fleet Owner (0.5hr) |
| Deposit Released | After successful return | 1 / - / 1 | 2 | 💰 Deposit for [Traveller Name] released successfully | In-app | what is the process to release deposit?<br>**Requires deposit release workflow/process** |
| Deposit Held | Pending issue | - | 2 | ⚠️ Deposit for booking #[ID] held due to reported damage | In-app | **Repeat** - Reuses Super Admin templates<br>we will have to implement the workflow<br>• Deposit is released automatically after N days, unless the fleet admin press a button to report issue<br>• Fleet admin report damage<br>• Deposit is held and the email goes out<br>• Need to have a list of pending issues to resolve? yes, displays a list of bookings with special status filter<br>**Requires cron job** to check for automatic release after N days<br>**Requires damage reporting feature** - button/workflow for fleet admin to report damage<br>**Requires pending issues management UI** - list of bookings with special status filter |
| New Message | Traveller sends message | - | 2 | 💬 New message from [Traveller Name] | In-app, Push (Not in this phase) | **Repeat** - Reuses Traveler templates<br>**Requires messaging system** (likely already exists)<br><span style="color: red;">May need email - clarify with client</span> |
| Unreplied Message Reminder | >2h without response | 3.5 / - / 1 | 4.5 | ⏳ You have an unanswered message from [Traveller Name] | In-app | **Requires cron job**<br>**Requires message tracking system**<br><span style="color: red;">May need email - clarify with client</span><br><br>**Implementation steps:**<br>1. Query messages without replies >2h (1hr)<br>2. Filter messages for current fleet owner (0.5hr)<br>3. Handle timezone considerations (0.5hr)<br>4. Prevent duplicate notifications (0.5hr)<br>5. Trigger notifications to Fleet Owner (1hr) |
| Low Response Rate Alert | <80% replies in 24h | 4 / 2 / 1 | 7 | 📭 Your response rate has dropped — faster replies help boost your ranking! | In-app, Email | **Requires cron job**<br>**Requires response rate calculation system**<br><br>**Implementation steps:**<br>1. Query messages for each fleet owner (1hr)<br>2. Calculate response rate (replies/received) in last 24h (1hr)<br>3. Filter owners with <80% response rate (0.5hr)<br>4. Prevent duplicate notifications (0.5hr)<br>5. Trigger notifications with response rate data (1hr) |
| Maintenance Reminder | Based on mileage/dates | 4 / - / 1 | 5 | 🔧 [Camper Name] service due in 7 days | In-app | **Requires cron job**<br>**Requires maintenance tracking system** - mileage/dates tracking<br><br>**Implementation steps:**<br>1. Query vehicles with maintenance due in 7 days (1hr)<br>2. Check mileage or date-based maintenance schedules (1hr)<br>3. Filter by fleet owner's vehicles (0.5hr)<br>4. Prevent duplicate notifications (0.5hr)<br>5. Trigger notifications to Fleet Owner (1hr) |
| Insurance Expiry Warning | 14 days before expiry | 3.5 / 2 / - | 5.5 | 🛡️ Insurance for [Camper Name] expires soon | Email | **Requires cron job**<br>**Requires insurance tracking system**<br><br>**Implementation steps:**<br>1. Query vehicles with insurance expiring in 14 days (1hr)<br>2. Filter by fleet owner's vehicles (0.5hr)<br>3. Handle timezone considerations (0.5hr)<br>4. Prevent duplicate notifications (0.5hr)<br>5. Trigger email notifications to Fleet Owner (1hr) |
| Payout Completed | Payout to owner processed | 2 / 2 / 1 | 5 | 💸 Your payout of €[Amount] has been processed | In-app, Email | **Requires payout system** - Stripe Connect or equivalent |
| New Review Received | Traveller leaves review | 2 / - / 1 | 3 | ⭐ [Traveller Name] left a 5★ review on [Camper Name] | In-app | **Requires review/rating system** |
| Negative Review Alert | <3★ | - / 2 / - | 4 | ⚠️ Low rating received for [Camper Name] — check feedback | In-app, Email | **Partial Repeat** - Reuses Super Admin trigger and in-app templates (2hr). New email template (2hr)<br>**Requires review/rating system** |
| Performance Summary | Weekly digest | 4 / 2 / - | 6 | "📊 This week: [X] bookings, [Y]% occupancy, [Z]€ revenue" | Email | **Requires cron job**<br>**Requires reporting/KPI system** to calculate bookings, occupancy, revenue<br><br>**Implementation steps:**<br>1. Query bookings for current week per fleet owner (1hr)<br>2. Calculate occupancy rate, revenue metrics (1hr)<br>3. Aggregate performance data (0.5hr)<br>4. Prevent duplicate notifications (weekly) (0.5hr)<br>5. Trigger email notifications with performance summary (1hr) |

---

## Traveler Notification

**Total Hours: 48**

**Note:** 
- Trigger notification, prepare data: 1-2hr (for event-based triggers, reuses some work: Repeat: 2hr)
- Cron job triggers: 2hr (simpler than Super Admin/Fleet Owner as they're per-traveler, see individual notes for breakdown)
- Email template: 2hr
- In-app template: 1hr
- Testing handled separately in overview section

| Feature | Task | Hours (Trigger / Email / In-App) | Total Hours | Message Template | Channels | Notes |
|---------|------|-------------------------------|-------------|------------------|----------|-------|
| Booking Confirmation | Booking successful | - | 2 | 🎉 Your booking is confirmed! [Camper Name] is ready for your trip. | In-app, Email | **Repeat** - Reuses Super Admin templates |
| Booking Updated | Booking Updated | - | 2 | | In-app, Email | **Repeat** - Reuses Fleet Owner templates<br>**Requires booking update feature** |
| Cancellation | Booking cancelled | - | 2 | | In-app, Email | **Repeat** - Reuses Super Admin templates<br>**Requires cancellation feature** |
| Booking Reminder | N days before trip start & 1 day before return | 4 / 2 / 0 | 6 | • Upcoming trip: Triggers a reminder email N days before the booking start date<br>• Camper return today: Triggers one day before the return<br>• The value of N is stored in a config file<br>• Applies system-wide (not configurable per fleet admin) | Email | **Requires cron job**<br><br>**Implementation steps:**<br>1. Query bookings N days before start date (1.5hr)<br>2. Query bookings 1 day before return date (1.5hr)<br>3. Handle timezone per location (0.5hr)<br>4. Prevent duplicate notifications (0.25hr)<br>5. Trigger email notifications to Traveler (0.25hr) |
| Deposit Reminder | 24h before pickup | 2 / 2 / 1 | 5 | 💰 Don't forget your deposit due at pickup | In-app, Email | **Requires cron job**<br><br>**Implementation steps:**<br>1. Query bookings with pickup date 24h from now (0.5hr)<br>2. Handle timezone per location (0.5hr)<br>3. Filter confirmed bookings only (0.25hr)<br>4. Prevent duplicate notifications (0.25hr)<br>5. Trigger notifications to Traveler (0.5hr) |
| Pickup Reminder | 24h before | 2 / 2 / 0 | 4 | 🚐 Ready to roll? Your camper pickup is tomorrow at [Time/Location]. | Push (Not in this phase), Email | **Requires cron job**<br><br>**Implementation steps:**<br>1. Query bookings with pickup date 24h from now (0.5hr)<br>2. Handle timezone per location (0.5hr)<br>3. Filter confirmed bookings only (0.25hr)<br>4. Prevent duplicate notifications (0.25hr)<br>5. Trigger notifications to Traveler (0.5hr) |
| Check-In Started | Owner begins process | - | 0 | 📝 Please complete your check-in with [Owner Name] | In-app | Check-in is a process when owner and traveler start together<br>**Requires check-in form/process (Goform)**<br><span style="color: red;">Clarify with client if this notification is needed</span> |
| Trip Ongoing | Mid-trip check | 0 / - / 0 | 0 | "✨ Enjoying your trip? Remember, we're here for support if needed." | Push (Not in this phase) | **Requires cron job**<br>No templates needed (simple push notification) |
| Return Reminder | 24h before due | 2 / - / 1 | 3 | ⏰ Time to head back — your return for [Camper Name] is tomorrow. | In-app, Push (Not in this phase) | **Requires cron job**<br><br>**Implementation steps:**<br>1. Query bookings with return date 24h from now (0.5hr)<br>2. Handle timezone per location (0.5hr)<br>3. Filter active bookings only (0.25hr)<br>4. Prevent duplicate notifications (0.25hr)<br>5. Trigger notifications to Traveler (0.5hr) |
| Late Return Alert | If overdue | 0 / - / 0 | 0 | ⚠️ You're late returning [Camper Name]. Contact [Owner Name] immediately. | Push (Not in this phase) | **Requires cron job**<br>No templates needed (simple push notification)<br><span style="color: red;">May need in-app or email - clarify with client</span> |
| Deposit Released | After return approved | - / 2 / - | 4 | 💰 Your deposit has been refunded — thanks for travelling with us! | In-app, Email | **Partial Repeat** - Reuses Fleet Owner trigger and in-app templates (2hr). New email template (2hr)<br>**Requires deposit release workflow/process** |
| New Message | Owner replies | - | 2 | 💬 [Owner Name] replied to your message | In-app, Push (Not in this phase) | **Repeat** - Reuses Fleet Owner templates<br>**Requires messaging system** (likely already exists) |
| Unreplied Message Reminder | No reply in 2h | - | 3 | ⏳ Did you see [Owner Name]'s message? | In-app | **Repeat** - Reuses Fleet Owner templates<br>**Requires cron job**<br>**Requires message tracking system**<br><span style="color: red;">May need email - clarify with client</span> |
| Trip Review Request | 24h after return | 2 / 2 / 1 | 5 | ⭐ How was your trip with [Camper Name]? Leave a quick review. | Email, In-app | **Requires cron job**<br>**Requires review/rating system**<br><br>**Implementation steps:**<br>1. Query completed bookings 24h after return (0.5hr)<br>2. Filter bookings without reviews (0.25hr)<br>3. Handle timezone considerations (0.25hr)<br>4. Prevent duplicate notifications (0.5hr)<br>5. Trigger notifications to Traveler (0.5hr) |
| Special Offers / Promotions | Seasonal or geo-targeted | - | 0 | 🍂 10% off autumn camper adventures — book your next trip today! | Email | Not sending notifications within the system. Client should use Mailchimp or similar marketing email software<br>**Requires contact sync service to Mailchimp** |
| Travel Inspiration | Automated monthly | - | 0 | 🚐 Discover 5 new routes to explore in Portugal this season. | Email | Not sending notifications within the system. Client should use Mailchimp or similar marketing email software<br>**Requires contact sync service to Mailchimp** |
| Abandoned Booking Reminder | Periodically checks for abandoned bookings | 4 / 2 / 0 | 6 | • Periodically checks for abandoned bookings<br>• Sends a reminder email to the user encouraging them to complete the booking<br>• Includes English and Dutch versions<br>• The X-hour delay is fixed system-wide and not configurable by fleet admins | Email | **Requires cron job**<br>**Requires abandoned booking tracking system**<br><br>**Implementation steps:**<br>1. Query abandoned bookings (no completion after X hours) (1.5hr)<br>2. Check time since abandonment (1hr)<br>3. Prevent duplicate notifications (1hr)<br>4. Trigger email notifications to Traveler (0.5hr) |
| Finalize Payment Email Template | Email sent when final payment is processed | 1 / 3 / 0 | 4 | • Email sent when final payment is processed<br>• Includes English and Dutch versions | Email | New<br>**Requires payment processing integration** |
| End trip Email Template | Email sent when trip ends | - | 0 | • Email both fleet admin and system admin<br>• Includes English and Dutch versions | Email | **Repeat** - Reuses Trip Review Request templates<br>**Requires trip completion workflow** |

---

## Total Hours: 246

