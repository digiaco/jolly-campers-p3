# V3 Development Estimate

**Total Hours: 1487**

---

## DevOps

### Servers

| Task | Hours | Description | Status |
|------|-------|-------------|--------|
| Server Configuration | 8 | Configure development and production environments. Store and retrieve keys and secrets using a secret manager. | ✓ Checked |
| Dockerize Containers | 4 | Dockerize all relevant services and applications for consistent local and production environments. | ✓ Checked |
| CI/CD | 4 | Set up CI/CD pipeline for automated testing, builds, and deployment to staging/production. | ✓ Checked |

**Subtotal: 16 hours**

---

## System

### Image Service

| Task | Hours | Description | Status |
|------|-------|-------------|--------|
| Create | 8 | • Upload to cloud storage<br>• Front-end cropper and file type validation<br>• Secure write access<br>• Assumes backend does not generate multiple resolutions | ✓ Checked |
| Read | 6 | • Serve images through CDN with caching<br>• Secure read access | ✓ Checked |

**Subtotal: 14 hours**

---

### Cron Service

| Task | Hours | Description | Status |
|------|-------|-------------|--------|
| Partial Payment Auto-Charge | 8 | • Automatically charges the remaining balance when the booking is less than 30 days away<br>• Assumes integration with the payment system | ✓ Checked |
| Automate Payment for Fleet Admin | 8 | • Automatically calculates and transfers earnings to each fleet admin based on completed bookings<br>• Runs on a fixed schedule (e.g., daily or weekly)<br>• Assumes integration with Stripe Connect or equivalent for payout<br>• Includes fee calculation (e.g., subtracting platform commission)<br>• Logs and stores payout records for reporting and audit purposes | ✓ Checked |

**Subtotal: 16 hours**

---

### Language Service

| Task | Hours | Description | Status | Notes |
|------|-------|-------------|--------|-------|
| Language Service | 8 | • Backend key-value mapping system<br>• Used for both system language translation (e.g., feature labels) and fleet-customized content<br>• Used for content, such as vehicle title, description<br>• It's not used for database ENUM type<br>• It's not used for UI language | ✓ Checked | Need to reconsider if this is really needed. Consider translation libraries and automation if 3+ languages are needed. |
| Available Languages | 1 | • List of supported languages<br>• Internal database access only (not exposed in admin UI) | ✓ Checked | |
| UI Language | 0 | Use library like React-i18next. The hour is broken down into each feature. | ✓ Checked | |

**Subtotal: 9 hours**

---

### Payment

| Task | Hours | Description | Notes |
|------|-------|-------------|-------|
| Stripe Credit Card Integration | 8 | Setup and test payment flow with Stripe Elements | We probably need to implement partial payment that will also take some time |
| iDEAL Payment Integration | 5 | Enable and test iDEAL as an alternative method | Stripe as a single integration. Stripe is your main payment processor. You create a PaymentIntent (or Checkout Session), and you tell Stripe which payment methods you want to accept (e.g., "card", "ideal"). Stripe handles the routing to either card processing or iDEAL, depending on what the customer selects. |
| Booking Flow Update | 6 | Connect booking confirmation to trigger Stripe payment | |
| Stripe Connect Setup | 16 | Enable multi-fleet payout via Stripe Connect | |
| Fleet Admin Onboarding | 0 | KYC support, assuming we're using the FE component from Stripe to handle | |
| Fleet Admin Management | 12 | UI and backend for managing connected accounts | |
| Error Handling & Logging | 6 | Implement transaction logging and error management | |
| Booking Payment Deposit | 8 | Apply a percentage-based deposit if the reservation is made more than 30 days before pickup | |
| Balance Due (30 Days) | 0 | Balance is automatically charged 30 days before pickup (handled in cron auto-charge) | |
| Refund Handling | 8 | Supports partial or full refunds based on cancellation timing:<br>• 0–30 days before pickup → No refund<br>• 30–60 days before pickup → 50% refund<br>• 60+ days before pickup → Full refund | |

**Note:** Sum is 69 hours for payment system. Should allocate time for testing while developing, setup on dev and prod environment, documentation, and unit tests. Payment integration could take ~80 hours (2 weeks). If including partial payments and other things - ~1 more week (±40 hours).

**Subtotal: 69 hours**

---

### Price Service

| Task | Hours | Description | Status |
|------|-------|-------------|--------|
| Quote calculation | 20 | • Calculates pricing based on date range, base price, vehicles, add-ons, rules, tax, and discount codes<br>• Includes rules such as seasonal pricing and length-of-booking adjustments<br>• Applies traveler service fee (fixed or percentage, configured in super admin panel)<br>• Applies fleet admin service fee (fixed or percentage, configured in super admin panel)<br>• Returns detailed line items, total price, and deposit breakdown | ✓ Checked |

**Subtotal: 20 hours**

---

### Availability Service

| Task | Hours | Description | Status | Notes |
|------|-------|-------------|--------|-------|
| Campervan Availability | 16 | • Retrieves availability dates for a specific campervan within a given date range<br>• Used in booking date pickers, search filters, and booking validation | ✓ Checked | |
| Turnaround time | 2 | • Adjust logic to exclude buffer days after each booking<br>• Use fleet admin's configured turnaround value<br>• Also handle time for cleaning<br>• Campervan sometimes needs cleaning and won't be ready by 10am need to block off the pickup time before 10<br>• 2hr prep for each campervan<br>• Fleet admin need to have the option to define the prep/cleaning hour | ✓ Checked | |

**Subtotal: 18 hours**

---

## Public Webpage

**Note:** Provide iframe support for fleet admins

### Browse Campervan

| Task | Hours | Description | Status |
|------|-------|-------------|--------|
| Campervan List | 8 | • Display a list of available campervans based on default criteria or current availability window<br>• Replace FE API<br>• Paginated | ✓ Checked |
| Campervan search | 8 | • Allow users to search by keyword, pickup location, or campervan name/title<br>• Simple database query-based search (no external service like Elasticsearch)<br>• Replace FE API<br>• Similar to what we have now | ✓ Checked |
| Campervan filter | 8 | • Filter campervans by location and date range<br>• Additional filters: vehicle type, seating capacity, sleeping capacity, features, and comfort options (based on current list)<br>• Replace existing frontend API with new backend implementation<br>• Similar to what we have now | ✓ Checked |
| Campervan sort | 4 | • Sort by position or price<br>• Similar to what we have now | ✓ Checked |
| Campervan detailed view | 14 | • Show full details of a selected campervan, including name, images, description, vehicle details, amenities, location, extras, daily price, and calendar availability<br>• Replace the current frontend API call with the new backend<br>• Pull all required details from the new system instead of Wheelbase Pro | ✓ Checked |
| Alternative Date Suggestion | 12 | • When no campervan is found for the selected dates, suggest alternative options<br>• Search for available campervans in the same city for nearby or future dates<br>• Assuming we use simple algorithm<br>  ○ Trigger a new query to search for available campervans:<br>  ○ In the same location<br>  ○ For the same duration<br>  ○ Within a search window (e.g., +7 days from original check-in) | ✓ Checked |
| Quotes | 4 | • Estimate the total booking price for a campervan based on:<br>  ○ Date range<br>  ○ Selected extras/add-ons<br>  ○ Fleet-specific pricing rules and fees<br>• Used in booking summary, search results, and campervan details<br>• Display the price breakdown. The calculation uses the price service listed in system section. | ✓ Checked |

