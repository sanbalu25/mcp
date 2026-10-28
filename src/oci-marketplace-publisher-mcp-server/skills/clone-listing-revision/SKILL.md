---
name: clone-listing-revision
description: Helps OCI Marketplace publishers clone an eligible listing revision into a new draft in production. Use to clone a production revision or find eligible production sources; signs in through OCI CLI, discovers the authenticated tenancy's listings, and checks eligibility before cloning.
---

# Clone listing revision

Guide a publisher from Oracle sign-in to a verified new draft under the same listing. A bare `$clone-listing-revision` invocation starts automatic eligibility discovery and presents explicit clone choices; the publisher's selection authorizes the clone. An explicit inspection-only request remains read-only. Run the CLI work for them and explain results in plain language. The only service integration required is the installed OCI CLI, which Codex runs through the local terminal. The publisher completes OCI login in their existing browser profile with their usual authenticators. Creating or reviewing the skill is not a request to clone a live resource.

Keep customer messages focused on listing/revision choices, concrete blockers and required actions, and the final result. Handle browser/profile selection, session validation, tenancy discovery, and successful checks without routine narration. Ask for login interaction only when needed. Include technical details or a full checklist when requested or necessary to resolve a problem; keep required progress updates brief and task-focused.

Load [OCI access and commands](references/oci-cli.md) before access, [required checks](references/checks.md) before deciding eligibility, and [publisher responses and recovery](references/publisher-flow.md) when presenting choices, observing a clone, or handling a blocker. Reuse already loaded references unless their contents change.

## OCI CLI-only execution

Use `oci` commands for authentication, discovery, checks, cloning, and status polling. Do not discover, call, or ask the publisher to install MCP servers, connectors, browser automation, marctl, internal APIs, or a separate SDK. Normal Codex terminal/file access is sufficient. Do not introduce Python, jq, or another utility as an installation prerequisite; interpret CLI JSON using the capabilities already available.

Any Console step is performed by the publisher, not browser automation. Treat a pasted Console link as a resource locator: extract an OCID/region only when present and verify them through OCI CLI; otherwise ask for the resource OCID. For blocking checks absent from installed CLI output, request the specific publisher-provided evidence described below and keep the check blocked until verified. Unavailable work-request listing or GET is a visibility limitation, not a blocking prerequisite: use current parent/source/sibling state and accessible known requests, retain the limitation internally, and let the service validate concurrent conflicts on clone. Do not ask the publisher to check Console activity or reconfirm the clone because those reads are unavailable. OPN and OCMA are advisory: missing evidence for them does not delay cloning. Never require a missing integration or invent an API/CLI command to fill the gap.

