# Publisher responses and recovery

Show only what the publisher needs to choose a listing/revision, complete an action, resolve a blocker, or understand the outcome. Use listing names and revision numbers first. Omit routine browser/profile details, session setup, tenancy discovery, sign-in success, and successful or not-applicable checklist rows unless requested. Keep exact IDs, checks, and evidence in the working record; show IDs only to disambiguate a choice, locate a draft, or troubleshoot. Never expose raw auth output, signed asset URLs, or long JSON dumps. Required disclosures from higher-priority instructions still apply.

Bundle independent missing details into one concise question when useful; avoid asking for information the session or listing can provide. Resolve ambiguous identity/source selection separately. A bundled reply is not blanket authorization or evidence that unrelated checks pass. When sign-in requires the publisher's action, say only: “Complete Oracle sign-in in the tab that opened.”

Use only OCI CLI for service actions. The publisher completes Oracle browser login and supplies any needed Console/Oracle confirmation themselves; no MCP server or browser automation is needed.

## Options and checks

Example layout only; populate it from real reads:

| Option | Listing | Revision | Status | Readiness / next action |
|---|---|---|---|---|
| 1 | Analytics Connector | 4 | Published | Ready to clone |
| 2 | Data Connector | 2 | Published as private | Existing draft: continue revision 3 |
| 3 | Reporting App | 7 | Published | Ready to clone |

Show the publisher or compartment only when needed to disambiguate names. Keep exact source/parent IDs mapped to each option and its production environment/endpoint in the working record; show a source ID only when needed to identify the chosen revision. Offer “Clone option 1” only for a ready option. Obtain the publisher's choice at the listing/revision level; an unambiguous existing request to clone that source already authorizes it and needs no repeated confirmation. Before cloning, identify the selected listing/revision and state that it will create a new draft. Do not ask for public/private visibility: cloning produces a draft.

Run and retain every check, recording whether it is blocking or advisory alongside Pass, Needs attention, Needs verification, or Not applicable. A ready option has passed all blocking checks; OPN/OCMA issues or missing evidence do not remove it from the choices or require acknowledgment. In customer responses, show only blockers and the specific next action; use a short list or **Blocker | Action needed** table when several apply. Share a publisher-level blocker once across affected candidates. If a detailed checklist is requested, include advisory results with their nonblocking role. Do not ask the publisher to approve away an unresolved blocking check.