**Subtotal: 58 hours**

---

### Locations

| Task | Hours | Description | Status |
|------|-------|-------------|--------|
| Location list | 6 | Retrieve and display a list of fleet locations (cities or pickup points). Used in search filters, campervan listings, and booking forms | ✓ Checked |

**Subtotal: 6 hours**

---

### Addons / Equipments / Items

| Task | Hours | Description | Status |
|------|-------|-------------|--------|
| Addons / Equipments / items | 6 | API to receive a list of items, show detailed information. | ✓ Checked |

**Subtotal: 6 hours**

---

### Others

| Task | Hours | Description | Status |
|------|-------|-------------|--------|
| WBP to New App API data transformation | 24 | • Replace WheelbasePro backend with the in-house backend api<br>• For each api call, match data, transform data if needed | ✓ Checked |

**Subtotal: 24 hours**

---

### User Account

| Task | Hours | Description | Status |
|------|-------|-------------|--------|
| Sign Up | 4 | • Email-based sign-up plus SSO support (next line)<br>• Email verification via link or code | ✓ Checked |
| SSO Support | 8 | Supports:<br>• Google<br>• iCloud | ✓ Checked |
| Profile on Signup | 2 | • Basic profile creating on sign up, first name, last name, no image<br>• Without 2FA | ✓ Checked |
| Profile photo add/update | 8 | • Support profile photo<br>• User FE to preview, compress image and crop<br>• Does not transcode image to various resolutions | |
| Login | 3 | User account login using email and password | ✓ Checked |
| Forgot Password | 5 | • User enters email address<br>• System sends HTML email with password reset link | ✓ Checked |
| Header avatar | 2 | • Display name when user is signed in<br>• When user is not signed in, show login option<br>• Use short form of first name. Ie, Henry would show H. | ✓ Checked |
| Manage Payment Methods | 24 | • List saved payment methods (e.g., cards)<br>• Add new card using Stripe<br>• Remove saved cards<br>• Securely handle and display data via Stripe integration | ✓ Checked |
| Personal Details & Security | 8 | • Allow user to update name<br>• Allow user to change password securely, ask for current password and new password.<br>• Without 2FA | ✓ Checked |

**Subtotal: 64 hours**

---

### User Dashboard

| Task | Hours | Description | Status |
|------|-------|-------------|--------|
| Dashboard Home | 8 | User landing dashboard showing high-level overview (e.g., upcoming bookings, quick actions) | |
| Booking Lists | 8 | Display upcoming and past bookings linked to the logged-in user | ✓ Checked |
| Booking Detailed View | 16 | • View booking details<br>• Add "Update" and "Cancel" buttons | ✓ Checked |
| Booking Detailed View Magic Token | 4 | Generate and validate a magic token to allow users to access booking details without signing in (e.g., from confirmation or reminder emails) | ✓ Checked |
| Update Booking | 30 | • Modify booking details<br>• Pre-fill page with existing data<br>• Recalculate pricing<br>• Adjust payment via Stripe (charge or refund)<br>• Apply booking adjustment policy<br>• Send booking modification email<br>• Respect refund policy | ✓ Checked |
| Cancel Booking | 8 | • Cancel booking with confirmation<br>• Apply refund rules (none, partial, or full)<br>• Send cancellation email | ✓ Checked |
| Continue Abandoned Booking | 8 | • User clicks email link to resume an abandoned booking<br>• Assuming we don't need magic token for this<br>• Load booking details<br>• Allow checkout<br>• Check campervan availability | ✓ Checked |
| Booking Message List | 6 | • List of booking threads<br>• Show unread indicator (e.g., red dot) on threads with unread messages<br>• Similar to bookings, before we get into the message thread of each booking | ✓ Checked |

**Note:** Update Booking has many edge cases such as: if the full price was paid and the user updated to a lower price; if the partial price was paid and the user updated to a lower price; if the user updated the date but it is already booked by another user. Need to find out all the cases and take them all into account.

**Subtotal: 88 hours**

---

### Checkout

| Task | Hours | Description | Status |
|------|-------|-------------|--------|
| Custom checkout page | 20 | • Build a custom front-end checkout flow<br>• Integrate Stripe as the payment gateway<br>• Embed Stripe's UI components to ensure credit card data goes directly to Stripe (PCI compliant)<br>• Handle payment errors and user feedback<br>• Display pricing summary including extras and taxes<br>• Store booking and payment reference info in the database<br>• Assumes Stripe handles transaction and receipt details | ✓ Checked |
| Partial Payment | 12 | • Enable 50% upfront payment if booking is made more than 30 days in advance<br>• Remaining balance is due before pickup (typically before customer arrives)<br>• System sends payment reminder email N days before the booking date<br>• For bookings ≤ 30 days, full payment is required at time of booking<br>• Skip annual, next phase. Designed for annual policy: 50% upfront, 50% late. Assuming no annual membership.<br>• Assuming no membership plan concept.<br>• Display information of upcoming payment and past payment for this booking. | ✓ Checked |

**Subtotal: 32 hours**

---

### Payment Unit Tests

| Task | Hours | Description | Status |
|------|-------|-------------|--------|
| Unit Test | 16 | Write unit tests for payment-related flows:<br>• Booking creation with payment<br>• Booking update with price adjustment and refund/charge<br>• Booking cancellation with refund handling<br>Validate transaction recording and Stripe integration behavior | ✓ Checked |

**Subtotal: 16 hours**

---

### Customer to Fleet Review

