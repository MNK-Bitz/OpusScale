# OpusScale — Build Blueprint

> Full-stack digital agency + automated operations platform.
> Once a Base44 app named "OpusScale" exists, send these build instructions to the builder in chunks.

---

## 0. Global / App-Level

### Theme
- Dark mode primary, with a professional blue + emerald accent palette.
- Trust-building SaaS aesthetic: clean cards, generous spacing, subtle motion.
- Mobile-responsive everywhere; PWA-friendly.

### Roles (use Base44 built-in role system + User entity)
- `client` — Business Owner (purchases/manages services).
- `agent` — Internal Staff / Support Agent / Manager.
- `admin` — Platform Owner (full control).

### Auth
- Google OAuth, Apple Sign-In, Email/password (Base44 built-in auth).
- On first login, route by role to the right dashboard.

### AI Superagents — "MNK"
- Name all AI-assistant features/agents "MNK".
- MNK drafts initial customer support responses; human agent reviews before send.

---

## 1. Entity Schema (PostgreSQL via Base44 entities)

### User (built-in + extra fields)
- `role`: enum [client, agent, admin]
- `company_name`: string
- `phone`: string
- `timezone`: string
- `avatar_url`: string
- `onboarding_status`: enum [pending, in_progress, complete]
- `country`: string (for tax/locale)

### ClientProfile
- `user_id`: ref User
- `business_name`, `industry`, `website`, `description`
- `brand_assets_url`: string (file link)
- `target_audience`: text (demographics, interests, geography)
- `product_service_descriptions`: text
- `compliance_flags`: object (tcpa, can_spam, coppa, gdpr accepted booleans)

### Service (Marketplace catalog)
- `name`: enum [Social Media Management, Website Development, Digital Advertising, Omni-channel Customer Support]
- `tier`: string (Starter / Growth / Scale)
- `price_monthly`: number
- `currency`: string (USD default)
- `description`, `features`: array
- `active`: boolean

### Subscription
- `client_id`: ref User
- `service_id`: ref Service
- `status`: enum [active, paused, cancelled, past_due]
- `billing_cycle`: enum [monthly, annual]
- `current_period_end`: date
- `payment_method_token`: string (Paddle/Braintree)
- `currency`: string
- `auto_renew`: boolean

### Order (one-time purchases / add-ons)
- `client_id`: ref User
- `subscription_id`: ref Subscription (optional)
- `items`: array of {service_id, name, price, currency}
- `total`: number
- `currency`: string
- `tax_amount`: number (localized US tax)
- `payment_processor`: enum [paddle, braintree]
- `payment_id`: string
- `status`: enum [pending, paid, refunded, partial_refund]
- `created_date` (auto)

### RefundRequest
- `order_id`: ref Order
- `client_id`: ref User
- `amount`: number
- `reason`: string
- `status`: enum [requested, admin_approved, auto_approved, processing, completed, rejected]
- `processor_refund_id`: string
- `requested_date`, `resolved_date`
- `auto_eligible`: boolean (per agency policy)

### SupportAgent
- `user_id`: ref User (for human agents; null for AI)
- `type`: enum [human, ai_hybrid]
- `name`: string (MNK for AI)
- `specializations`: array [pre_sale, general, after_sale]
- `languages`: array
- `status`: enum [online, offline, busy]
- `assigned_client_ids`: array (for human agents)

### Ticket
- `client_id`: ref User (the business that owns the support context)
- `end_customer_name`, `end_customer_email`, `end_customer_phone`
- `category`: enum [pre_sale, general, after_sale]
- `subject`, `description`
- `status`: enum [open, in_progress, awaiting_client, resolved, closed]
- `priority`: enum [low, normal, high, urgent]
- `assigned_agent_id`: ref SupportAgent
- `sla_deadline`: datetime (response deadline)
- `sla_first_response_deadline`: datetime
- `first_response_at`: datetime
- `resolved_at`: datetime
- `suggested_addon_ids`: array (ref Service) — from upselling engine
- `mnk_draft`: text (AI-drafted initial response pending human review)
- `mnk_draft_status`: enum [pending, approved, edited, rejected]

