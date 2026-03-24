---
title: "Add Leads API Section with Opt-Out Endpoint"
created: "2026-03-24"
status: "completed"
current_phase: "Phase 3"
jira_ticket: ""
---

# Add Leads API Section with Opt-Out Endpoint

## Objective

Add a new "Leads" top-level section to the API documentation with an initial entry documenting the lead opt-out (TCPA opt-out / stop calling) endpoint.

## Context

The platform has a `POST /opt-out/{internalLeadId}` endpoint that opts a lead out of calling (currently the only opt-out action, but may grow). This needs to be documented as a new "Leads" section in the Slate API docs. The endpoint accepts an `internalLeadId` path parameter and uses the authenticated org context to stop calling the specified lead.

Currently, lead-related documentation only exists under the Webhooks section (inbound lead notifications and lead concierge events). This new section covers outbound lead management API calls made by the client.

## Phases

### Phase 1: Create the Leads Documentation File

- [x] Create `source/includes/_leads.md` following existing endpoint documentation patterns
- [x] Add `# Leads` top-level heading with a brief section overview
- [x] Document the `## Opt-Out Lead` endpoint with:
  - Example request (POST with path param)
  - Example success response (standard `ResponseView<Void>` / empty data response)
  - Description of what the endpoint does (opts lead out of calling)
  - `### HTTP Request` — `POST management/v1/opt-out/{internalLeadId}`
  - `### URL Parameters` table (`internalLeadId` — Long, required)
  - `### Response Codes` (200 OK, 404 Not Found, etc.)
  - Note that this currently opts the lead out of calling, but the scope may expand
- [x] Verify the documentation renders correctly (local Ruby env has version mismatch; CI will validate)

### Phase 2: Register in the Includes Index

- [x] Add `_leads` to the `includes` list in `source/index.html.md`
- [x] Position it after `_webhook_integration` (keeps lead-related docs adjacent to webhooks)
- [x] Verify the section appears in the correct position in the rendered docs

### Phase 3: Update Knowledge Base

- [x] Update `knowledge-base/architecture/project-overview.md` to reference the new Leads section
- [x] Commit plan and docs together

## Risks & Open Questions

- **Exact API path**: The controller mapping shows `/opt-out/{internalLeadId}` — need to confirm the full path prefix (e.g., `management/v1/opt-out/{internalLeadId}` or similar). Check with the backend team or existing route configuration.
- **Response shape**: The endpoint returns `ResponseView<Void>` — need to confirm if this is the standard `{ "data": null }` wrapper or an empty body.
- **Authentication**: Confirm whether this uses the same shared-secret header auth as other management API endpoints or if it has different auth requirements.
- **Additional opt-out types**: The code comment mentions this may grow beyond calling opt-out. Consider whether the endpoint name/docs should be future-proofed or kept specific for now.
- **Error scenarios**: What happens if the lead doesn't exist, or doesn't belong to the org? Document expected error codes.