| Task | Hours | Description | Status |
|------|-------|-------------|--------|
| Review category | 4 | • Up to 5 review categories, ie, cleanliness, value for money, staff<br>• Defined systemwide in database, not definable in admin panel<br>• Each category has a 5 point system | ✓ Checked |
| Write review | 16 | Customer rate review:<br>• Email review link after each trip<br>• Magic link<br>• 5 point system for each category<br>• And a text only comment<br>• Recalculate the average points for each category and the total score<br>• Total score is 10 point system, similar to booking.com, simple formula<br>• UI interface to write review<br>• Review is based on campervan-booking | ✓ Checked |
| Read review | 12 | Read a list of reviews:<br>• Show total score, 10 point system, should be pre-calculated<br>• Show point for each category, 5 point system<br>• Show review<br>• Pagination<br>• Ordered by review date<br>• Assuming there is no other filter or sort in this phase | ✓ Checked |
| Read review summary | 6 | Similar to the review summary in booking.com, one on top of apartment detailed view and one at the bottom. | ✓ Checked |

**Subtotal: 38 hours**

---

### AI Chat Assistance - Customers with Reservations

**Note:** Cannot reuse the messaging app between the customer and the fleet owner

| Task | Hours | Description |
|------|-------|-------------|
| AI note About the vehicle and rental | 4 | A text field note about the vehicle, such as FAQ, important information. Text note that the AI can read. |
| Data compilation | 8 | • Compile relevant information for the customer from the database, such as vehicle spec, vehicle schedule (in case the user needs to extend the rental)<br>• Creates an API |
| User Identity & Permissions Check | 3 | • Validate user ID/session before sharing booking data<br>• Prevents unauthorized access to other users' bookings |
| Chat Session Context Builder | 6 | Middleware in n8n or backend that fetches info and composes prompt - e.g., "User has booking #XYZ, here's the vehicle summary…" |
| OpenAI Integration (via n8n) | 4 | Use n8n's OpenAI node for prompt/response - Configure error handling, streaming (if needed), and retry logic |
| AI Message Routing Logic | 4 | Use logic in n8n to detect intent: info request, modify, cancel, etc. - Trigger corresponding workflow branch |
| Custom Action Handler (Optional) | 0 | Assuming not in this phase. Handle special cases: e.g., extend booking, report issue, contact fleet owner - May call internal APIs or trigger human follow-up |
| n8n Workflow Integration | 4 | Set up flexible workflows in n8n for escalation, fallback, or integrations (e.g., Slack, email, Intercom, Twilio) |

**Subtotal: 33 hours**

---

### AI Chatbot Support - General User

| Task | Hours | Description |
|------|-------|-------------|
| Intercom integration | 0 | General AI help, included in general support. Assuming custom data is not required to pass into Intercom. |

**Subtotal: 0 hours**

---

### Privacy and Data Management

**Status:** TBD

---

### Analytics

| Task | Hours | Description |
|------|-------|-------------|
| Google analytic integration | 2 | Integration on public website page, without custom event tracking. |

**Subtotal: 2 hours**

---

### Reports

| Task | Hours | Description | Notes |
|------|-------|-------------|-------|
| Financial report | 8 | • Start date, end date, itemized rentals (booking id, start date, end date, amount, tax)<br>• Total amount and total tax in the duration<br>• Group by vehicle | Need to understand the content |
| Financial report - print to pdf | 6 | Print financial report to pdf, simple format | |
| Usage report | 0 | Same as financial report? Does it need to show the usage of all additional items? | |

**Subtotal: 14 hours**

---

### Check In

| Feature | Hours | Description |
|---------|-------|-------------|
| Traveler check in pages | 0 | |

**Subtotal: 0 hours**

---

### Sync Marketing Email Contact

| Task | Hours | Description |
|------|-------|-------------|
| Sync Mailchimp Contact | 0 | Sync email contacts from app to marketing email like Mailchimp |

**Subtotal: 0 hours**

---

### IFrame Embed

**Status:** TBD

---

## User Dashboard & Fleet Admin

### Message Center

| Task | Hours | Description | Status | Notes |
|------|-------|-------------|--------|-------|
| Twilio integration | 2 | Set up Twilio account and messaging service configuration | ✓ Checked | |
| Twilio Backend integration | 8 | Implement server-side logic:<br>• User identity mapping<br>• Access token generation<br>• Chat room creation<br>• Webhook endpoints | ✓ Checked | Twilio Conversations API - Replacement for Programmable Chat. Lets create chat "conversations" that can include multiple participants (users). Supports messaging over SMS, WhatsApp, and Web chat. |
| Booking Message Frontend Chat UI | 16 | Design and build booking-level messaging UI:<br>• Text-only support<br>• Display message history<br>• Enable live sending and receiving of messages | ✓ Checked | Text only |
| Image Support | 0 | | | |
| Initial load | 8 | Initial loads, load the last N messages initially. | ✓ Checked | |
| History chat loading | 6 | On scrolling back it loads older history, calling the API and render older messages. | ✓ Checked | |
| Webhook Handling | 4 | Process Twilio events such as:<br>• User joined/left<br>• Message sent/deleted | ✓ Checked | |
| Authentication Linking | 3 | • Securely link logged-in users to Twilio<br>• Generate and manage Twilio access tokens | ✓ Checked | |

**Subtotal: 47 hours**

---

### Media File Support in Chat

**Note:** Support photos and videos (Photo + Video Files in Chat)

| Task | Hours | Description |
|------|-------|-------------|
| Frontend Upload UI | 6 | File select UI, preview, validation, progress display |
| Twilio Media Upload | 4 | Use Twilio Conversations media API to upload files |
| Display Media in Chat | 4 | Render thumbnails for images, embedded video for supported formats |
| Backend Security | 3 | Validate media types, proxy file handling, optional signed URLs |
| Storage & Retention Handling | 2 | Handle media expiration/lifecycle if required |

**Subtotal: 19 hours**

---

### Twilio Video Calling

| Task | Hours | Description |
|------|-------|-------------|
| Twilio Video Room Setup | 6 | Setup Twilio Video credentials, token generation, and room API |
| User Interface for Call | 6 | Call button, camera/mic toggle, full screen UI, call duration, etc. |
| Signaling Integration | 4 | Detect incoming/outgoing call requests (e.g., via chat or alert modal) |
| Device Handling | 5 | Camera/microphone permission handling, fallback/error states |
| Video Call Event Handling | 2 | Track join/leave events, timeouts, participant errors |

**Subtotal: 23 hours**

---

### General Support

| Task | Hours | Description |
|------|-------|-------------|
| Intercom integration | 4 | • General intercom integration. Assume system admin manages and create the content.<br>• With AI support |

**Subtotal: 4 hours**

---

## Fleet Admin Panel

**Note:** Assuming mobile responsive is not a requirement. Support tablet form, desktop form. Full mobile responsive support for messaging.

### Account

