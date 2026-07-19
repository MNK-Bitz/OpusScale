# OpusScale — Build Sequence

Ordered batch-by-batch instructions sent to the Base44 builder. Each batch must finish (status = "ready") before the next is sent. Builder messages are metered and rate-limited — one at a time.

---

## Batch 1: Foundation ✅ SENT
- Theme: dark mode, blue + emerald palette.
- Roles: client, agent, admin.
- Auth: Google, Apple, email/password.
- Entities: all 14 (ClientProfile → Notification).

## Batch 2: Client Portal Pages
- `/portal/dashboard` — active services, project timelines, performance metrics.
- `/portal/services` — browse/purchase services.
- `/checkout` — Paddle/Braintree checkout (multi-currency, Apple/Google Pay, US tax).
- `/portal/subscriptions` — manage subscriptions.
- `/portal/refunds` — refund portal (request + history).
- `/portal/support` — Support Hub (tickets + live chat).
- `/portal/projects` — project timelines & milestones.
- `/portal/onboarding` — compliance checklist + legal doc generation.
- `/portal/ads` — ad campaign management (brand assets, audience profiles).
- `/portal/settings` — profile, company, payment methods.

## Batch 3: Agent Console Pages
- `/console/dashboard` — queue, SLA countdown timers, stats.
- `/console/tickets` — ticket inbox with MNK drafts for review.
- `/console/chat` — live chat interface.
- `/console/clients` — assigned clients.
- `/console/upsell` — manage upsell triggers.

## Batch 4: Admin Panel Pages
- `/admin/dashboard` — platform-wide metrics.
- `/admin/services` — manage marketplace catalog.
- `/admin/refunds` — approve/reject refund requests.
- `/admin/users` — manage users & roles.
- `/admin/agents` — manage support agents (human + MNK AI).
- `/admin/sla` — SLA policies.
- `/admin/legal` — legal doc templates & compliance settings.
- `/admin/payments` — payment processor config & transactions.

## Batch 5: Backend Functions
- Payments: `createCheckoutSession`, `handlePaymentWebhook`, `calculateUsTax`, `processRefund`.
- Support/AI: `mnkDraftResponse`, `evaluateUpsellTriggers`, `assignTicket`, `startSlaCountdown`.
- Notifications: `sendNotification`, `notifySlack`.
- Legal: `generateLegalDoc`, `validateCompliance`.
- Onboarding: `checkOnboardingComplete`.

## Batch 6: Workflows + Integrations
- Workflows: SLA Warning, Refund Auto-Approval, MNK Draft Generation, Upsell Suggestion, Onboarding Reminder, Subscription Renewal, Legal Doc Regeneration.
- Integrations: Paddle/Braintree checkout + webhooks; Slack notifications; Google/Apple auth.
