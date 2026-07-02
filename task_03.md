TASK 03: INTEGRATION DESIGN
End-to-End Architecture

Since HubSpot uses email as the primary field for contact deduplication, using a normal HubSpot embedded form with only a phone number can create duplicate contacts when an existing patient submits the form again. To avoid this, I would build a custom integration using Make (formerly Integromat) as the automation layer.

1. Front-End & Google Ads

When a user submits the HTML form, a JavaScript Fetch API request sends the patient's Name, Phone Number, and Clinic Preference to a Make Webhook. At the same time, the website pushes a GTM dataLayer event, which triggers the 'consultation_form_submitted' Google Ads conversion on the client side.

2. CRM Logic (HubSpot)

After receiving the form data, Make searches HubSpot contacts using the Phone Number instead of email. If an existing contact is found, it updates the record by changing the Lead Status to "New Enquiry". If no matching contact exists, Make creates a new contact and stores the Name, Phone Number, Clinic Preference, and sets the Source as "Google Ads - Consultation Landing Page."

3. WhatsApp Automation

Once the contact has been successfully created or updated in HubSpot, Make sends an HTTP POST request to the Karix WhatsApp Business API, passing the patient's phone number along with the approved WhatsApp template so that the confirmation message is sent automatically.

Biggest Failure Point & Fallback

The most likely point of failure is communication between Make and the Karix API. If the Karix service is unavailable or the API request times out, the WhatsApp confirmation message will not be delivered.

To handle this, I would configure an Error Handler in Make for the Karix module. If the request fails, the workflow can automatically move to a fallback route that either:

sends a normal SMS using another provider such as MSG91 or Twilio, or
sends an automated Slack notification to the OrthoNow reception team so they can manually contact the patient as soon as possible.

This ensures that the patient still receives a confirmation even if WhatsApp delivery fails.

Monitoring the 2-Minute SLA

The 2-minute WhatsApp delivery SLA could be affected by high workflow queues in Make during peak traffic or by delays in the Karix API.

To monitor this, I would use a delivery webhook from Karix that sends the delivery status back to Make. Make would then update a custom HubSpot property called "WhatsApp_Delivered_Time." A calculated property in HubSpot can measure the time difference between the contact's Create Date and WhatsApp_Delivered_Time. Finally, a dashboard report can automatically highlight any records where the delivery time exceeds 120 seconds, making it easy to identify and investigate SLA breaches.