| Task | Hours | Description | Status | Questions |
|------|-------|-------------|--------|-----------|
| Sign Up | 4 | • Email-based sign-up only<br>• Email verification via link or code<br>• Reduced time from similar feature from user | ✓ Checked | Does this require approval? |
| Create personal profile | 2 | • First and last name<br>• Phone (without validation)<br>• Initial phase to have one admin for each fleet, later to support more users and roles for each fleet<br>• Build it in the way so that it's easy to change architecturally | ✓ Checked | |
| Create fleet profile | 4 | • Fleet business name<br>• Website<br>• Phone number (without validation)<br>• Email (without validation)<br>• Timezone should be associated to a location<br>• Default locale<br>• Default currency | ✓ Checked | |
| Login | 2 | • User account login using email and password<br>• Reduced time from similar feature from user | ✓ Checked | |
| Forgot Password | 2 | • User enters email address<br>• System sends HTML email with password reset link<br>• Reduced time from similar feature from user | ✓ Checked | |
| UI Language | 3 | • Enable language switching in this section of the admin UI<br>• Assumes support for two languages<br>• Does not include translation of content | ✓ Checked | |

**Subtotal: 17 hours**

---

### Role

**Note:** It would be useful for a super admin to have access to any fleet admin panel. For example, the admin user can switch to the fleet panel in the dropdown and see the same thing that a specific fleet admin sees. This can be useful for troubleshooting some issues that may have occurred in any fleet admin panel and checking if everything is working properly or any other issues or questions.

| Task | Hours | Description | Status |
|------|-------|-------------|--------|
| Role | 4 | • Secondary role, manager or staff<br>• Assign role(s) to each controller and test | ✓ Checked |

**Subtotal: 4 hours**

---

### Permission

| Task | Hours | Description | Status |
|------|-------|-------------|--------|
| Permission | 4 | • Manager or staff can't view financial information<br>• Assign permissions(s) to each controller and test | ✓ Checked |

**Subtotal: 4 hours**

---

### Fleet Profile

| Task | Hours | Description | Status |
|------|-------|-------------|--------|
| Fleet on Creating Approval Process | 2 | • On creating a fleet, the default status is set to pending, an admin must approve<br>• Block access until account is approved. Not seeing any feature related to the fleet at all.<br>• Does not include the UI interface for admin to approve or reject | ✓ Checked |
| Update fleet profile | 2 | • Fleet business name<br>• Website<br>• Phone number (without validation)<br>• Email (without validation)<br>• Default locale<br>• Default currency | ✓ Checked |
| UI Language | 1 | • Enable language switching in this section of the admin UI<br>• Assumes support for two languages<br>• Does not include translation of content | |

**Subtotal: 5 hours**

---

### Campervan Management

| Task | Hours | Description | Status | Notes |
|------|-------|-------------|--------|-------|
| List Campervans | 12 | • Display a list of all campervans associated with the current fleet admin<br>• Includes pagination and basic detail preview | ✓ Checked | |
| Search | 0 | Search by name (deferred to future phase) | ✓ Checked | |
| Filter | 0 | Filtering campervans (deferred to future phase) | ✓ Checked | |
| Create Campervan | 8 | • Create and register a new campervan with basic configuration<br>• Includes form validation and data model integration | ✓ Checked | |
| Batch Create Campervan | 0 | | | |
| Update Campervan | 8 | Edit existing campervan details via form interface | ✓ Checked | |
| View Campervan | 8 | • View campervan details in a read-only format<br>• Calendar availability view is excluded in this phase | ✓ Checked | |
| Delete Campervan | 2 | Soft delete (marks campervan as inactive without removing from the database) | ✓ Checked | |
| Base Specifications | 16 | • Assumes all fields are free-form text or numbers (no lookup tables or presets)<br>• Vehicle type (enum)<br>• Seatbelt, Year, length (numbers)<br>• Make, model, trim, class (string)<br>• Transmission, Fuel Type (enum)<br>• km/l, GVWR, Dry weight, amps (number)<br>• Height, width, length with hitch, box length (number / 100 or string)<br>• Water tank, fuel, propane, black water, gray water (number /100 or string)<br>• Sleep, sleep adult, sleep children (numbers)<br>• King bed, queen bed, full bed, twin bed (numbers)<br>• Internal info:<br>  - License plate (string)<br>  - Replacement value (number)<br>  - Mileage (number) | ✓ Checked | Need to know what fields are needed or not needed from WBP page |
| Unit preference | 8 | • Same options as in WBP<br>  - Distance<br>  - Length<br>  - Weight<br>  - Liquids<br>• Fleet admin has the ability to choose unit<br>• Render chosen units for vehicle | ✓ Checked | |
| Import year, make, model, and trim | 0 | Assuming these are free text fields, no relational data | | |
| Photos Management | 16 | • Add, remove, reorder campervan photos<br>• No auto-enhance or category-specific handling (e.g., interior vs. exterior) | ✓ Checked | |
| YouTube Video Link Support | 8 | • Support YouTube video link for media collection<br>• Assuming it does not validate the link<br>• Display video on UI if the content is a YouTube link<br>• Backend and db support | ✓ Checked | |
| Feature Flags | 8 | • Toggle key campervan features (e.g., toilet, kitchen sink, radio) using boolean flags<br>• Support feature category<br>• Up to 50 features, 5 categories<br>• Boolean (true/false only), without any other data type<br>• DB setup, UI & BE create and update support | ✓ Checked | |
| Associate Locations/Features | 4 | Assign campervan to locations and feature categories | ✓ Checked | |
| Tax Association | 0 | Remove this, to simplify things, we associate tax rate to a location, not a vehicle | ✓ Checked | |
| Base Rate & Deposit | 4 | • Set base daily rate and required security deposit<br>• Assuming deposit is charged on picking up the car, not through the app.<br>• On the day of pick up, have the system to charge the deposit fee, handle failure, in this case, another form of payment | ✓ Checked | When to charge deposit? How to charge? - to revisit |
| Block Date Range | 16 | • Block availability for specified date ranges for a vehicle<br>• Add, edit, and remove blocked periods | ✓ Checked | |
| Upcoming Bookings | 4 | • List upcoming bookings tied to a specific campervan<br>• Next N bookings, not all | ✓ Checked | |
| Content Language | 4 | Multilingual support for name, highlight, and description content. Does not include admin UI language. | ✓ Checked | |
| UI Language | 4 | • Enable language switching in this section of the admin UI<br>• More content, take longer time<br>• Assumes support for two languages<br>• Does not include translation of content<br>• Assuming enum like vehicle type is limited to 15 max each and it is stored in a file, not in a database | Updated | Assume it's configurable in the system variable |

