# {{API_NAME}} API Contract

<!-- 

Use this template to create new API contract requirement documents. Remove sections that are not applicable.

-->

**API contract revision**: {{rev number}} <!-- e.g. rev1, rev2, ... -->
**Created**: {{DATE}} 
**Features this API revision supports**: {{FEATURE_NAME_AND_REVISION}} <!-- e.g. show_list_of_products_rev1 -->
  - **User Stories**: {{List of user stories from the feature spec that this API supports}}
    - {{User Story 1 Title}}
    - {{User Story 2 Title}}


{{A brief description of what this API covers, its purpose and domain context}}

---

**Feature this 

## 2. Agent Instructions
High-level guidance for the AI agent / developer:
- What the API covers (resources, domain scope).
- Mandatory background docs to read:
  - Architecture document: `docs/requirements/system-architecture.md` 
  - Data Model, REST standards, existing OpenAPI spec path: `src/main/resources/open-api/open-api-spec.yaml`).
- Reuse guidance: Prefer existing schemas if compatible; extend otherwise.
- Time & date format requirements (e.g. ISO 8601 UTC).
- Any global constraints (e.g. pagination strategy, partition keys, multi-tenancy, security model flags, feature toggles).
- Explicit mention to read any "Modifications" section files to construct the current truth.

---
## 3. Modifications
If this is an initial contract, list none. Otherwise list incremental modification files that override or extend prior behavior.
Format each entry:
- `docs/requirements/<api>-api-contract-modification-N.md`: Short summary of change.
Provide chronological order; newest last. Each modification file should ONLY list deltas (unchanged items omitted).

Optional guidance for creating modification files:
```
# Modification <N> of the Contract for the <API Name> API
- change purpose: <one-line reason>
- background: <context for fix or enhancement>
- logic changes: <targeted instructions referencing file:line when helpful>
- repository changes: <new/updated queries & rationale>
- service changes: <methods to add/update/remove>
- dto/schema changes: <fields added/removed, defaulting rules>
- tests: <unit/integration adjustments>
- edge cases: <new ones considered>
- performance/security impacts: <if any>
```

---
## 4. Domain Context & Data Model References
Describe domain entities touched by this API. For each entity include:
- Document / Table name & partition key strategy.
- Primary identifier field(s).
- Relevant arrays / nested structures used in joins (e.g. `readReceipts[]`, `acknowledgeReceipts[]`).
- Relationships (one-to-many, ownership, aggregation).
Include cross-links to architecture doc subsections.

---
## 5. Glossary (Optional)
Define domain-specific terms (e.g. "Recipient List", "Acknowledgement", "Facility Audience Resolution").

---
## 6. Common Notes
General notes that apply to multiple endpoints:
- Pagination defaults and limits (`page`, `pageSize`, `pageTotal`).
- Default filter values (e.g. `active=true`).
- Implied defaults for boolean fields (e.g. `urgent=false`).
- Conventions for optional vs required query parameters.

---
## 7. Common Logic
Reusable algorithmic procedures referenced across endpoints:
- Audience resolution (example: resolve facilities from message audience states & recipient lists).
- Shared counting methods (e.g. COUNT(DISTINCT) patterns to avoid inflated counts from array joins).
- Date range filtering (inclusive/exclusive boundaries, timezone normalization).
- Blob storage or external integrations configuration steps.
- Audit field handling (`createdAt`, `lastUpdatedAt`).
For each logic block:
```
### <Logic Name>
Purpose: <why>
Steps:
1. ...
2. ...
Edge Cases:
- ...
Performance Considerations:
- ...
Reusable Method Signature Suggestion: <serviceMethodName(params)> returning <type>
```

---
## 8. Security & Authorization (Optional)
- Auth mechanisms (JWT, API key, feature flag `enable_auth_library_security`).
- Public/anonymous endpoints list (e.g. health, info).
- Required roles/claims per endpoint (if applicable).

---
## 9. Error Handling
Reference standard error schema. List common error scenarios:
- Validation failures (400) – enumerate validation codes/messages.
- Not found (404).
- Conflict (409) for upsert or duplicate operations.
- Unauthorized (401) / Forbidden (403).
- Rate limit (429) (if applicable).
- Internal server error (500).
Provide example JSON for each unique case; ensure codes are consistent with standards doc.

