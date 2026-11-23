# Worked Example

Plan: migrate billing from provider A to provider B within one quarter, with no customer-visible downtime. Success: all invoices paid through provider B, charge failure rate not higher than today, no revenue loss. Horizon: six months.

## Synthesis

- Most likely failure: the migration runs late because the reconciliation logic for past invoices is more complex than planned.
- Most dangerous failure: a billing outage during cutover loses revenue and trust at the same time.
- Hidden assumption: that provider B supports the proration rules in the current contracts.
- Top recommendation: run the reconciliation in shadow mode for one full billing cycle before cutover.

## Finding 1, Proration Rules

Finding: invoices after cutover are wrong for mid-cycle upgrades.
Tag: Assumed

Why chain:

1. Why? Provider B computes proration from its own plan model.
2. Why? The plan model does not encode the contract rules in the current system.
3. Why? The migration mapped plans by price, not by contract terms.
4. Why? The plan treated billing as a pricing problem.
5. Why? The team had no owner for contract semantics during the migration.

Root cause: the migration plan lacks an owner for contract rules.

## Finding 2, Cutover Outage

Finding: no charges succeed for several hours on cutover night.
Tag: Speculative

Why chain:

1. Why? Both providers reject requests during the cutover window.
2. Why? The cutover relies on a hard switch with no dual-write period.
3. Why? The plan optimized for a short migration.
4. Why? A short migration was easier to schedule with the vendor.
5. Why? The risk of an outage was never priced into the schedule.

Root cause: schedule convenience overrode outage risk.

## Recommendation 1

```
Recommendation 1 (Impact: high)

Action: run provider B in shadow mode for one billing cycle and compare every invoice.
Timing: before launch
Role: billing engineer
Expected outcome: contract and proration mismatches surface before cutover.
Addresses: Finding 1
Early-warning sign: more than 0.5% of shadow invoices differ from the live system.
```