**Subtotal: 130 hours**

---

### Location Management

| Task | Hours | Description | Status | Notes |
|------|-------|-------------|--------|-------|
| List Locations | 6 | • Display all locations for the fleet admin<br>• No pagination or search in this phase | ✓ Checked | |
| Create Location | 10 | • Create a new location<br>  ○ Use geo service to auto-fetch coordinates. Assuming we cover the address that is translateable to coordinates by Google Map Api.<br>  ○ Handle map display<br>• Handle notes (assuming this is not needed)<br>  ○ Associate location with a user<br>• Defined tax rate<br>• Bank account is linked to fleet admin, not individual location like how it's done in WBP | ✓ Checked | |
| Update Location | 4 | Edit location information | ✓ Checked | |
| Delete Location | 3 | • Soft delete with association checks<br>• Handle error messages for linked data, ie, if there is any campervan associated to this location then the location cannot be deleted | ✓ Checked | |
| Hours of operation | 0 | Assuming we use the pick up and drop off time | ✓ Checked | Assuming we use the pick up and drop off time |
| Pickup/Dropoff Hours | 12 | • Unable to pickup / drop off flag<br>• Pickup Begins Hour and Min (15 mins interval)<br>• Pickup Ends Hour and Min (15 mins interval)<br>• Basic validation that Pickup Ends cannot be before Pick up Begins<br>• Add logic for pick up and drop off on checking out a vehicle | ✓ Checked | |
| Special Hours | 6 | • Name, date, available for pickup, available for drop off, repeats<br>• List, create, edit<br>• Add logic on checking out a vehicle | ✓ Checked | |
| Content Language | 0 | Assuming content language is not needed here, same as WBP | ✓ Checked | |
| UI Language | 2 | • Enable language switching in this section of the admin UI<br>• More content, take longer time<br>• Assumes support for two languages<br>• Does not include translation of content | ✓ Checked | |
| Timezone | 12 | • Each location is assigned a specific timezone<br>• Dates and times selected by customers are interpreted in the timezone of the pickup location<br>• All displayed information (e.g., booking start/end, reminders) respects the location's local time<br>• Cron services (e.g., reminders, auto-charges) execute based on the location's timezone rather than system/server time | ✓ Checked | |

**Subtotal: 55 hours**

---

### Addons Management

| Task | Hours | Description | Status | Notes |
|------|-------|-------------|--------|-------|
| List Addons | 8 | • List all addons and fees<br>• No pagination, filtering, or search in this phase<br>• Assuming inventory management is not needed | ✓ Checked | |
| Create Addon | 8 | Create new addon or fee item | ✓ Checked | |
| Update Addon | 4 | Edit existing addon | ✓ Checked | |
| Delete Addon | 2 | Soft delete addon with validation | ✓ Checked | |
| Photo | 4 | • Support 1 image per addon<br>• No downsizing<br>• Crop image on frontend | | |
| Location Availability | 2 | Select which locations this addon is available for | ✓ Checked | |
| Charge Type | 0 | Assuming only a fixed flat fee is supported in this phase | ✓ Checked | |
| Daily Fee Calculation | 4 | • If enabled, fee is charged per day<br>• No support for min/max days | ✓ Checked | |
| Quantity available | 2 | The max quantity user can get for each booking. Assuming there is no inventory tracking. | ✓ Checked | |
| Hidden Feature | 3 | Option to hide addon from customer-facing UI but still associate internally | ✓ Checked | |
| Tax Rate | 4 | • Assign tax rate per addon<br>• Default is no tax | ✓ Checked | |
| Position | 2 | Define display order of addons manually, if the number is the same as another addon, the order is random between the addons | ✓ Checked | |
| Content Language | 4 | • Localized name and description fields<br>• Does not include admin UI language. | ✓ Checked | |
| UI Language | 3 | • Enable language switching in this section of the admin UI<br>• More content, take longer time<br>• Assumes support for two languages<br>• Does not include translation of content | ✓ Checked | |
| Applies to Rental Type | 4 | Specify applicable vehicle types (e.g., campervan, converted van) | ✓ Checked | |

**Subtotal: 54 hours**

---

### Other Fees Management

| Task | Hours | Description | Status | Notes |
|------|-------|-------------|--------|-------|
| Other Fees | 8 | Handle Items like:<br>• Cleaning fee (prep fee, by camper), assume it's configurable by each fleet admin, fleet admin setting<br>• Fleet admin service fee (by commission), assume it's configurable in the system variable<br>• Customer service fee (by a set amount), assume it's configurable in the system variable<br>• Delivery fee (not needed) | | Discussion. Do we need any of this? Maybe prep fee. |
| State Fee | 0 | Deferred to a future phase | ✓ Checked | |
| Notes to guests | 0 | Deferred to a future phase | ✓ Checked | |
| Delivery | 0 | Deferred to a future phase | ✓ Checked | |
| Mileage | 20 | • Type of fee<br>• Unit: Mile or km<br>• Name of the fee<br>• Unlimited or charge<br>• Tax rate<br>• Tier pricing / Matching Tier<br>• Based free mile per day / reservation<br>• Pricing levels list, add, delete<br>• Input for miles based on the reservation, auto calculate the additional fees | ✓ Checked | |
| Generator | 4 | Similar to Mileage, but without the unit of mile / km | ✓ Checked | |
| UI Language | 2 | • Enable language switching in this section of the admin UI<br>• More content, take longer time<br>• Assumes support for two languages<br>• Does not include translation of content | ✓ Checked | |
| Pick up and return time dynamic pricing | 24 | • 30 mins interval<br>• Assuming this is bounded by the pick up and drop off hours, the option is not available if it's before and after the pickup/drop off hours<br>• Dynamic pricing for pick up and return time<br>• Set the price and the time range<br>• The same price for each day, so no dynamic pricing for day and time<br>• Displays a list of dynamic pricing<br>• Create a dynamic pricing range (from and to hh:mm), validates duplication and overlapping<br>• Update and delete a dynamic price range<br>• Displays the dynamic pricing on pick up and drop off time<br>• Update the time on price breakdown<br>• Each location has its own dynamic pricing configuration | ✓ Checked | Only within business hour? |
| Handoff Limit | 0 | The number of handoffs per person per day. Assume we do this in future month, this will require staff schedule management to understand the schedule for each staff and location, calculate the bandwidth etc | ✓ Checked | Future phase |

**Subtotal: 58 hours**

---

### Customer Management

