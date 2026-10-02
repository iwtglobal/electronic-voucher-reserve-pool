# Glossary — Electronic Voucher Reserve Pool

Educational terminology for **electronic voucher reserve pool**, EVD voucher inventory reserve, and PIN stock reservation. Definitions are industry-oriented; vendor labels vary.

## Core Terms

| Term | Definition |
|------|------------|
| **Reserve pool** | Controlled set-aside of voucher inventory for a scoped owner |
| **Free stock** | Unreserved serials available for new reserves or sales |
| **Reserved stock** | Serials (or soft count) locked for a pool pending allocate |
| **Hard reserve** | Immediate binding of concrete serial numbers to a pool |
| **Soft reserve** | Quantity reservation; serials chosen at allocate time |
| **Allocate / sell** | Transition from reserved to sold or activated |
| **Release** | Return unused reserved stock to free |
| **Reserve TTL** | Time-to-live after which unused reserve auto-releases |
| **Batch** | Generation or import group of serials for audit |
| **Denom** | Face value product bucket within a voucher catalog |
| **Pool owner** | Dealer, API client, campaign, or channel that holds the reserve |
| **Inventory identity** | Invariant: free + reserved + sold (+ void) explains the batch |

## Flow States (Typical)

```
free → reserved → allocated/sold
         └→ released/expired → free
```

## Roles

| Role | Concern |
|------|---------|
| **Operator / warehouse** | Generates batches; monitors free vs reserved ageing |
| **Dealer / channel** | Requests reserves; sells from reserved stock |
| **Campaign manager** | Holds denoms for launches; releases after window |
| **POS / API** | Consumes reserves at fulfill time with idempotent keys |
| **Finance** | Reconciles inventory identity to GL and partner statements |
| **Support** | Explains why a serial is locked or was released |

## Design Notes

- Treat **reserve IDs** as permanent for retries and audits.  
- Prefer **TTL + ageing reports** so reserved stock cannot hoard forever.  
- Document **hard vs soft** clearly—mixed models confuse ops.  
- Keep PIN secrecy for reserved serials equal to free and sold stock.

## Related Reading

Live product reading: [evdsystem.com](https://evdsystem.com/), [About MoboGage](https://evdsystem.com/about-mobogage/).

MoboGage / EVD System materials on evdsystem.com describe electronic voucher distribution capabilities that depend on clear free / reserved / sold inventory states.
