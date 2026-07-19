# OpusScale

A full-stack digital agency and automated operations platform — allowing businesses to purchase and manage outsourced digital services (Social Media Management, Website Development, Digital Advertising, Omni-channel Customer Support) with a unified support engine, automated workflows, and integrated global payments.

> This repository tracks the **build blueprint, entity schema, backend functions, and workflow specs** for the OpusScale platform, which is being built on the [Base44](https://base44.com) platform and merged into the existing **Nova Digital Portal** app (id `6a5a64c4604df7503ae49e69`).

---

## Core Pillars

1. **Customer Support Engine** — Support Hub, live ticket & chat (Pre-Sale / General / After-Sale), upselling engine, and strict SLA countdown timers.
2. **Client Onboarding & Portal** — secure registration (Google / Apple / email), dashboard with active services, project timelines, and performance metrics.
3. **Premium Service Marketplace & Checkout** — Paddle / Braintree (PayPal Commerce) checkout, replacing Stripe. Multi-currency, Apple Pay, Google Pay, localized US taxation.
4. **Automated Customer Refund Portal** — self-serve or admin-approved instant refunds connected to the payment processor.
5. **Subscription & Ad Management** — clients upload brand assets, product/service descriptions, and target audience profiles.
6. **Legal & Compliance Module** — TCPA, CAN-SPAM, COPPA, GDPR compliance; dynamic Privacy Policy & Terms of Service generators.

## Roles

| Role | Description |
|------|-------------|
| **Client** | Business Owner — purchases and manages services |
| **Agent** | Internal Staff / Support Agent / Manager |
| **Admin** | Platform Owner — full control |

## AI Superagents — "MNK"

All AI-assistant features are branded **MNK**. MNK drafts initial customer support responses for human agent review before sending.

## Tech & UI

- Modern, clean SaaS aesthetic — dark mode primary, professional **blue + emerald** theme.
- Fully responsive / mobile-friendly (PWA-installable, not native binaries).
- AI Superagents ("MNK") draft initial customer support responses for human agent review.

---

## Documentation Index

| Document | Description |
|----------|-------------|
| [docs/build-blueprint.md](docs/build-blueprint.md) | Complete end-to-end build blueprint (pages, schema, functions, workflows, build sequence) |
| [docs/project-notes.md](docs/project-notes.md) | Current status, key decisions, open questions |
| [docs/entity-schema.md](docs/entity-schema.md) | Full PostgreSQL entity schema reference |
| [docs/backend-functions.md](docs/backend-functions.md) | Backend function specifications |
| [docs/workflows.md](docs/workflows.md) | Scheduled & triggered automation specs |
| [docs/build-sequence.md](docs/build-sequence.md) | Ordered batch-by-batch builder instructions |

---

_Last updated: 2026-07-19_