| Task | Hours | Description | Status |
|------|-------|-------------|--------|
| List | 8 | • Display a paginated list of customers with name, email, and phone<br>• Only shows customers belonging to the current fleet admin | ✓ Checked |
| Search | 6 | • Search customers by name<br>• Simple database query (no advanced filtering or fuzzy search) | ✓ Checked |
| Create | 6 | • Add a new customer<br>• Fields: first name, last name, email (used as primary key), phone, birthdate, locale/language, and address | ✓ Checked |
| Update | 4 | Update customer profile information | ✓ Checked |
| User & Fleet Customer Association | 6 | • Each fleet manages its own customer list<br>• A fleet customer can be linked to a user account<br>• A single user may be associated with multiple fleet customers<br>• Need to have a user table and a fleet user table<br>• Use email as a unique ID | ✓ Checked |
| UI Language | 2 | • Enable language switching in this section of the admin UI<br>• Assumes support for two languages<br>• Does not include translation of content | |

**Subtotal: 32 hours**

---

### Booking Management

| Task | Hours | Description | Status | Notes |
|------|-------|-------------|--------|-------|
| List | 8 | • List existing rentals and the abandon reservations from customers<br>• Pagination<br>• Without search | ✓ Checked | |
| Filter | 8 | • Created between<br>• Departed between<br>• Status: cancelled, current<br>• Assuming there is no following filters: change requested, handed off, starts soon, approved, renter cancelled, expired, renter withdrew, declined | ✓ Checked | Status: approved - needed, handed off - nice to have, cancelled status is enough, don't need renter cancelled |
| Predefined List - Quote List | 4 | • Waiting on renter - defined by user, triggers abandoned email notification<br>• To simplify, draft / quote are the same, just one status, does not send email to user and it does not triggered abandoned email notification<br>• Assuming no concept of expired or rejected<br>• Assuming no InstaMatch concept | ✓ Checked | |
| Predefined List - Current & Upcoming List | 4 | Current and Upcoming | ✓ Checked | |
| Search | 0 | Assuming search is not included in this scope | ✓ Checked | |
| Create | 12 | • Admin to create rental on behalf of the customer.<br>• Select a date range, vehicle, and addons, etc.<br>• Accept discount code<br>• Not possible with iDEAL payment here, so an alternative payment must be provided.<br>• Associate to a customer, customer does not necessary have to verify email. Create a new customer inline if the customer does not exist.<br>• Will try to reuse the existing UI component | ✓ Checked | iDEAL payment not possible |
| Detailed view | 10 | • View the details, campervan, addons<br>• Without activities, drivers, calendar, note, documents and files, emails, reviews, and inspection photos<br>• Show information that users view | ✓ Checked | |
| Detailed view - transaction list | 4 | The transaction history of the booking | ✓ Checked | |
| Update booking | 8 | Date range, addons, etc | ✓ Checked | |
| Update booking - update charge | 8 | Calculate the difference, make the necessary charge or refund | ✓ Checked | |
| Offline Payment | 0 | Support offline payment like Cash, check, others. How will they pay the commission? May have to disable the feature. Assuming this will be done in future phase | ✓ Checked | To discuss |
| Credit card payment | 8 | Support credit payment. Admin uses the customer's credit card to pay the campervan | ✓ Checked | |
| iDEAL payment | 0 | Not possible from fleet admin side | ✓ Checked | |
| Reservation Note | 4 | One text field, does not track history | ✓ Checked | |
| UI Language | 2 | • Enable language switching in this section of the admin UI<br>• Assumes support for two languages<br>• Does not include translation of content | ✓ Checked | |

**Subtotal: 80 hours**

---

### Insurance Management

| Task | Hours | Description | Status | Notes |
|------|-------|-------------|--------|-------|
| List | 8 | • Display all insurance plans<br>• No pagination, filter, or search in this phase | ✓ Checked | Need to discuss more, if they don't have their own insurance. JollyCampers to provide some basic insurance plans. For limited countries only. |
| Create | 8 | • Add new insurance plan<br>• Name and per-day pricing only | ✓ Checked | |
| Update | 4 | Edit insurance plan details | ✓ Checked | |
| Delete | 2 | • Soft delete<br>• Does not affect existing bookings that already applied the plan | ✓ Checked | |
| Tax | 2 | Associate tax rate to the insurance plan | ✓ Checked | |
| Content Language | 4 | Multilingual support for name and details content. Does not include admin UI language. | ✓ Checked | |
| UI Language | 2 | • Enable language switching in this section of the admin UI<br>• Assumes support for two languages (add 1h per additional language)<br>• Does not include translation of content | ✓ Checked | |

**Subtotal: 30 hours**

---

### Fleet to Customer Review

| Task | Hours | Description | Status | Questions |
|------|-------|-------------|--------|-----------|
| Customer Reviews | 12 | • Assuming this is associated to a booking<br>• user -> booking -> review<br>• Each user / fleet user can have multiple review<br>• Each review has 5 points system, datetime, and a comment.<br>• When we select a user, we can view the reviews for the user<br>• Read only<br>• Without sub category | ✓ Checked | Should this be associated to a booking? Should this be shared among all the fleets? |
| Review a customer | 6 | • Give review based on a reservation<br>• 5 points system and a text note. | ✓ Checked | |
| UI Language | 1 | • Enable language switching in this section of the admin UI<br>• Assumes support for two languages<br>• Does not include translation of content | ✓ Checked | |

**Subtotal: 19 hours**

---

### Calendar View

| Task | Hours | Description | Status | Notes |
|------|-------|-------------|--------|-------|
| Calendar / Vehicle list | 28 | • Use a third party library that support calendar timeline view, such as full calendar or syncfusion<br>• Render a list of campervans, assuming the library handles large list of campervans<br>• Note that there is an additional license charge for the library<br>• Show approved status bookings only<br>• Show blocked dates<br>• Controls, next week, previous week, monthly view, weekly view, assuming it's supported by the calendar library. | ✓ Checked | |
| Reservation Overview Modal | 8 | • Displays reservation overview, quote, without note<br>• Button to reservation details | ✓ Checked | |
| Choose date | 4 | Choose date and jumps to the selected date | ✓ Checked | |
| Resource date range select | 6 | • Select a date range on a resource with mouse<br>• Displays a modal with option to make the car unavailable<br>• Does not have start quote button | ✓ Checked | |
| Calendar view to support pickup and drop off time | 0 | Pick up and drop off time of the day for each vehicle. Assuming this is a feature from the library | ✓ Checked | |
| UI Language | 0 | • Enable language switching in this section of the admin UI<br>• Assumes support for two languages<br>• Does not include translation of content<br>• Assuming the library supports UI translation | ✓ Checked | |

**Subtotal: 46 hours**

---

### Discount Codes Management