This version targets **Production — Ashburn (`us-ashburn-1`)**. Use `https://cloud.oracle.com` for Publisher Console guidance and the explicit production Publisher endpoint in [environment routing](references/oci-cli.md#production-environment) for every Publisher read, clone, and poll. Mention the environment when needed to distinguish a choice or resolve a problem. Do not use staging or beta endpoints or infer a tenancy from the environment. This skill supports production only. Another production region requires verified production routing before access.

## 1. Sign in to the publisher account

At every new invocation, including a repeated `$clone-listing-revision` invocation or explicit restart, begin the requested inspection or cloning task. Check OCI CLI availability; if missing, give the official setup link in the command reference and offer setup assistance. When login needs the publisher's input, say only: “Complete Oracle sign-in in the tab that opened.” Do not announce browser/profile choices, session creation, or successful sign-in unless they ask or a problem requires it.

Run a new `oci session authenticate` with an unused dedicated OCI profile and the [browser login](references/oci-cli.md#browser-authentication) procedure. Use the publisher's existing browser profile by default and retain their browser/profile choice across replies. A new OCI configuration profile does not require a new Chrome profile. Do not create a temporary browser profile, use incognito, or clear browser data. Never reuse an earlier invocation's token, saved OCI profile, tenancy, or compartment, even if its session is valid. Browser cookies and authenticators remain available while OCI creates a new session. Let the publisher complete Oracle sign-in and MFA themselves.

Ordinary replies such as “done,” source selection, and polling continue the current invocation; they do not start another login. If a fresh invocation replaces a pending login, stop only that invocation's owned CLI login process before starting the new one; do not leave competing callbacks or close the publisher's browser. After login, validate the new session and bind all reads and the clone to its explicit config, profile, region, and `--auth security_token`. Add the production endpoint to every Publisher call; keep OCI identity calls on their own service endpoint.

Use the configured production region and derive the tenancy from this invocation's validated session. Record the authenticated tenancy and region internally, then automatically discover its listings without tenancy-level confirmation or a sign-in status report. Show account details only to distinguish listings or resolve an explicit target mismatch. If the publisher explicitly supplied a different target tenancy, resolve that mismatch through a new login before discovery; do not query the known-wrong account or ask permission to accept the mismatch. If a requested tenancy name cannot be verified, ask for its OCID to resolve the target. A valid token does not establish production access or ownership of a selected listing; verify those through the reads below. Never collect passwords, MFA codes, keys, or token contents in chat.

## 2. Find the source

Accept a listing name, revision OCID, listing OCID, or Oracle Console link. A name is a search hint, not a unique identifier. Resolve a supplied revision to its parent listing. For an ambiguous name, show matches with compartment and publisher names; let the publisher choose. If no target was supplied, discover listings in the authenticated tenancy and offer a short selection list instead of requiring the publisher to locate OCIDs.

Prefer the supplied listing or compartment. Otherwise search the tenancy root and accessible active compartments, including the root explicitly. Describe accessible compartments as places being searched, not as the publisher's confirmed location. If discovery is denied, use a supplied resource directly or ask for its Console link/compartment. Never equate access denied or a partial search with “no listings.” Do not repeatedly search a scope already known to be wrong or denied.

Read the parent listing, its actual publisher, and all sibling revision pages. Automatically check supported sources and offer eligible revisions as clone choices when cloning is requested. A listing-only discovery choice narrows the search; it does not authorize a revision that has not been identified. An existing active/updating `NEW` draft blocks another clone: show that draft and the appropriate next step. Check a narrowed candidate before performing expensive inventory reads; do not require publisher-wide package scans to inspect one source.

Use the command reference's output projections for discovery and polling. Keep the [scoped evidence record](references/checks.md#evidence-reuse) to deduplicate reads within a discovery pass and share publisher evidence across candidates. These optimizations do not replace detailed validation or the complete final refresh below.

## 3. Check eligibility

Evaluate all applicable rules in [required checks](references/checks.md), including:

- Blocking: session identity, publisher ownership, approved registration, and a non-null publisher `logo` field.
- Active parent/source, supported source status, no conflicting draft or observed conflicting operation; unavailable work-request reads alone do not block.
- For package sources: usable packages, exactly one default, unique version names within the source, and no whitespace in package names or versions.
- Advisory: OPN membership and OCMA status/validity. Inactive, expired, unapproved, or unverified OPN/OCMA evidence does not block cloning or require a separate acknowledgment.

Record each check's blocking/advisory role and **Pass**, **Needs attention**, **Needs verification**, or **Not applicable** with evidence internally. Only blocking checks with Needs attention or Needs verification prevent cloning. Show the publisher the blockers and actions they need; a full checklist is optional when requested. Missing CLI response fields are not failures or passes. First use supported OCI CLI reads; request precise current Console/Oracle evidence only when a blocking check needs it. Work-request visibility is recorded separately from eligibility, without a Console activity request. Actual nonterminal conflicting operations or an uncertain earlier clone still require reconciliation before any new clone. Retain available OPN/OCMA evidence and unknowns for the post-clone notice without asking the publisher to resolve them before cloning. Record publisher/OPN identity, source, validity, and verification time. A generic “yes” must not silently resolve multiple unrelated checks.

Preserve logo/package and other blocking readiness checks. Treat OPN and OCMA as approval reminders, separate from clone eligibility; do not claim they are service-enforced clone restrictions. An actual service rejection still stops the operation; never force or bypass it. Offer concrete next steps, not raw exceptions or a blanket request to “fix permissions.” Never repair a published source, delete a draft, renew memberships, accept agreements, or change statuses as an implicit part of cloning.

## 4. Select and clone

Show ready revisions as numbered clone choices and show blockers separately using the compact format in [publisher responses](references/publisher-flow.md). Keep an exact mapping from each option to the full source and parent IDs. State with the choices that selecting an option creates a new draft under that listing. Include publisher/compartment details only when needed to distinguish the source. Keep successful checks in the evidence record. Public/private visibility selection is unnecessary for cloning.

An unambiguous selection such as “1,” “option 1,” or “Clone option 1” in response to those explicit clone choices authorizes that displayed source. Immediately refresh and clone it without asking the publisher to confirm again. If the original request already unambiguously identifies a source and asks to clone it, proceed once all blocking checks pass without another confirmation. For an inspection-only request, present inspection choices; choosing one continues inspection and does not authorize a clone. Never choose among multiple sources on the publisher's behalf.

Immediately before cloning, refresh the source, parent, publisher, all sibling pages, package inventory/details, and accessible relevant operation evidence; verify external evidence for blocking checks is still current. Re-run all blocking checks and update OPN/OCMA advisories from available evidence. If anything affecting identity, source content, or blocking eligibility changed, show the new result and resolve that change before proceeding. Unchanged eligible state proceeds directly to the clone under the existing authorization. An advisory-only change or unavailable work-request read does not require another confirmation or delay the clone. Snapshot the source ETag and sibling IDs.

Send one clone request with the latest ETag when available and automatic mutation retries disabled. An ETag protects the source; it cannot prevent a concurrent sibling draft, which the service still validates. Preserve returned request/work request IDs. Do not use a live clone call as a compatibility or permissions test.

## 5. Verify and hand back the draft

Follow [targeted polling](references/publisher-flow.md#observe-the-clone): poll known operation/draft IDs with backoff for up to ten minutes, expanding sibling discovery when attribution, state changes, conflicts, timeout, or final verification require it. A successful command or accepted request alone is not completion.

Report success only for a distinct, attributable revision under the same listing with `status == NEW` and `lifecycle-state == ACTIVE`, plus a successful work request when available. Verify copied content/inventory as described in the checks. For an attributable draft with incomplete copy verification, report “Created, verification pending” with its ID. If attribution is unresolved, report “Outcome uncertain” and retain any candidate IDs without choosing one. Preserve the source's publication status and flag unexpected changes.

After a successful clone, if either OPN or OCMA is not verified as active/approved and currently valid, show the prominent [approval notice](references/publisher-flow.md#approval-notice) immediately after the result. State that OCMA must be approved and current before the listing revision can be approved, and include only observed issues or clearly labeled unknowns. This notice does not change clone success or authorize submission/approval. Do not claim clone completion while it is pending, failed, or uncertain.

On timeout or interruption, retain nonsecret context in the chat: environment/endpoint, tenancy, region, parent/source IDs, baseline sibling IDs, and known request/draft IDs. A new invocation must authenticate afresh and verify the same target environment and tenancy before inspecting that saved operation; keep old session details only as history, never as credentials to reuse. Do not issue another clone. Handle definite failures through [recovery guidance](references/publisher-flow.md). Finish with the new draft ID and next action. Editing, submission, and publication require separate requests.

## Example requests

- “Use $clone-listing-revision to show which of my listings I can clone.”
- “Use $clone-listing-revision to clone the published revision of Analytics Connector.”
- “Use $clone-listing-revision to check this listing before cloning: <Console link>.”
