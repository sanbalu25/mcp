# Publisher responses and recovery

Lead with the outcome or next action. Use listing/publisher names and revision numbers first; retain full OCIDs for exact selections and recovery. Show evidence without raw auth output, signed asset URLs, or long JSON dumps. Ask one focused question at a time, and avoid requesting information the session or listing can provide.

Use only OCI CLI for service actions. The publisher completes Oracle browser login and supplies any needed Console/Oracle confirmation themselves; no MCP server or browser automation is needed.

## Options and checks

Example layout only; populate it from real reads:

| Option | Listing | Revision | Status | Readiness / next action |
|---|---|---|---|---|
| 1 | Analytics Connector | 4 | Published | Ready to clone |
| 2 | Data Connector | 2 | Published as private | Existing draft: continue revision 3 |
| 3 | Reporting App | 7 | Published | Verify current OCMA status |

Show the account/compartment when it disambiguates otherwise identical names. Keep exact source/parent IDs mapped to each option; show the full selected source ID before cloning. Offer “Clone option 1” only for a ready option. Do not ask for public/private visibility: cloning produces a draft.

Summarize checks with a short table: **Check | Result | Evidence or next action**. For multiple candidates, share publisher-level evidence once and report revision/package differences separately. Use Pass, Needs attention, Needs verification, Not applicable consistently. Do not ask the publisher to approve away an unresolved check.

For a blocked OCMA check, say for example: “The installed OCI CLI does not show current OCMA validity. Check the agreement for OPN [observed number] in your Publisher account and provide its status and validity dates, or Oracle's current-active confirmation.” Reuse previously provided current, matching evidence; do not re-ask without a reason.

## Recovery decisions

| Situation | Action and publisher-facing explanation |
|---|---|
| CLI absent | Provide the official OCI CLI installation link; offer setup help. Do not silently install or upgrade |
| Browser login pending | Keep the original process/session context and wait in bounded intervals; say “Complete sign-in in the Oracle browser window.” Continue after completion without requiring a ritual “done” |
| Login canceled/timed out | Explain the outcome and offer another sign-in; do not spawn repeated login windows |
| Repeated invocation while login is pending | Resume that login; do not create another profile or callback listener |
| Browser selected an unexpected tenancy | Show the actual account and let the publisher choose the correct tenancy on a new login; do not query known-wrong compartments |
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

## Final response

Successful example shape (replace placeholders with verified values):

“Created draft revision [number] for [listing].
- New revision: [full OCID]
- Status: New / Active
- Source revision: [number and full OCID]
- Work request: [ID, if available]

You can now review and edit the draft in Marketplace → Publisher → Listings.”

Use an observed Console URL when available; otherwise provide the navigation path and ID instead of inventing a deep link. If verification is incomplete, lead with that and the exact remaining check. Preserve a created draft's ID even when copy verification fails. Do not label a draft approved or published.
