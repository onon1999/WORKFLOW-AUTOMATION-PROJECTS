Case Study
Deskflow has recently launched a free interactive ROI calculator on their website. When a visitor
completes the calculator, a webhook fires containing their name, work email, company name,
estimated team size, and the calculated ROI value. The volume is roughly 40–60 submissions per
day.
Currently, all submissions land in a Google Sheet and a sales rep manually reviews each one every
morning. Your VP of Sales wants this automated: each submission should be enriched with company
data, scored based on fit, and routed to the appropriate CRM owner — all without a human in the
loop for standard cases.
Requirements — Build this as a working Make.com scenario:
• Trigger: Custom Webhook module receiving the ROI calculator submission payload (name,
work email, company, team_size, roi_value).
• Enrichment: Use an HTTP Request module to call a company enrichment API (you may
use Clearbit, Apollo, or simulate the response with a mock API endpoint like webhook.site or
Beeceptor). Extract: industry, employee count, and company HQ country.
• Scoring logic: Implement a lead scoring model inside Make.com using Set Variable and
Math modules. The scoring should factor in: team size (≥50 employees = high), ROI value
(≥$50K = high), industry (SaaS, FinTech, Healthcare = priority verticals), and email domain
(reject generic domains like gmail.com or yahoo.com).
• Router module: Branch the flow into three paths based on the computed score:
◦ Hot Lead (≥80 points): Create a contact in HubSpot (or a Google Sheet simulating
CRM) with “Hot” tag, send a Slack notification to #sales-qualified with the lead
summary, and trigger an immediate follow-up task.
◦ Warm Lead (40–79 points): Create a CRM contact with “Warm” tag, add to a nurture
list in a Google Sheet or Datastore.
◦ Disqualified (<40 points or generic email): Log to a “Disqualified” Datastore with the
rejection reason. Do not create a CRM contact.

• Error handling: Add an error handler branch on the enrichment HTTP module (in case the
API is down or returns an unexpected status). If enrichment fails, the lead should still be
processed using only the data from the original submission, with a flag noting “Enrichment
Failed.”
• Persistence: Use a Make.com Datastore to log every processed lead (regardless of
outcome) with a timestamp, score, and routing decision for auditability.