---
## 10. Observability & Telemetry (Optional)
- Required logs (start/end of operation, key identifiers).
- Metrics (counters for reads/writes, latency histograms).
- Tracing spans naming conventions.

---
## 11. Performance & Scalability Considerations (Optional)
- Maximum page sizes & reasoning.
- Query patterns to avoid cross-partition scans.
- Index usage expectations.
- Potential hotspots and mitigation (caching, projection queries).

---
## 12. Data Validation Rules
For each DTO/entity field list critical validations:
```
Field: <name>
Type: <type>
Constraints: <regex / enum / range>
Nullable: yes|no
Default: <value or none>
Notes: <business logic notes>
```
Group by Request Type if multiple request schemas exist.

---
## 13. DTO / Schema Definitions Summary
Enumerate all request & response schemas needed (with reuse guidance). For each schema:
- Name
- Purpose
- Fields table (brief – refer to validation section for details)
- Optional vs required fields
- Omitted conditions (e.g. `readAt` omitted when `userId` absent)

---
## 14. Repository Layer Requirements
List needed repository methods and queries. For each:
```
Method: <name(signature)>
Purpose: <what it returns and why>
Query: <SQL/CQL/JPQL/etc>
Notes: Distinct usage / JOIN arrays / projection optimization
Return Type: <type>
Edge Cases: <empty result behavior>
```
Include caution notes on COUNT vs COUNT(DISTINCT) where arrays are joined.

---
## 15. Service Layer Requirements
Outline service methods. For each:
```
Method: <name>
Visibility: public | package
Purpose: <summary>
Input: <params>
Output: <DTO/entity>
Steps: <ordered implementation steps>
Uses: <repository methods, other services>
Error Modes: <exceptions and responses>
```
Highlight which are helper/internal vs controller-facing.

---
## 16. Endpoint Definitions (Access Patterns)
Repeat the following template PER endpoint or grouped pattern.
```
### <Descriptive Name>
- purpose: <what scenario this covers>
- method: GET|POST|PUT|DELETE|PATCH
- URL: /api/v1/<path>?<queryParamsExample>
- Preconditions: <data existence, auth requirements>
- Inputs:
  - Path Params: <list with types and validations>
  - Query Params: <list, defaults, optionality>
  - Headers: <auth / tracing / custom>
  - Request Body Schema: <schema name or none>
- Defaults Applied: <list>
- Logic:
  1. <step>
  2. <step>
- Pagination: <if applicable – algorithm to compute pageTotal, recordTotal>
- Calculated Fields: <list and calculation rule>
- Idempotency: <if write operation – key or condition>
- Example Request Body:
```json
{}
```
- Example Response Body:
```json
{}
```
- Error Cases Specific to This Endpoint:
  - 400 <reason>
  - 404 <reason>
  - ...
- OpenAPI Spec Tasks:
  - Add/Reuse schema `<SchemaName>`.
  - Mark optional fields: <list>.
  - Add error responses: <list codes>.
- Test Cases:
  - Unit: <core logic functions>
  - Integration: <endpoint success + edge cases>
  - Contract: <OpenAPI validation>
  - Negative: <invalid param, missing body>
```

Group multiple similar read patterns (e.g. same path different query params) under one section and document variations clearly.

---
## 17. Pagination Specification (If Not Already Covered)
Detail offset vs cursor approach, maximum limits, calculation formulas, examples with boundary conditions.

---
## 18. Calculated vs Stored Fields Matrix
Table describing which fields are persisted vs derived at runtime, and derivation algorithms.

---
## 19. Edge Cases Inventory
List cross-cutting edge cases:
- Missing related documents (e.g. read-receipt doc absent).
- Empty arrays vs null.
- Large audience resolution producing >X facilities.
- Concurrent updates (ack vs read collisions).
- Duplicate submissions (idempotent writes).

---
## 20. Testing Strategy Summary
- Unit focus areas.
- Integration coverage matrix (endpoint x scenario).
- Performance test triggers (if any > threshold).
- Test data setup helpers.

Optional: Provide a table of endpoints vs test types required.

---
## 21. OpenAPI Specification Update Checklist
Checklist:
- [ ] Paths added/updated
- [ ] Schemas reused/created
- [ ] Optional fields flagged
- [ ] Example requests/responses added
- [ ] Error responses included
- [ ] Authentication security schemes applied
- [ ] Tags added for grouping

---
## 22. Migration / Backward Compatibility (Optional)
If schema changes are breaking, describe migration steps, versioning approach, feature flags or dual-write strategy.

