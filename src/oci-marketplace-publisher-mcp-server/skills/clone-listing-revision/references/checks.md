# Required checks

Use detailed current records and complete pagination. Use JSON from OCI CLI; map the actual returned field names, commonly kebab-case. All service reads in this reference must use installed OCI CLI commands. Console/Oracle evidence is supplied by the publisher, not retrieved with an MCP server or automated browser. For clone-blocking checks, treat missing or contradictory evidence as **Needs verification**, invalid evidence as **Needs attention**, and apply **Not applicable** only when the listing type establishes it. Only Pass/Not applicable permit cloning for those checks. Report blocking reasons even if other independent checks pass. OPN membership and OCMA are separate, nonblocking approval advisories; inactive, expired, pending, or unverified evidence does not prevent a clone.

## Evidence reuse

Within one discovery pass of the freshly authenticated invocation, reuse reads by bound environment/endpoint/tenancy/profile/region, resource type, and full resource ID. Share publisher-level evidence across candidates of that publisher; fetch each required detail once. Keep a compact record of scope, parent/source/publisher IDs, observed states, read times, pagination completeness, ETag/request IDs when exposed, check results, and unresolved fields. For external evidence retain its source, matching environment and publisher/OPN entity, verification time, and validity. Reuse it only after matching it to the freshly verified production publisher; evidence never selects the login account. Staging service records or approvals do not establish production readiness.

Missing/projected-out fields for clone-blocking checks require detailed reads, not an inferred pass. Invalidate relevant evidence on scope changes, changed states, contradictions, or expiry. Start a new service-read cache for each discovery pass; retain external evidence only while current and matching. Immediately before cloning, refresh the source, parent, publisher, every sibling page, package inventory/details, and relevant operation evidence, and revalidate external evidence needed for clone-blocking checks. Refresh OPN/OCMA advisories from the available publisher read; retain their observed status, dates, source, and observation time, and mark unavailable or stale evidence unverified. Do not delay cloning to obtain advisory evidence or require renewed user confirmation solely because an advisory changed. A cached record or source ETag cannot establish that sibling/package state is unchanged.

## Identity and publisher

| Check | Pass evidence | If it cannot pass |
|---|---|---|
| Environment | Ashburn production is recorded in the evidence and every Publisher command explicitly uses the configured production endpoint; OCI identity calls use their own service | Resolve endpoint/region mismatch or production access before discovery/clone; never use staging or beta endpoints |
| Session | Fresh `oci session authenticate` through the publisher's existing browser profile, unused OCI configuration profile, successful `oci session validate`, and actual tenancy/region recorded; match a target tenancy when explicitly supplied | Complete and validate fresh OCI login using the same browser profile, then discover automatically without account confirmation; resolve an explicit target mismatch first and never reuse a previous invocation's OCI credentials |
| Ownership | Parent listing's `publisher-id` resolves to the intended publisher in the authenticated tenancy and authorized compartment | Resolve the selected account/listing first; use compartment ancestry if needed |
| Publisher registration | Detailed `publisher-status == APPROVED`; no authoritative suspension indication | Show the exact status and direct the publisher to their Publisher profile/support |
| Publisher logo | The detailed publisher response contains a `logo` field whose value is not null | `logo: null` needs attention; an omitted field needs verification through a detailed publisher read or current publisher-provided evidence |

This is a presence check only: a non-null publisher `logo` passes. Do not download or view the image, validate its URL or dimensions, reject a default-looking filename, or request visual confirmation. A listing icon does not satisfy the publisher logo check. Presence does not establish artwork compliance.

The logo and package-quality checks are this workflow's requested clone readiness requirements. They do not imply every deployment enforces them during clone.

## OPN and OCMA approval advisories

Read available OPN/OCMA evidence for the selected publisher and retain it for the post-clone reminder. These advisories never become clone-blocking checks, including when evidence is unavailable, contradictory, or unverified. Do not request membership renewal, agreement approval, or missing agreement evidence before cloning. Registration approval and suspension remain separate clone-blocking checks.

| Advisory | Evidence to record | Meaning for cloning |
|---|---|---|
| OPN | Actual `opn-membership.opn-status`, OPN number, and any `time-start`/`time-end` returned for the matching publisher | Inactive, expired, renewal-pending, future, or unverified membership does not block; retain the observed condition for the reminder |
| OCMA | Actual agreement/enrollment status and available validity evidence for the same legal entity and OPN number | Expired, unapproved, pending, or unverified agreement does not block; retain the observed condition for the reminder |

For OPN, parse timezone-aware dates when available; current validity is `time-start <= now < time-end`. For documented date-only agreement validity, use its documented timezone/inclusivity. Missing dates or fields mean validity is unverified, not expired. A returned status of ACTIVE/APPROVED alone may leave current validity unverified. Some CLI versions expose OCMA as `enrollment-status`; registration approval, acceptance/submission, or a generic terms record does not prove active OCMA. Use the evidence actually available without inventing enrollment endpoints, using internal-only records, or accepting agreements.

