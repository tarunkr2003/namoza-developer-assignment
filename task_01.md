### Task 01: GTM Event Schema & Tracking Strategy

**1. Complete GTM Event Schema**
Below is the structured event schema covering all key interactions on the OrthoNow website.

| Event Name | Trigger Type | Key Parameters (Min. 3) | GA4 Target Report / Audience |
| --- | --- | --- | --- |
| `booking_step_complete` | Custom Event (`booking_step_complete`) | `step_number`, `step_name`, `clinic_location` | Funnel Exploration (Drop-off analysis) |
| `appointment_booked` | Custom Event (`appointment_booked`) | `clinic_location`, `specialty`, `patient_type` | Conversions / Primary Google Ads Import |
| `click_call_now` | Click - Just Links (Matches `tel:`) | `page_url`, `clinic_location`, `button_position` | Event Count / High-Intent Audience Builder |
| `click_whatsapp` | Click - Just Links (Matches `wa.me`) | `page_url`, `time_on_page`, `device_category` | Event Count / High-Intent Audience Builder |
| `download_patient_guide` | Custom Event (Fires on form success) | `guide_name`, `user_phone_provided`, `page_url` | Lead Generation / Mid-Funnel Audience |
| `clinic_page_view` | Page View (URL contains `/clinic/`) | `clinic_location`, `referring_page`, `session_source` | Pages and Screens / Local Remarketing |
| `blog_scroll_depth` | Scroll Depth (25%, 50%, 75%, 90%) | `percent_scrolled`, `blog_category`, `article_title` | Engagement Report / Retargeting Audience |

**2. Tracking the 3-Step Booking Form (Funnel Drop-off)**
Native GTM form tracking (like Form Submission triggers) cannot reliably track multi-step forms built with modern JavaScript, as the page does not reload between steps.

To track this accurately:

* **Trigger Mechanism:** I will instruct the front-end developer to write a custom `dataLayer.push` that fires exactly when a user successfully validates and completes a step to move to the next one. GTM will use a **Custom Event Trigger** looking for the event name `booking_step_complete`.
* **GA4 Surface:** In GA4, I will create a **Funnel Exploration** report. I will define Step 1, Step 2, and Step 3 by filtering the `booking_step_complete` event by its `step_number` parameter. This will visually map out the exact drop-off percentage between selecting a clinic (Step 1), entering details (Step 2), and confirming (Step 3).



**3. Actual dataLayer Push JSON (Front-End Briefing)**
Here is the exact JSON format the front-end developer must push when a user completes Step 2 (Entering name/phone/preferred date):

```json
window.dataLayer = window.dataLayer || [];
window.dataLayer.push({
  "event": "booking_step_complete",
  "step_number": 2,
  "step_name": "patient_details_entered",
  "clinic_location": "Bengaluru - Indiranagar",
  "specialty": "Knee Pain",
  "preferred_date": "2026-07-05"
});

```

**4. Google Ads Conversion Import**
I would import the **`appointment_booked`** (the final completion of the 3-step form) as the primary conversion action into Google Ads.

* **Why?** This is a bottom-of-funnel, high-intent action. If we optimize the campaign for top-of-funnel actions (like `click_call_now` or `click_whatsapp`), the Google Ads bidding algorithm might feed us cheap clicks from users who never actually book. Optimizing for the final form completion ensures the algorithm bids on users most likely to generate actual revenue for OrthoNow.