### ChatMessage
- `ticket_id`: ref Ticket
- `sender_type`: enum [end_customer, agent, ai_mnk, system]
- `sender_id`: string
- `body`: text
- `attachments`: array (file urls)
- `read`: boolean
- `created_date` (auto)

### UpsellTrigger
- `trigger_keyword`: string
- `suggested_service_id`: ref Service
- `message_template`: text
- `category`: enum [pre_sale, general, after_sale]
- `active`: boolean

### SLAPolicy
- `category`: enum [pre_sale, general, after_sale]
- `priority`: enum [low, normal, high, urgent]
- `first_response_minutes`: number
- `resolution_hours`: number

### LegalDocument
- `client_id`: ref User
- `type`: enum [privacy_policy, terms_of_service, tcpa_consent, gdpr_dpa]
- `content`: text (generated)
- `version`: string
- `accepted_date`: datetime

### OnboardingChecklist
- `client_id`: ref User
- `items`: array of {key, label, completed, required}
  - Keys: profile, brand_assets, compliance_tcpa, compliance_can_spam, compliance_coppa, compliance_gdpr, payment_setup, first_service
- `completed_count`: number
- `total_count`: number

### Project (for web dev / social / ad campaigns)
- `client_id`: ref User
- `subscription_id`: ref Subscription
- `title`, `type`: enum [website, social_campaign, ad_campaign, support_setup]
- `status`: enum [planning, in_progress, review, completed, on_hold]
- `milestones`: array of {title, due_date, completed}
- `progress`: number (0-100)
- `assigned_manager_id`: ref User (internal)

### Notification (in-app + email/Slack dispatch)
- `user_id`: ref User
- `type`: string (ticket_assigned, sla_warning, refund_requested, etc.)
- `payload`: object
- `channel`: enum [in_app, email, slack]
- `read`: boolean

---

## 2. Pages / Frontend Structure

### Public
- `/` — Landing (hero, services, pricing, testimonials, CTA)
- `/services` — Marketplace catalog
- `/pricing` — Tiered pricing
- `/login`, `/signup` (Google/Apple/email)

### Client Portal (`/portal/*` — role: client)
- `/portal/dashboard` — active services, project timelines, performance metrics
- `/portal/services` — browse/purchase services
- `/checkout` — Paddle/Braintree checkout (multi-currency, Apple/Google Pay, US tax)
- `/portal/subscriptions` — manage subscriptions
- `/portal/refunds` — refund portal (request + history)
- `/portal/support` — Support Hub (tickets + live chat)
- `/portal/projects` — project timelines & milestones
- `/portal/onboarding` — compliance checklist + legal doc generation
- `/portal/ads` — ad campaign management (brand assets, audience profiles)
- `/portal/settings` — profile, company, payment methods

### Agent/Manager Console (`/console/*` — role: agent)
- `/console/dashboard` — queue, SLA countdown timers, stats
- `/console/tickets` — ticket inbox with MNK drafts for review
- `/console/chat` — live chat interface
- `/console/clients` — assigned clients
- `/console/upsell` — manage upsell triggers

### Admin (`/admin/*` — role: admin)
- `/admin/dashboard` — platform-wide metrics
- `/admin/services` — manage marketplace catalog
- `/admin/refunds` — approve/reject refund requests
- `/admin/users` — manage users & roles
- `/admin/agents` — manage support agents (human + MNK AI)
- `/admin/sla` — SLA policies
- `/admin/legal` — legal doc templates & compliance settings
- `/admin/payments` — payment processor config & transactions

---

## 3. Backend Functions (deploy via deploy_backend_function)

