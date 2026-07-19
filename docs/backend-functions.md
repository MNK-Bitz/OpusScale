# OpusScale — Backend Functions

Deployed via `deploy_backend_function`. Called from frontend or workflows.

---

## Payments

### `createCheckoutSession(orderId, processor)`
Returns a Paddle/Braintree hosted checkout URL for the given order. Sets up multi-currency, Apple Pay, Google Pay, and localized US tax.

### `handlePaymentWebhook(payload)`
Verifies the processor signature, updates `Order` status to `paid`, and creates/activates the `Subscription`. Idempotent.

### `calculateUsTax(amount, state, zip)`
Returns localized sales tax for a US jurisdiction. Used at checkout to display tax before payment.

### `processRefund(refundRequestId)`
Calls the Paddle/Braintree refund API for the linked order, updates `RefundRequest` status to `completed`, and adjusts the `Order` status. Records `processor_refund_id`.

---

## Support / MNK AI

### `mnkDraftResponse(ticketId)`
Generates an initial draft support response from the ticket context + knowledge base. The "MNK" AI agent. Returns draft text and stores it on the ticket (`mnk_draft`, status `pending`) for human agent review.

### `evaluateUpsellTriggers(messageText, ticketId)`
Matches message text against active `UpsellTrigger` keywords. Returns suggested addon `Service` IDs and attaches them to the ticket (`suggested_addon_ids`).

### `assignTicket(ticketId)`
Routes a ticket to the best available human/AI support agent by category, language, and current load. Updates `assigned_agent_id`.

### `startSlaCountdown(ticketId)`
Looks up the matching `SLAPolicy` by category + priority, computes `sla_first_response_deadline` and `sla_deadline`, and writes them to the ticket.

---

## Notifications

### `sendNotification(userId, type, payload)`
Writes a `Notification` record and dispatches via the specified channel (in-app, email, or Slack).

### `notifySlack(channel, message)`
Posts a message to a Slack channel via incoming webhook. Used for SLA warnings, new ticket alerts, refund requests.

---

## Legal / Compliance

### `generateLegalDoc(clientId, type)`
Fills a legal document template (privacy_policy, terms_of_service, tcpa_consent, gdpr_dpa) with the client's business data from `ClientProfile`. Stores the result as a new `LegalDocument` record.

### `validateCompliance(clientId)`
Checks all required compliance flags and returns a list of missing/incomplete items. Used during onboarding.

---

## Onboarding

### `checkOnboardingComplete(clientId)`
Tallies the `OnboardingChecklist` items. If all required items are complete, updates the user's `onboarding_status` to `complete`.

---

## Integration Notes

- **Paddle** (preferred): Merchant of Record — handles global tax, multi-currency, subscriptions, refunds via `POST /refunds`. API key stored as secret.
- **Braintree** (alternative): PayPal Commerce — requires merchant account setup. Refunds via transaction API.
- **Slack:** Incoming webhook URL stored as secret.
- All secrets managed via Base44 secret storage (`set_secrets` / `.agents/.env`).