Keep OPN/OCMA advisories for the [approval notice](#approval-notice) after successful cloning. Do not require agreement evidence, renewal, or acknowledgment before the clone. Apply [evidence reuse](checks.md#evidence-reuse) to current, matching evidence already available; missing evidence remains unverified, not expired.

## Observe the clone

Preserve request/work request IDs and the pre-clone sibling inventory. Resolve a missing draft ID from work request resources, source lineage, and before/after sibling IDs; never guess the newest revision. Once attributable, use that draft's direct GET instead of repeatedly discovering it.

- With a known work request, poll its projected GET; with only an attributable draft, poll its projected direct GET. Re-read the draft when work request state/resources change or verification is due. If neither ID is known, use scoped work request/sibling discovery to resolve the outcome.
- Back off between unchanged observations, for example 5, 10, 20, then 30 seconds, capped at 30 seconds. Reset on meaningful change. Bound the entire interactive observation to ten minutes and keep waits interruptible. Report meaningful progress, actionable results, or required next steps briefly; do not narrate each internal state transition or poll.
- Refresh all sibling pages when attributing an unknown draft, when operation state changes, after a conflict/timeout, and for final verification. With a known request and unchanged state/resources, avoid repeating broad discovery just because the draft ID is still unknown.
- On completion, perform every [completion check](checks.md#completion), including detailed copied-content/package/attachment comparisons and the source publication-status check. A projected status response is not full copy verification. Failed, canceled, ambiguous, or timed-out outcomes follow the recovery rules below; never resend an unresolved clone.

## Recovery decisions

| Situation | Action and publisher-facing explanation |
|---|---|
| CLI absent | Provide the official OCI CLI installation link; offer setup help. Do not silently install or upgrade |
| Browser login pending within this invocation | Keep its login process alive and wait in bounded intervals; say “Complete Oracle sign-in in the tab that opened.” A normal “done” reply continues this login; do not repeat the prompt while waiting |
| Login canceled/timed out | Explain the outcome and offer another sign-in; do not spawn repeated login windows |
| Repeated skill invocation or explicit restart | Start a fresh OCI login and unused OCI configuration profile through the same existing browser profile. First stop only the prior invocation's owned pending CLI login process so callbacks do not compete; leave the browser open. Explain only the sign-in action the publisher must complete |
| Login opens in a different Chrome profile or authenticators are unavailable there | Keep the pending CLI login alive and open its exact generated URL in the publisher's existing profile containing their authenticators. Resolve an unknown profile choice without changing browser data or starting another OCI login |
| Login completed without a supplied target tenancy | Automatically discover its compartments/listings without a tenancy confirmation or sign-in success announcement; retain authenticated identity and region in the working record |
| Browser selected a tenancy different from an explicitly supplied target | Briefly ask the publisher to sign in to the requested account, then verify it before discovery; do not query the known-wrong tenancy or ask the publisher to approve the mismatch |
| Production endpoint unreachable or denied | Explain the specific access/network blocker and next action; include endpoint/region details only when needed to resolve it. Do not try staging or beta endpoints |
| Supplied link or saved operation belongs to another environment | Resolve the environment mismatch before inspecting resources; do not carry source choices, approval evidence, or operation IDs across environments |
| OCI says NotAuthorizedOrNotFound | Say “OCI could not authorize this lookup or locate the resource.” Check account/region and the supplied resource once; ask for the intended Console link or administrator assistance. Do not say the resource is missing or that the session token is invalid |
| IAM compartment listing denied | Use a supplied listing/revision directly, or request a compartment/Console link; do not require broad IAM policy administration |
| No candidates | Explain whether discovery completed or was partial, and whether an existing draft or unsupported state explains the result |
| Existing active draft | Identify the listing and draft revision and suggest continuing it; include a verified Console link or ID when needed to locate it. Editing/deletion needs its own request |
| NEW draft is updating | Explain a clone/update may still be running; inspect its known request and wait, rather than submitting another clone |
| Stale ETag / state conflict | Re-read source, siblings, and work requests; show the changed result. Do not force or automatically retry |
| Network/command timeout after clone | Mark the outcome uncertain and inspect known request plus sibling inventory. Never resend until the earlier outcome is resolved |
| Definite clone failure | Report the service reason and smallest next action. Preserve request/partial draft IDs; show a draft ID to locate it and a request ID when troubleshooting needs it. Retry needs fresh checks and publisher authorization |
| Ten-minute observation limit reached | For a known nonterminal operation, say “Clone is still in progress for [listing].” If attribution or outcome remains unresolved, say “Outcome uncertain.” Preserve source/work request IDs and context so a later “check again” inspects the same operation; expose IDs only when needed to resolve the outcome |

A ten-minute observation limit bounds the current interactive wait; it does not cancel the service operation or prove failure. Do not create background monitoring unless requested.

Re-entering the skill to check a saved operation requires fresh login and verification of its target environment, endpoint, region, and tenancy. Preserve prior request/draft IDs for reconciliation, not the previous account as a login default. A new login never authorizes resending an unresolved clone.

## Final response

Successful example shape (replace placeholders with verified values):

“Created draft revision [number] for [listing]. Review and edit it in Marketplace → Publisher → Listings.

Draft ID: [full new revision OCID]”

Prefer a verified production Console link to the created draft when available; otherwise provide `https://cloud.oracle.com`, the navigation path, and draft ID instead of inventing a deep link. Do not add source/request IDs, a successful-check table, or sign-in details to a normal success response.

Blocked example shape (use only observed blockers):

“[Listing] already has draft revision [number]. Continue editing that draft before creating another. No new draft was created.”

Report every unresolved blocker needed for the next step without listing passed or inapplicable checks. If verification is incomplete after creation, lead with “Created, verification pending” and the exact remaining check. Preserve a created draft's ID even when copy verification fails and provide it or a verified link so the publisher can locate it. For unresolved attribution, say “Outcome uncertain”; preserve candidate IDs without selecting one. Do not label a draft approved or published.

## Approval notice

After verified clone success, show this separate bold callout immediately after the result whenever either OPN or OCMA is not verified as active/approved and currently valid. This prominent notice is required even in a concise response; do not bury it in a checklist or routine progress update:

> **ACTION REQUIRED BEFORE LISTING APPROVAL**
>
> **OCMA must be approved and current before this listing revision can be approved.**

Under the callout, add only applicable actions supported by the evidence:

- Expired OCMA: give its verified expiry date and ask the publisher to renew it and obtain approval.
- OCMA not approved/inactive: ask the publisher to obtain approval for a current agreement.
- OCMA unverified: say its status could not be verified and ask the publisher to check approval and validity before requesting listing approval. Do not claim it is expired.
- Inactive/expired OPN: state the observed status and expiry date when available, and ask the publisher to restore active membership. Unverified OPN: ask them to check its current status.

If OCMA is verified approved/current and only OPN needs attention, say OCMA approval is already verified and identify the OPN action; do not imply the agreement expired. If both advisories pass, omit the notice. The draft is created successfully even when this notice appears. Keep “Created, verification pending” or “Outcome uncertain” as the outcome when applicable; show the notice on later verified completion, not as a substitute for completion checks. Do not accept agreements, renew membership, submit, approve, or publish the revision as part of this skill.
