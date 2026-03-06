---
title: "Add Lead Concierge Webhook Events Documentation"
created: "2026-03-05"
status: "in-progress"
current_phase: "Phase 2"
jira_ticket: ""
---

# Add Lead Concierge Webhook Events Documentation

## Objective

Add documentation for the new **Lead Concierge Webhook Events** to the existing Webhooks section in `_webhook_integration.md`. This webhook notifies partners of lead concierge status changes (e.g., call outcomes, status transitions).

## Context

The existing Webhooks section documents the **Client Leads Webhook**, which delivers lead data from Facebook, Google, and TikTok. The new Lead Concierge webhook follows a similar pattern but has a different payload structure:

- **`orderData`** and **`programData`** reuse the same shape as the existing leads webhook's `order` and `program` objects (same fields, just top-level key names differ slightly).
- **`leadConciergeData`** is a new object containing concierge-specific fields like status transitions, outcomes, summaries, and transcripts.

The new webhook is a **separate event type** (not a new lead channel like Facebook/Google/TikTok), so it should be documented as its own subsection under `# Webhooks`, parallel to `## Client Leads Webhook`.

## Phases

### Phase 1: Add Documentation

- [x] Add a new `## Lead Concierge Webhook Events` section in `_webhook_integration.md`
- [x] Include the following subsections:
  - [x] **Overview** - Brief description of what this webhook delivers
  - [x] **Setup** - Same approach as Client Leads but separate endpoint
  - [x] **Policies** - Same retry policies, deduplication via `idempotencyKey`
  - [x] **Headers** - Same headers, with cross-reference to signature validation
  - [x] **Payload** - Full JSON example with `idempotencyKey` added
  - [x] **Field reference table** for all fields (`orderData`, `programData`, `leadConciergeData`)
  - [x] Marked `summary` and `transcript` as nullable
  - [x] Added `idempotencyKey` (string) as top-level deduplication field
  - [x] Left `leadType`, `currentStatus`, `previousStatus`, `currentOutcome` values as TODO placeholders

### Phase 2: Knowledge Base Update

- [ ] Update `knowledge-base/architecture/project-overview.md` to mention the new Lead Concierge webhook under the API surface area
- [ ] Update `knowledge-base/index.yaml` if new entries are created

## Payload Structure Reference

The payload provided for this webhook:

```json
{
    "orderData": {
        "architectureId": 3799517520646443763,
        "architectureName": "Org 3799517520210236106 Arch 1772769684906",
        "productId": 3799517520763884281,
        "productName": "Product Name 1772769684910",
        "orderId": 3799517520663220980,
        "userId": "email-1772769684902@example.com",
        "groupId": "34e3fbfc-ef96-43ef-b46f-726d8047a059",
        "orderItem": {
            "orderItemId": 3799517521485304607
        }
    },
    "programData": {
        "projectId": 3799517520898102019,
        "programId": 3799517520629666546,
        "programName": "programName-1772769684904",
        "programDescription": "verbose program description-1772769684905",
        "programVariables": {
            "var1": "foo"
        }
    },
    "leadConciergeData": {
        "internalLeadId": "100",
        "conciergeProviderLeadId": "1095675601",
        "conciergeEventId": "1772769685200",
        "leadType": "1",
        "currentStatus": "completed",
        "previousStatus": "NEW",
        "currentOutcome": "COMPLETED_CLOSED_OTHER",
        "summary": "Test call summary",
        "transcript": "Test transcript content"
    }
}
```

### Key Observations

- `orderData` has the same fields as the existing `leads[].order` object
- `programData` has the same fields as the existing `leads[].program` object
- `leadConciergeData` is entirely new and specific to this webhook type
- Unlike the leads webhook (which wraps everything in a `leads[]` array), this payload is a flat object

## Resolved Questions

- **Endpoint**: Same setup approach as Client Leads but a separate endpoint. ✅
- **Nullable fields**: `summary` and `transcript` in `leadConciergeData`. ✅
- **Deduplication**: New `idempotencyKey` field (string) added at the top level. ✅

## Remaining TODOs (in the doc as HTML comments)

- Possible values for `leadType`
- Possible values for `currentStatus` and `previousStatus`
- Possible values for `currentOutcome`
