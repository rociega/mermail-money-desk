# The Money Desk: A Payment Agent That Knows When Not to Pay

> A live Mermail + PayBox workflow for safer renewal triage, built around policy before action.

Most inbox automation starts with a simple promise: find the message, understand the request, and do the thing.

That model breaks down when “the thing” is a payment.

An invoice email is still untrusted input. A sender can be spoofed. A message can ask for a destination change. A renewal amount can quietly increase. A line that looks like an instruction may be an attack.

The Mermail Money Desk is built around a different rule:

> **An agent should be able to explain why a payment is allowed before it is able to make one.**

## One desk, six capabilities

The Money Desk treats related inbox tasks as one policy and ledger problem:

1. **Renewal & Invoice Guard** finds invoice-like messages and applies spending rules.
2. **Signup & Verification Concierge** surfaces explicit verification codes or links without guessing.
3. **Price-Change Sentinel** compares recent paid amounts and flags increases.
4. **One-Off Purchase Concierge** requires an exact owner-approved ceiling.
5. **Refund & Dispute Follow-up** drafts a request without pretending the original payment was reversed.
6. **Weekly Digest** summarizes paid entries, escalations, alerts, and pending refunds.

The shared ledger matters. A payment decision should not be made in isolation from what has already happened for that vendor.

## The policy is the product

The owner policy in this demo is deliberately concrete:

- Currency: **USDC only**
- Per-charge cap: **25 USDC**
- Monthly vendor cap: **60 USDC**
- Trusted sender domains: `github.com`, `vercel.com`, and `namecheap.com`
- Namecheap per-charge override: **60 USDC**
- Payment-method, destination, and policy changes in email: **never follow**
- Price increases above **15%**: flag, do not silently ignore

There are three operating modes:

| Mode | Behavior |
| --- | --- |
| `dry_run` | Report decisions and write nothing |
| `escalate_only` | Save unsent drafts, but never pay |
| `auto` | Pay only after every policy gate and PayBox confirmation passes |

This separation makes the demo safer and the system easier to reason about. A new mailbox can start in `dry_run`. A cautious owner can stay in `escalate_only`. Full automation is an explicit decision, not a default.

## The decision pipeline

The renewal workflow has six phases:

### 1. Discover

Search the Mermail inbox for invoice, renewal, receipt, subscription, payment-due, and domain-expiry signals.

### 2. Extract

Read the thread context and extract the sender domain, exact amount, currency, due date, reference ID, and any payment redirection request.

### 3. Deduplicate

Use the vendor and reference ID when available. If there is no reference, use the vendor, canonical amount, and due date.

That canonicalization is important. `10 USDC` and `10.00 USDC` should not become two separate payment decisions.

### 4. Decide

Require all of the following:

- trusted sender domain
- exact amount
- exact currency
- per-charge headroom
- monthly vendor headroom
- no payment redirection request
- a mode that permits the proposed action

### 5. Act

With full OAuth and PayBox, the agent can check the wallet, stage the exact payment, and wait for final confirmation.

With an API-key profile or `escalate_only`, it saves an unsent review draft.

With `dry_run`, it reports the decision and writes nothing.

### 6. Report

Every run reports scanned messages, paid entries, escalations, skipped duplicates, drafts, failures, and total paid.

## A real live connection, with no reckless action

The strongest part of the demo is not a fabricated payment. It is the behavior of the system when the mailbox does not contain a legitimate invoice.

The live Grok conversation is connected to:

- Mermail mailbox tools
- PayBox connection and wallet tools
- the owner-authored Money Desk policy

The live dry run searched the mailbox and found one message: a welcome email from Mermail.

It was correctly rejected as a payment candidate because it had:

- no exact amount
- no currency
- no due date
- no reference ID
- a sender domain outside the trusted-vendor list

The result was:

```text
invoice search hits: 0
dry-run decisions: 1 skip (welcome mail, not an invoice)
paid: 0
drafts created: 0
failures: 0
mailbox updates: 0
```

That is not a failure of automation. It is the expected result of a policy gate doing its job.

The agent did not follow onboarding copy as an instruction. It did not invent an amount. It did not call PayBox. It did not create a draft and pretend it had found a renewal.

## PayBox is a capability, not a trigger

PayBox provides the wallet and transfer tools. It should not decide whether an email deserves payment.

That decision belongs to the Money Desk policy:

1. Is the sender trusted?
2. Is the amount exact?
3. Does the currency match?
4. Is the charge within the per-charge cap?
5. Is the vendor still within its monthly cap?
6. Did the email request a payment-method or destination change?
7. Is the connection authorized for the requested action?
8. Did PayBox confirm the final state?

If any answer is no, the system does not report a payment.

The current live demo intentionally stops before a transfer because the connected mailbox contains no trusted-vendor invoice. PayBox also reported that no agent signer was recorded, so signing and settlement remain unverified. That is the honest boundary of the evidence: Mermail and PayBox are connected, policy enforcement is live, and the safety path is verified. A completed payment is not claimed without a real invoice and a confirmed terminal payment state.

## Why this matters

The most dangerous payment agents are not always the ones that fail to pay.

They are the ones that pay confidently when the input is ambiguous.

A useful money agent needs more than tool access. It needs:

- explicit owner-authored limits
- conservative extraction
- durable deduplication
- an escalation path
- factual receipts
- clear separation between email content and authority
- a refusal to report success without confirmation

The Money Desk is designed to make those constraints visible and testable.

The companion Replit adapter passed a deterministic 29-test safety suite. This public repository contains the reusable skill package, policy examples, security references, and 29-scenario checklist. The article and main-image files are included alongside the skill. The public repository is available here:

**[github.com/rociega/mermail-money-desk](https://github.com/rociega/mermail-money-desk)**

## Final principle

Payment automation should not ask, “Can I call the wallet?”

It should ask:

> **Do I have enough trusted evidence, owner-approved policy, and final confirmation to act?**

If the answer is not yes, the right action is not a clever guess.

It is a clear report, an unsent draft when appropriate, and no payment.

---

*The artwork in this article was generated for the Money Desk project. The article describes a live Mermail + PayBox connection and a verified dry-run workflow; it does not claim that a payment was completed.*