### Payments
- `createCheckoutSession(orderId, processor)` → returns Paddle/Braintree checkout URL
- `handlePaymentWebhook(payload)` → verifies signature, updates Order status, creates Subscription
- `calculateUsTax(amount, state, zip)` → localized sales tax
- `processRefund(refundRequestId)` → calls processor refund API, updates status

### Support / MNK AI
- `mnkDraftResponse(ticketId)` → generates initial draft response from ticket context + KB (returns draft text; human reviews)
- `evaluateUpsellTriggers(messageText, ticketId)` → matches keywords → returns suggested addon service ids
- `assignTicket(ticketId)` → routes to best available agent by category/language/load
- `startSlaCountdown(ticketId)` → computes deadlines from SLAPolicy, sets on ticket

### Notifications
- `sendNotification(userId, type, payload)` → writes Notification, dispatches email/Slack
- `notifySlack(channel, message)` → posts to Slack webhook

### Legal / Compliance
- `generateLegalDoc(clientId, type)` → fills template with client business data → stores LegalDocument
- `validateCompliance(clientId)` → checks all required flags → returns missing items

### Onboarding
- `checkOnboardingComplete(clientId)` → tallies checklist, updates onboarding_status

---

## 4. Workflows (scheduled / triggered automations)

### SLA Warning (scheduled every 5 min)
- Scan open tickets where `sla_first_response_deadline` < now+30min and not responded → notify assigned agent (in-app + Slack). Escalate if breached.

### Refund Auto-Approval (entity-triggered on RefundRequest create)
- If `auto_eligible` and amount within policy threshold → auto-approve → call `processRefund`. Else notify admin.

### MNK Draft Generation (entity-triggered on Ticket create / first end-customer message)
- Call `mnkDraftResponse` → store draft on ticket with status pending → notify assigned human agent.

### Upsell Suggestion (entity-triggered on ChatMessage create from end_customer)
- Run `evaluateUpsellTriggers` → attach suggested addon ids to ticket → surface suggestion in chat UI.

### Onboarding Reminder (scheduled daily)
- Clients with `onboarding_status != complete` after 3 days → send email nudge.

### Subscription Renewal (scheduled daily)
- Subscriptions with `current_period_end` = today → trigger renewal via processor → update period / handle failures.

### Legal Doc Regeneration (entity-triggered on ClientProfile update)
- If business details change → regenerate Privacy Policy & ToS → store new version.

---

## 5. Checkout — Paddle / Braintree Integration Notes
- Replace Stripe entirely. Use Paddle (Merchant of Record → handles global tax) OR Braintree/PayPal Commerce.
- Paddle preferred: handles multi-currency, Apple/Google Pay, EU/US tax, subscriptions, refunds via API.
- Flow: client selects service → `createCheckoutSession` → redirect to Paddle/Braintree hosted checkout → webhook updates Order + creates Subscription.
- Refunds: `processRefund` calls Paddle `POST /refunds` or Braintree refund API.

---

## 6. Build Sequence (for the builder messages)

Send to builder in this order, one message at a time, waiting for status between each:

1. **Scaffold**: "Create app OpusScale. Dark mode, professional blue + emerald theme. Set up roles: client, agent, admin. Auth: Google, Apple, email."
2. **Entities**: paste the full entity schema list above.
3. **Client portal pages**: dashboard, services marketplace, checkout, subscriptions, refunds, support hub, projects, onboarding, ads, settings.
4. **Agent console pages**: dashboard with SLA timers, tickets with MNK draft review, live chat, clients, upsell management.
5. **Admin pages**: dashboard, services, refunds, users, agents, sla, legal, payments.
6. **Backend functions**: payments, MNK AI draft, upsell eval, notifications, legal gen, onboarding.
7. **Workflows**: SLA warning, refund auto-approval, MNK draft gen, upsell suggestion, onboarding reminder, renewal, legal regen.
8. **Integrations**: Paddle/Braintree checkout + webhooks; Slack notifications; Google/Apple auth.

---

*Blueprint complete. Once the OpusScale app exists in Base44, this document drives the build.*
