# Test scenarios

Any change to this skill should cover these scenarios:

1. Trusted in-policy charge is selected for payment only with full OAuth.
2. Untrusted vendor is escalated with an unsent draft.
3. Per-charge cap is exceeded and the full charge is escalated.
4. Monthly vendor cap is exceeded and the full charge is escalated.
5. Duplicate reference in one run is processed once.
6. Reference already paid in a prior run is skipped.
7. Payment-redirection request is always escalated.
8. Missing exact amount is escalated.
9. `escalate_only` never calls a payment tool.
10. `dry_run` writes neither drafts nor ledger rows.
11. Missing ledger access is reported explicitly.
12. Wallet decline is reported with the wallet's reason.
13. Ambiguous wallet state is not reported as paid.
14. Existing open escalation does not create a second draft.
15. Stale escalation is reported as stale, not silently re-drafted.
16. Currency mismatch is escalated without conversion.
17. Missing due date does not block reference-based dedupe.
18. No-reference duplicates use vendor, amount, and due date.
19. Signup verification code or link is surfaced without guessing.
20. Signup first invoice respects the explicit one-time cap.
21. Signup first invoice over the cap is escalated.
22. Price increase is flagged without blocking an otherwise valid payment.
23. One-off purchase without a ceiling asks for one.
24. One-off purchase over the stated ceiling is not paid.
25. Refund follow-up drafts and preserves the original paid row.
26. Weekly digest summarizes the configured period.
27. Quiet period still produces a digest or draft.
28. Missing digest recipient falls back to draft-only.
29. Digest send failure is retried once and reported.