After confirmed clone completion, if either advisory is adverse or unverified, show a prominent standalone reminder in the completion response: **OCMA must be approved and current before this listing revision can be approved.** State the known condition and necessary action; for unknown validity, say it needs verification rather than claiming expiry. If OPN is inactive/expired, include renewal or activation as an approval-readiness action. If OPN is unverified, ask the publisher to verify its status before approval. Do not present the created draft as approved or require these actions to complete the clone. See [completion responses](publisher-flow.md) for customer wording.

## Access and operation evidence

A successful read confirms read access, not clone permission. Use IAM evidence accessible through OCI CLI or evidence supplied by the publisher if available, and surface any known denial. Do not require an ordinary publisher to list administrator-only policies. When no supported read-only effective-permission check exists, label clone authorization “enforced by OCI on request,” not a fabricated Pass or an invented failed prerequisite. The real clone request still requires user authorization and all clone-blocking checks to pass. If OCI rejects the clone, honor and explain the service denial; advisory handling never authorizes bypassing a service restriction or retrying around it.

List relevant work requests when supported, correlate returned resource references to this listing and its revisions, and inspect nonterminal results. Do not block on unrelated compartment activity. Nonterminal clone/update/delete/publish/withdraw operations that can conflict need attention. If operation reads are denied, try an OCI CLI read of the specific known request or ask the publisher for current Console activity evidence. Unresolved conflict evidence needs verification; absence of a list permission alone does not prove a conflict. For prior failed/canceled clones, check for a remaining nondeleted draft.

## Source and siblings

- Require parent and source `lifecycle-state == ACTIVE`, matching source `listing-id`, and consistent publisher/compartment scope.
- `PUBLISHED` and `PUBLISHED_AS_PRIVATE` are supported source states. Oracle's general guide also includes `UNPUBLISHED`, while some service versions reject it. For `UNPUBLISHED`, use authoritative evidence specific to the target service or publisher-provided evidence of the selected revision's current Console capability; if unresolved, mark Needs verification. Do not execute a trial clone or withdraw a published source to test eligibility. `NEW`, `APPROVED`, rejected, review-pending, and transitional publication states are not cloneable defaults.
- List every sibling revision page without lifecycle/status filters. A `NEW` sibling in `ACTIVE` or `UPDATING` blocks another clone. Show the draft ID, and suggest continuing it or waiting for completion. A definitively `DELETED` `NEW` sibling does not block.
- A nondeleted sibling still creating/updating/deleting, or a failed state that could conceal a partial draft, needs resolution when it conflicts with cloning. Do not interpret an unrelated historical rejected review or publication failure as an in-progress clone; inspect the actual lifecycle/operation evidence.
- Re-read immediately before the mutation. Compare identity, source content/ETag, sibling IDs/states, package readiness, and operation evidence with the selected option. ETag protection applies to the source only. A concurrent sibling conflict is a service validation failure; re-read instead of forcing a retry.

## Packages

Determine applicability from the listing/revision type and package type. Package-based OCI applications require these checks; lead generation/service revisions with no package requirement get Not applicable. Unknown or inconsistent types need verification.

1. Fetch all package pages and detailed records, deduplicating repeated IDs. Exclude only definitively `DELETED` packages. Require at least one usable `ACTIVE` package; nondeleted transitional, failed, inactive, or unknown package states need resolution. Every package must reference the selected source revision.
2. Require exactly one Boolean `is-default == true` among applicable active packages. Summaries may omit `is-default`: get details. A missing/non-Boolean flag makes the count unverified even if one known flag is true. Zero or multiple defaults need attention.
3. Require nonempty `package-version` and `display-name`. Distinct packages in the same source revision cannot share the exact version string. Follow documented case-insensitive rules if the deployed service specifies them; otherwise do not invent case folding. Matching a package version in a historical revision is not a duplicate within the source.
4. Reject any Unicode whitespace in a package display name or version, including internal/leading/trailing spaces, tabs, newlines, and nonbreaking spaces. Use Unicode-aware whitespace detection with existing local capabilities; no additional runtime or utility installation is required. Do not trim values to make them pass, or apply this rule to descriptions, terms, or usage prose.
5. Show affected package names, versions, IDs, and default flags when a check fails. Offer correction through the supported Publisher workflow; if a published source is immutable, direct the publisher to Marketplace support rather than promising an edit or bypassing the check.

## Completion

Before cloning, capture source metadata and package/attachment inventories needed to compare the result. Exclude deleted entries and preserve identity/version/default information. Missing preflight inventory evidence relevant to a required check blocks cloning; a feature that does not apply gets Not applicable.

Require an attributable, distinct draft ID under the same parent/compartment, status `NEW`, lifecycle `ACTIVE`, and work request `SUCCEEDED` when available. Correlate work request resources or source lineage with before/after sibling IDs. Never choose the newest revision if concurrent activity makes attribution ambiguous.

Compare supported copied metadata and package/attachment inventory by stable content identifiers, names, versions, and default flags. Resource IDs, timestamps, revision numbers, and lifecycle fields can legitimately differ. Do not promise every field is copied identically when the service does not expose that evidence. Report a missing expected item as verification pending; retain the draft ID for recovery. Re-read the source to check its publication status. A partial clone is not authority to delete, edit, submit, or publish it.
