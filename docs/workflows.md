# OpusScale — Workflows

Scheduled and triggered automations built with CNCF SWF v1.0 format via `create_or_update_workflow`. Agent steps trigger the Superagent, which handles the task using its tools.

---

## 1. SLA Warning (Scheduled — every 5 minutes)

**Trigger:** CRON `*/5 * * * *`
**Steps:**
1. Query open tickets where `sla_first_response_deadline` < now + 30 min and `first_response_at` is null.
2. For each, send an in-app + Slack notification to the assigned agent.
3. If deadline is already breached, escalate (notify admin + increase priority).

---

## 2. Refund Auto-Approval (Entity-triggered — on RefundRequest create)

**Trigger:** RefundRequest created
**Steps:**
1. Check `auto_eligible` flag and whether amount is within policy threshold.
2. If eligible → auto-approve → call `processRefund`.
3. If not eligible → set status to `requested` and notify admin for manual review.

---

## 3. MNK Draft Generation (Entity-triggered — on Ticket create / first end-customer ChatMessage)

**Trigger:** Ticket created OR first ChatMessage from `end_customer`
**Steps:**
1. Call `mnkDraftResponse(ticketId)` to generate an AI-drafted initial response.
2. Store draft on the ticket (`mnk_draft`, `mnk_draft_status = pending`).
3. Notify the assigned human agent that a draft is ready for review.

---

## 4. Upsell Suggestion (Entity-triggered — on ChatMessage create from end_customer)

**Trigger:** ChatMessage created with `sender_type = end_customer`
**Steps:**
1. Run `evaluateUpsellTriggers(messageText, ticketId)`.
2. If matches found, attach suggested addon `Service` IDs to the ticket.
3. Surface the suggestion in the chat UI for the agent to present to the customer.

---

## 5. Onboarding Reminder (Scheduled — daily)

**Trigger:** CRON `0 9 * * *`
**Steps:**
1. Query clients where `onboarding_status != complete` and account is older than 3 days.
2. Send a personalized email nudge listing remaining checklist items.

---

## 6. Subscription Renewal (Scheduled — daily)

**Trigger:** CRON `0 6 * * *`
**Steps:**
1. Query subscriptions where `current_period_end` = today and `auto_renew = true`.
2. Trigger renewal via the payment processor.
3. On success → update `current_period_end`. On failure → set status to `past_due` and notify client + admin.

---

## 7. Legal Doc Regeneration (Entity-triggered — on ClientProfile update)

**Trigger:** ClientProfile updated
**Steps:**
1. Detect changes to business details (name, website, industry, country, compliance flags).
2. Regenerate affected `LegalDocument` records (Privacy Policy, Terms of Service) with updated data.
3. Store as new versions; notify client to review and accept.

---

## Workflow Notes

- All agent-step workflows report results to the owner via `broadcast_message`.
- SLA and renewal workflows are time-critical — schedule frequency must be respected.
- Refund and MNK draft workflows are event-driven — fire immediately on entity change.