---
## 23. Future Extension Points (Optional)
Potential enhancements that are out of scope now but considered in design (e.g. soft deletes, auditing expansion, multi-region support).

---
## 24. Non-Functional Requirements (Optional)
- Availability / latency SLOs
- Throughput expectations
- Data retention policies
- Security compliance requirements

---
## 25. Example Generic Error Responses
Provide (or reference standard doc) examples for 400, 401, 403, 404, 409, 429, 500.
```json
{
  "error": "Internal Server Error",
  "code": "MessageManager-00010",
  "message": "An internal server error occurred while processing the request.",
  "date": "2026-03-30T10:00:32.678Z"
}
```

---
## 26. Document Maintenance Notes
- How modification files should be incremented.
- Versioning naming convention.
- Responsibility for keeping OpenAPI spec in sync.

---
## 27. Final Implementation Readiness Checklist
Before starting coding verify:
- [ ] All endpoints have purpose & logic.
- [ ] All repository methods specified or intentionally deferred.
- [ ] Calculated fields algorithms defined.
- [ ] Validation rules complete.
- [ ] Error responses enumerated.
- [ ] Test cases mapped.
- [ ] Performance considerations addressed.
- [ ] Security requirements clear.

---
## 28. Removal Guidance (Meta)
When simplifying for a smaller API:
- Keep sections: Title, Agent Instructions, Endpoint Definitions, Error Handling, Repository & Service Requirements, OpenAPI Checklist.
- Remove optional sections that have no content; do NOT leave empty headings.

---
## 29. Minimal Starter Variant (If Quick Draft Needed)
A condensed version (delete this entire section if not needed):
```
# <API Name> API Contract
## Agent Instructions
<summary>
## Modifications
<list or none>
## Domain Context
<key entities>
## Common Logic
<list algorithms>
## Endpoint Definitions
<use template from section 16>
## Repository Methods
<list>
## Service Methods
<list>
## Schemas
<list>
## Error Handling
<list examples>
## OpenAPI Checklist
<checked items>
```

---
## 30. Example Filled Endpoint Section
```
### Read Urgent Sent Messages
- purpose: Retrieve urgent messages sent and filter by date, facility, user.
- method: GET
- URL: /api/v1/messages?status=SENT&urgent=true&facilityId=STSH-1234-ABC&userId=user243&page=1&pageSize=25
- Preconditions: facilityId must exist if provided.
- Inputs:
  - Path Params: none
  - Query Params:
    - status (enum: SENT|SCHEDULED|DRAFT, required) -> SENT
    - urgent (boolean, required) -> true
    - facilityId (string, optional)
    - userId (string, optional)
    - page (int, default 1, min 1)
    - pageSize (int, default 25, max 100)
    - pageTotal (boolean, default false)
    - createdAtFrom (ISO timestamp, optional)
    - createdAtTo (ISO timestamp, optional)
    - query (string, optional – searches title,text)
  - Headers: Authorization (Bearer), Correlation-Id (UUID)
  - Request Body Schema: none
- Defaults Applied: urgent=true forces filtering logic.
- Logic:
  1. Resolve audience if facilityId provided (call StatsService helper).
  2. Build repository query with filters (status=SENT, urgent=true, date range, facility inclusion, text search).
  3. Execute paginated query.
  4. If status=SENT and userId present, compute readAt per message from read-receipt doc; accumulate unread count.
  5. If pageTotal=true compute total pages.
  6. Map entities to DTO including optional readAt.
- Pagination: offset, pageTotal via Pageable.getTotalPages(), recordTotal via getTotalElements().
- Calculated Fields: readAt (conditional), stats.totalUnreadMessages (conditional), readReceiptStats (omit if userId provided).
- Example Response Body:
```json
{
  "pagination": {"page":1,"pageSize":25,"pageTotal":4,"recordTotal":100},
  "stats": {"totalMessages":100,"totalUnreadMessages":20},
  "records": []
}
```
- Error Cases:
  - 400 invalid page params
  - 404 facility not found (if facilityId provided)
- OpenAPI Tasks: mark optional query params, define MessageListResponse schema, include error responses.
- Test Cases:
  - Unit: unread count calculation, readAt derivation
  - Integration: filtering by urgent, facility, date range
  - Negative: invalid date format, non-existent facility
```

---
End of Template.