| Task | Hours | Description | Status |
|------|-------|-------------|--------|
| List | 8 | List all discount codes for the fleet admin. No pagination, search, or filtering in this phase | ✓ Checked |
| Create | 12 | • Define code (English alphabet only, no special characters or spaces)<br>• Associate discount code with one or more locations<br>• Select discount type: flat amount or percentage<br>• Apply to either daily rate or total price<br>• Define start date and end date<br>• Option to apply based on shopping dates or travel dates<br>• Assumptions:<br>  ○ No restriction by length of stay<br>  ○ No filtering by vehicle country, RV type, or first-time renters<br>  ○ No cap on redemptions per user or in total<br>  ○ One discount per booking maximum | ✓ Checked |
| Update | 4 | • Update discount code details<br>• Changes do not affect quotes or bookings that already used the discount | ✓ Checked |
| Delete | 3 | • Deactivate (soft delete) the discount<br>• Discount remains valid for existing bookings that already applied it | ✓ Checked |
| Apply Discount | 6 | • Apply a discount to a quote<br>• Discount amount is fixed at the time of quote creation<br>• Changing the code later does not retroactively modify existing quotes | ✓ Checked |
| UI Language | 2 | • Enable language switching in this section of the admin UI<br>• Assumes support for two languages (add 1h per additional language)<br>• Does not include translation of content | ✓ Checked |

**Subtotal: 35 hours**

---

### Settings

| Task | Hours | Description | Status | Notes |
|------|-------|-------------|--------|-------|
| Min nights | 0 | Min night for each reservation. Already included in Minimum Booking Nights Rule | ✓ Checked | |
| Charge by day or night | 0 | Assuming it's charge by day. Move charge by night option to future phase. | ✓ Checked | Charged by night is required, similar to hotel, pick up at 5pm, return at 10am. Charge by day, 10am pickup, 10am drop off |
| Turnaround time | 2 | • Adds a fleet admin–configured buffer (in full days) between bookings<br>• Ensures sufficient time for cleaning and preparation after a return<br>• Booking system automatically blocks availability based on the specified turnaround duration<br>• Applies at the fleet level (not configurable per vehicle)<br>• Add a numeric input in the fleet admin settings panel<br>• Store and retrieve the turnaround value per fleet<br>• Default to 0 if unset<br>• Does not include the logic of vehicle availability | ✓ Checked | |
| Default currency | 2 | • Set default currency from an available list<br>• Assuming currency setting is tied to a fleet profile, not a location | ✓ Checked | |
| UI Language | 3 | • Enable language switching in this section of the admin UI<br>• Assumes support for two languages (add 1h per additional language)<br>• Does not include translation of content | ✓ Checked | |

**Subtotal: 7 hours**

---

### Rules Management

| Task | Hours | Description | Status |
|------|-------|-------------|--------|
| List | 8 | • Display all rules in a simple list view<br>• No search, filter, or pagination functionality in this phase | ✓ Checked |
| Base Rule Logic | 20 | • Shared rule setup components<br>• Name the rule<br>• Repeat annually option<br>• Enable/disable toggle (not conditional by search dates)<br>• Calendar-based date range selection<br>• Apply rules to specific days of the week<br>• Uses Wheelbase Pro (WBP) rule behavior as reference | ✓ Checked |
| Date & Range Price Adjustments | 8 | • In the date range, set price to, increase / decrease percentage, increase / decrease amount<br>• Apply to vehicles, without vehicle type filter<br>• Name the rule<br>• Repeat annually option<br>• Enable/disable, without only for certain search dates<br>• Apply to date range, and day(s) of the week<br>• Use WBP as reference | ✓ Checked |
| Minimum Booking Nights | 8 | • Min booking nights field<br>• Apply to vehicles, without vehicle type filter<br>• Name the rule<br>• Repeat annually option<br>• Enable/disable, without only for certain search dates<br>• Apply to date range, and day(s) of the week<br>• Use WBP as reference | ✓ Checked |
| Advanced Pricing (Length-Based) | 8 | • Price adjustment based on length of booking, at least N days, then increase/decrease price by X percentage/amount. Support multiple adjustments.<br>• Apply to vehicles, without vehicle type filter<br>• Name the rule<br>• Repeat annually option<br>• Enable/disable, without only for certain search dates<br>• Apply to date range, and day(s) of the week<br>• Use WBP as reference | ✓ Checked |
| Utilization & Optimization | 0 | • Reserved for future phase<br>• Advanced logic based on fleet occupancy or availability windows | ✓ Checked |
| UI Language | 2 | • Enable language switching in this section of the admin UI<br>• Assumes support for two languages (add 1h per additional language)<br>• Does not include translation of content | ✓ Checked |

**Subtotal: 54 hours**

---

### Reports

| Task | Hours | Description | Status |
|------|-------|-------------|--------|
| Monthly Rental Report | 16 | • Display the report online<br>• Monthly summary of rentals, showing a breakdown per booking: base price, add-ons, service fees, platform fees, etc.<br>• Includes month selector for viewing reports from different periods | ✓ Checked |
| Download PDF Report | 8 | Generate pdf file based on monthly rental report | ✓ Checked |

**Subtotal: 24 hours**

---

### Tax Management

| Task | Hours | Description | Status |
|------|-------|-------------|--------|
| CRUD | 16 | List, Create, Update, Soft delete (Soft delete prevents future association) | ✓ Checked |
| Default | 2 | Default tax rate, on creating a fleet admin, a default tax rate is created for the fleet admin. In Setting section. | ✓ Checked |
| UI Language | 1 | • Enable language switching in this section of the admin UI<br>• Assumes support for two languages (add 1h per additional language)<br>• Does not include translation of content | ✓ Checked |

**Subtotal: 19 hours**

---

### Payout Management

| Task | Hours | Description |
|------|-------|-------------|
| Create | 12 | Assuming there is only one payout method for each fleet management. Find out what payout methods to support |
| Update | 8 | |

**Subtotal: 20 hours**

---

### Others

| Feature | Hours | Status | Description |
|---------|-------|--------|-------------|
| Stripe Connect | 0 | | Backend creates a Stripe Connect account (via API) with type express. Generate a Stripe onboarding link using account_links.create. Fleet admin clicks the link and is taken to the Stripe-hosted onboarding UI. After completion, Stripe redirects them back to your platform. Store their Stripe account ID for future payouts. Optionally, query their account status (e.g., charges_enabled, payouts_enabled) to display onboarding progress |
| Payout | 4 | | Charge the customer, and Stripe automatically routes the correct share to the fleet admin's account via a method called "destination charges" or "transfer to connected account." |
| Reservation preference | 0 | | Future phase |
| Policies and documents | 0 | | Contract for booking, insurance policy. Need to think about how they would receive the pre-filled documents |
| Delivery drivers | 0 | | Future phase |
| Cosigned owners | 0 | | Future phase |
| Checkout questions | 0 | | Future phase |
| Integrations | 0 | | Future phase |
| Reporting | 0 | | Future phase, need some reports. |

