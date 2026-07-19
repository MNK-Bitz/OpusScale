# OpusScale — Project Notes

**Last updated:** 2026-07-19

## Status

- **Phase:** Foundation build (Batch 1 of ~6)
- **Base44 App:** Nova Digital Portal (id `6a5a64c4604df7503ae49e69`)
- **Strategy:** Merge OpusScale into existing Nova Digital Portal — keep marketing/showcase pages, layer full operational platform on top.
- **GitHub:** Synced to `MNK-Bitz/OpusScale`

## Batch Progress

| # | Batch | Status |
|---|-------|--------|
| 1 | Theme, roles, auth + 14 entities | ⏳ Processing |
| 2 | Client portal pages | ⬜ Pending |
| 3 | Agent console pages | ⬜ Pending |
| 4 | Admin panel pages | ⬜ Pending |
| 5 | Backend functions | ⬜ Pending |
| 6 | Workflows + integrations | ⬜ Pending |

## Key Decisions

- **Payments:** Paddle preferred over Braintree. Paddle is a Merchant of Record → handles global tax/compliance, multi-currency, Apple/Google Pay, subscriptions, and refunds via API. Base44's native payments are Stripe-backed, so Paddle/Braintree will be implemented via custom backend functions calling their APIs directly.
- **Mobile:** Responsive PWA (web-based, mobile-optimized) — not native iOS/Android App Store binaries.
- **AI branding:** All AI support-drafting features branded "MNK" (matches the user's GitHub handle MNK-Bitz).
- **Theme:** Dark mode primary, blue + emerald accent palette.
- **App merge:** OpusScale is being built inside the existing Nova Digital Portal app rather than a new app.

## Open Questions

- [ ] Confirm Paddle vs Braintree as the final payment processor (recommend Paddle).
- [ ] Confirm PWA approach is acceptable for "mobile app" requirement.
- [ ] Specify SLA response/resolution time targets per category+priority (defaults set in SLAPolicy entity).
- [ ] Refund policy thresholds for auto-approval (amount limits, time windows).

## Entity Count

14 entities defined: ClientProfile, Service, Subscription, Order, RefundRequest, SupportAgent, Ticket, ChatMessage, UpsellTrigger, SLAPolicy, LegalDocument, OnboardingChecklist, Project, Notification.

## Build Constraints (Base44)

- Builder messages are metered & rate-limited: **one batch at a time**, wait for "ready" status before sending the next.
- No tool to create a brand-new app from scratch in-chat — which is why we merged into the existing Nova Digital Portal app.
- Workflows use CNCF SWF v1.0 format via `create_or_update_workflow`.
- Backend functions deployed via `deploy_backend_function`.
