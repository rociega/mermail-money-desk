# Example run walkthrough

Owner prompt: “Run the renewal guard on my Mermail inbox.”

Policy: `auto_pay_max_per_charge: 25`,
`auto_pay_max_per_vendor_per_month: 60`,
`trusted_vendors: [github.com, vercel.com, namecheap.com]`.

## Discover

Three unread messages match invoice signals:

1. GitHub Pro renewal from `billing@github.com`
2. Namecheap domain renewal from `noreply@namecheap.com`
3. A past-due invoice from `billing@quickinvoice-pay.io`

## Extract

1. GitHub — `10.00 USDC`, reference `gh-2026-09-pro`
2. Namecheap — `14.00 USDC`, reference `nc-example.dev-2026`
3. QuickInvoice — amount unclear, untrusted domain, and a request to use an
   updated wallet address

## Decide

GitHub and Namecheap satisfy the vendor and cap checks. QuickInvoice fails
the trusted-domain, exact-amount, and payment-redirection checks.

## Act

With full OAuth and an available PayBox connection, the first two charges
are paid exactly and receive factual thread receipts. QuickInvoice receives
an unsent escalation draft.

With an API-key or `escalate_only` session, all three receive unsent drafts;
no payment tool is called.

## Record

The run reports scanned, paid, escalated, skipped-duplicate, and draft
counts. Every action is recorded in the shared ledger.