**Subtotal: 4 hours**

---

### Damage Check

**Note:** Assuming this is done on a web app. Can consider making an iOS app release for this feature.

| Task | Hours | Description |
|------|-------|-------------|
| Check-in and check-out buttons | 2 | In booking list, add a feature to add vehicle check-in and check-out photos |
| List of photo thumbnails | 8 | List of photos thumbnails for viewing |
| Check-in and check-out selection | 4 | Select between check-in and check-out photos |
| Add photo | 4 | Take photo with mobile phone |
| Upload photo | 8 | Upload the photo to cloud, such as s3, may encode more to reduce photo size |
| Delete photo | 2 | |
| View photo | 4 | View full size photo on selecting a thumbnail |
| iOS app | 0 | Optional |
| Note | 0 | Note for each photo? Log with timestamp? |

**Subtotal: 32 hours**

---

### Maintenance Management

| Task | Hours | Description |
|------|-------|-------------|
| Create/edit an event | 12 | • Date, duration, vehicle, note, status, type (repair, maintain, etc)<br>• Block dates to make rental unavailable<br>• Damage maintenance is the same concept |
| Maintenance list view | 10 | Past and upcoming events with filter (vehicle) and pagination |
| Maintenance event calendar view | 10 | Use the same calendar to display maintenance events |
| View a maintenance event | 4 | |
| DB & model setup | 6 | |
| Email reminder logic (cron) | 6 | Remind N days before the event |

**Subtotal: 48 hours**

---

### Fleet Admin Support

| Task | Hours | Description |
|------|-------|-------------|
| Intercom integration | 2 | • General intercom integration. Assume system admin manages and create the content.<br>• This is available when a fleet admin signs into the dashboard<br>• Pass in basic fleet admin information, such as user info, to Intercom<br>• Not including additional work for passing custom data to intercom |

**Subtotal: 2 hours**

---

### Calendar Sync

| Task | Hours | Description |
|------|-------|-------------|
| Base Sync Module | 10 | • Delete the list of blocked dates<br>• Get the list of blocked dates and apply to the vehicle<br>• Download calendar file and parse, assuming this is standardized<br>• Cron service to update periodically, ie, every 10 mins |
| Goboony calendar integration | 4 | • From Goboony to the system<br>• Add and update the calendar url |

**Subtotal: 14 hours**

---

### Digital Handover

**Description:** A digital handover process for pick-up and return:

**Pick-up: Step-by-Step**
- Fleet owner starts the process on their phone through the Jolly Campers platform
- Traveller receives a notification that the pick-up has started
- Complete the form together – All information and photos are entered on the fleet owner's device
- Review details together including mileage, fuel level, inventory, and any existing damages
- Review & confirm – Both parties review the form and confirm the pick-up

**Return: Step-by-Step**
- Open the booking in Jolly Campers account
- Tap 'Finalise Return' to begin the process
- Follow the guided steps together with the Traveller
- Inspect the camper – Document cleanliness, any new damages, or changes since pick-up
- Review agreements – Verify fuel level, mileage, return time, and any extras agreed upon at pick-up
- Settle any additional costs
- Manage the deposit – If everything is in order → release the deposit. If there's an issue → hold the deposit until it's resolved.
- Important: Always ensure the Traveller stays until the full return process is completed
- Once submitted, receive a confirmation email with a link to the completed form

**Features:**
- Rental agreement (non-digital)
- Check ID
- Search for damage
- Video externally and internally
- Fleet owner should update everything
- Photo for now
- Support damage photo update for customers

**Status:** TBD

---

## Admin

**Note:** Does not support multi languages

### AdminJS Integration

| Task | Hours | Description | Status |
|------|-------|-------------|--------|
| AdminJS Integration | 12 | | ✓ Checked |

**Subtotal: 12 hours**

---

### User Management

| Task | Hours | Description | Status |
|------|-------|-------------|--------|
| User List, Read, Update, Delete | 4 | • List all users, assuming email is the UID<br>• Read, Update, Delete a user. Direct DB mapping, without join. | ✓ Checked |

**Subtotal: 4 hours**

---

### Fleet Users

| Task | Hours | Description | Status |
|------|-------|-------------|--------|
| LCRUD | 0 | Not needed | ✓ Checked |

**Subtotal: 0 hours**

---

### Fleet Management

| Task | Hours | Description | Status |
|------|-------|-------------|--------|
| List, Read, Update, Delete | 4 | • List all fleets<br>• Read each fleet detail<br>• Update a fleet (basic info)<br>• Delete - Set a fleet status to deleted<br>• Default UI to model association without additional logic | ✓ Checked |
| Approve Fleet | 2 | • Approve - Set a fleet status from pending to active<br>• Email fleet admin and admin | ✓ Checked |
| Backend | 4 | Build the backend to support fleet in the future | |

**Subtotal: 10 hours**

---

### Campervan Feature/Amenity Management

**Note:** Superuser access only

| Task | Hours | Description |
|------|-------|-------------|
| CRUD | 16 | List, create, update, delete, group, icon |
| Multiple language support | 6 | Associate name to a language |
| Tax | 0 | Associate to tax if needed. Not applicable. |

**Subtotal: 22 hours**

---

### Currency

| Task | Hours | Description | Status |
|------|-------|-------------|--------|
| Basic | 8 | • Support for Euro (EUR) and British Pound Sterling (GBP)<br>• Structure built in the database for future multi-currency support<br>• No UI access or customization in this phase (currencies are fixed)<br>• Currency setting is shared globally (not per fleet admin)<br>• Includes handling currency in pricing, calculations, and display logic throughout the application<br>• Includes logic for formatting, symbol display, and internal value storage<br>• Payout in fleet default currency<br>• Does not include setting the default currency on fleet admin settings | ✓ Checked |

**Subtotal: 8 hours**

---

### Vehicle Type

| Task | Hours | Description |
|------|-------|-------------|
| Vehicle Type | 2 | Campervan, Type A, B, C, etc. Database access only |

**Subtotal: 2 hours**

---

## Total Hours: 1487

---

## Additional Notes

- Landing page, filter by geo location
- Targeting EU countries
- Fleet with its own set of languages
- Each EU country has its own VAT tax rate
- Additional charge on returning vehicle, associate to booking
- Fleet owners mandatory

