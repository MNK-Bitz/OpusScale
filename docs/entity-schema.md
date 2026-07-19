# OpusScale — Entity Schema Reference

All entities are created on the Base44 platform (PostgreSQL-backed). Every record auto-includes: `id`, `created_date`, `updated_date`, `created_by`.

---

## 1. ClientProfile
| Field | Type | Notes |
|-------|------|-------|
| user_id | ref User | |
| business_name | string | |
| industry | string | |
| website | string | |
| description | text | |
| brand_assets_url | string | file link |
| target_audience | text | demographics, interests, geography |
| product_service_descriptions | text | |
| country | string | for tax/locale |
| compliance_flags | object | {tcpa boolean, can_spam boolean, coppa boolean, gdpr boolean} |

## 2. Service (Marketplace catalog)
| Field | Type | Notes |
|-------|------|-------|
| name | string | Social Media Mgmt / Web Dev / Digital Ads / Omni-channel Support |
| tier | string | Starter / Growth / Scale |
| price_monthly | number | |
| currency | string | default USD |
| description | text | |
| features | array[string] | |
| active | boolean | |

## 3. Subscription
| Field | Type | Notes |
|-------|------|-------|
| client_id | ref User | |
| service_id | ref Service | |
| status | string | active / paused / cancelled / past_due |
| billing_cycle | string | monthly / annual |
| current_period_end | date | |
| payment_method_token | string | |
| currency | string | |
| auto_renew | boolean | |

## 4. Order
| Field | Type | Notes |
|-------|------|-------|
| client_id | ref User | |
| subscription_id | ref Subscription | optional |
| items | array[object] | {service_id, name, price, currency} |
| total | number | |
| currency | string | |
| tax_amount | number | localized US tax |
| payment_processor | string | paddle / braintree |
| payment_id | string | |
| status | string | pending / paid / refunded / partial_refund |

## 5. RefundRequest
| Field | Type | Notes |
|-------|------|-------|
| order_id | ref Order | |
| client_id | ref User | |
| amount | number | |
| reason | string | |
| status | string | requested / admin_approved / auto_approved / processing / completed / rejected |
| processor_refund_id | string | |
| requested_date | date | |
| resolved_date | date | |
| auto_eligible | boolean | per agency policy |

## 6. SupportAgent
| Field | Type | Notes |
|-------|------|-------|
| user_id | ref User | null for AI agents |
| type | string | human / ai_hybrid |
| name | string | "MNK" for AI |
| specializations | array[string] | pre_sale / general / after_sale |
| languages | array[string] | |
| status | string | online / offline / busy |
| assigned_client_ids | array[string] | for human agents |

## 7. Ticket
| Field | Type | Notes |
|-------|------|-------|
| client_id | ref User | business that owns the support context |
| end_customer_name | string | |
| end_customer_email | string | |
| end_customer_phone | string | |
| category | string | pre_sale / general / after_sale |
| subject | string | |
| description | text | |
| status | string | open / in_progress / awaiting_client / resolved / closed |
| priority | string | low / normal / high / urgent |
| assigned_agent_id | ref SupportAgent | |
| sla_deadline | datetime | |
| sla_first_response_deadline | datetime | |
| first_response_at | datetime | |
| resolved_at | datetime | |
| suggested_addon_ids | array[string] | ref Service — from upselling engine |
| mnk_draft | text | AI-drafted initial response |
| mnk_draft_status | string | pending / approved / edited / rejected |

## 8. ChatMessage
| Field | Type | Notes |
|-------|------|-------|
| ticket_id | ref Ticket | |
| sender_type | string | end_customer / agent / ai_mnk / system |
| sender_id | string | |
| body | text | |
| attachments | array[string] | file urls |
| read | boolean | |

## 9. UpsellTrigger
| Field | Type | Notes |
|-------|------|-------|
| trigger_keyword | string | |
| suggested_service_id | ref Service | |
| message_template | text | |
| category | string | pre_sale / general / after_sale |
| active | boolean | |

## 10. SLAPolicy
| Field | Type | Notes |
|-------|------|-------|
| category | string | pre_sale / general / after_sale |
| priority | string | low / normal / high / urgent |
| first_response_minutes | number | |
| resolution_hours | number | |

## 11. LegalDocument
| Field | Type | Notes |
|-------|------|-------|
| client_id | ref User | |
| type | string | privacy_policy / terms_of_service / tcpa_consent / gdpr_dpa |
| content | text | generated |
| version | string | |
| accepted_date | datetime | |

## 12. OnboardingChecklist
| Field | Type | Notes |
|-------|------|-------|
| client_id | ref User | |
| items | array[object] | {key, label, completed, required} |
| completed_count | number | |
| total_count | number | |

**Checklist item keys:** profile, brand_assets, compliance_tcpa, compliance_can_spam, compliance_coppa, compliance_gdpr, payment_setup, first_service

## 13. Project
| Field | Type | Notes |
|-------|------|-------|
| client_id | ref User | |
| subscription_id | ref Subscription | |
| title | string | |
| type | string | website / social_campaign / ad_campaign / support_setup |
| status | string | planning / in_progress / review / completed / on_hold |
| milestones | array[object] | {title, due_date, completed} |
| progress | number | 0-100 |
| assigned_manager_id | ref User | internal |

## 14. Notification
| Field | Type | Notes |
|-------|------|-------|
| user_id | ref User | |
| type | string | ticket_assigned, sla_warning, refund_requested, etc. |
| payload | object | |
| channel | string | in_app / email / slack |
| read | boolean | |
