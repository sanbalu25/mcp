# Publisher responses and recovery

Lead with the outcome or next action. Use listing/publisher names and revision numbers first; retain full OCIDs for exact selections and recovery. Show evidence without raw auth output, signed asset URLs, or long JSON dumps. Bundle independent missing details into one concise question when useful; avoid asking for information the session or listing can provide. Resolve ambiguous identity/source selection separately. A bundled reply is not blanket authorization or evidence that unrelated checks pass.

Use only OCI CLI for service actions. The publisher completes Oracle browser login and supplies any needed Console/Oracle confirmation themselves; no MCP server or browser automation is needed.

## Options and checks

Example layout only; populate it from real reads:

| Option | Listing | Revision | Status | Readiness / next action |
|---|---|---|---|---|
| 1 | Analytics Connector | 4 | Published | Ready to clone |
| 2 | Data Connector | 2 | Published as private | Existing draft: continue revision 3 |
| 3 | Reporting App | 7 | Published | Verify current OCMA status |

Label choices **Production — Ashburn** and show the account/compartment when it disambiguates otherwise identical names. Keep exact source/parent IDs mapped to each option and its environment/endpoint; show the full selected source ID before cloning. Offer “Clone option 1” only for a ready option. Obtain the publisher's choice at the listing/revision level; an unambiguous existing request to clone that source already authorizes it and needs no repeated confirmation. Do not ask for public/private visibility: cloning produces a draft.

Summarize checks with a short table: **Check | Result | Evidence or next action**. For multiple candidates, share publisher-level evidence once and report revision/package differences separately. Use Pass, Needs attention, Needs verification, Not applicable consistently. Do not ask the publisher to approve away an unresolved check.

For a blocked OCMA check, say for example: “The installed OCI CLI does not show current OCMA validity. Check the agreement for OPN [observed number] in your Publisher account and provide its status and validity dates, or Oracle's current-active confirmation.” Apply [evidence reuse](checks.md#evidence-reuse) to current, matching confirmations.

## Observe the clone

Preserve request/work request IDs and the pre-clone sibling inventory. Resolve a missing draft ID from work request resources, source lineage, and before/after sibling IDs; never guess the newest revision. Once attributable, use that draft's direct GET instead of repeatedly discovering it.

- With a known work request, poll its projected GET; with only an attributable draft, poll its projected direct GET. Re-read the draft when work request state/resources change or verification is due. If neither ID is known, use scoped work request/sibling discovery to resolve the outcome.
- Back off between unchanged observations, for example 5, 10, 20, then 30 seconds, capped at 30 seconds. Reset on meaningful change. Bound the entire interactive observation to ten minutes and keep waits interruptible. Report status changes and actionable results; do not echo unchanged JSON or each poll.
- Refresh all sibling pages when attributing an unknown draft, when operation state changes, after a conflict/timeout, and for final verification. With a known request and unchanged state/resources, avoid repeating broad discovery just because the draft ID is still unknown.
- On completion, perform every [completion check](checks.md#completion), including detailed copied-content/package/attachment comparisons and the source publication-status check. A projected status response is not full copy verification. Failed, canceled, ambiguous, or timed-out outcomes follow the recovery rules below; never resend an unresolved clone.

## Recovery decisions

| Situation | Action and publisher-facing explanation |
|---|---|
| CLI absent | Provide the official OCI CLI installation link; offer setup help. Do not silently install or upgrade |
| Browser login pending within this invocation | Keep its login process alive and wait in bounded intervals; say “Complete sign-in in the Oracle browser window.” A normal “done” reply continues this login |
| Login canceled/timed out | Explain the outcome and offer another sign-in; do not spawn repeated login windows |
| Repeated skill invocation or explicit restart | Start a fresh OCI login and unused OCI configuration profile through the same existing browser profile. First stop only the prior invocation's owned pending CLI login process so callbacks do not compete; leave the browser open |
| Login opens in a different Chrome profile or authenticators are unavailable there | Keep the pending CLI login alive and open its exact generated URL in the publisher's existing profile containing their authenticators. Resolve an unknown profile choice without changing browser data or starting another OCI login |
| Login completed without a supplied target tenancy | Show actual tenancy name/OCID suffix and region as information, then automatically discover its compartments/listings without asking for tenancy confirmation |
| Browser selected a tenancy different from an explicitly supplied target | Repeat login for the requested account and verify it before discovery; do not query the known-wrong tenancy or ask the publisher to approve the mismatch |
| Production endpoint unreachable or denied | Report the production endpoint/region and specific failure; resolve network/access requirements. Do not try staging or beta endpoints |
| Supplied link or saved operation belongs to another environment | Resolve the environment mismatch before inspecting resources; do not carry source choices, approval evidence, or operation IDs across environments |
| OCI says NotAuthorizedOrNotFound | Say “OCI could not authorize this lookup or locate the resource.” Check account/region and the supplied resource once; ask for the intended Console link or administrator assistance. Do not say the resource is missing or that the session token is invalid |
| IAM compartment listing denied | Use a supplied listing/revision directly, or request a compartment/Console link; do not require broad IAM policy administration |
| No candidates | Explain whether discovery completed or was partial, and whether an existing draft or unsupported state explains the result |
| Existing active draft | Show its ID and suggest continuing the draft; editing/deletion needs its own request |
| NEW draft is updating | Explain a clone/update may still be running; inspect its known request and wait, rather than submitting another clone |
| Stale ETag / state conflict | Re-read source, siblings, and work requests; show the changed result. Do not force or automatically retry |
| Network/command timeout after clone | Mark the outcome uncertain and inspect known request plus sibling inventory. Never resend until the earlier outcome is resolved |
| Definite clone failure | Report the service reason, request ID, and any partial draft ID. Recommend the smallest next action; retry needs fresh checks and publisher authorization |
| Ten-minute observation limit reached | Say “Clone still in progress” with source and work request IDs; preserve context so a later “check again” inspects the same operation |

A ten-minute observation limit bounds the current interactive wait; it does not cancel the service operation or prove failure. Do not create background monitoring unless requested.

Re-entering the skill to check a saved operation requires fresh login and verification of its target environment, endpoint, region, and tenancy. Preserve prior request/draft IDs for reconciliation, not the previous account as a login default. A new login never authorizes resending an unresolved clone.

## Final response

Successful example shape (replace placeholders with verified values):

“Created production draft revision [number] for [listing].
- New revision: [full OCID]
- Status: New / Active
- Source revision: [number and full OCID]
- Work request: [ID, if available]

You can now review and edit the draft in the production Console: Marketplace → Publisher → Listings.”

Use a verified production Console URL when available; otherwise provide `https://cloud.oracle.com`, the navigation path, and ID instead of inventing a deep link. If verification is incomplete, lead with that and the exact remaining check. Preserve a created draft's ID even when copy verification fails. Do not label a draft approved or published.
