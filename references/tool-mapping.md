# Tool mapping

Verify exact names against a live `tools/list` call before relying on them.
The API-key `agent-inbox` profile is sufficient for scan-only and
escalate-only work. PayBox requires the full-profile OAuth session.

| Phase | Purpose | Tool |
|---|---|---|
| Discover | Search candidate mail | `search_emails` |
| Extract | Read full thread content | `get_email_context` |
| Pay | Confirm wallet session | `get_paybox_connection` |
| Pay | Check delegated balance/headroom | live PayBox balance or allowance tool |
| Pay | Stage standard payment | `paybox_request_transfer` |
| Pay | Stage x402 payment | `paybox_pay_x402` |
| Pay | Confirm final state | status returned by the PayBox call |
| Paid | Post factual receipt | `reply_to_email` |
| Escalate | Save an owner-facing draft | `save_draft` |
| Process | Mark source state | `update_email` and supported label tools |
| Record | Write shared ledger | configured ledger location |

PayBox writes are direct tool calls, not mailbox destructive-action
confirmation flows. The skill's Decide phase is the policy gate.

When only the API-key adapter is connected, never attempt PayBox tools and
state that the run is `escalate_only`.