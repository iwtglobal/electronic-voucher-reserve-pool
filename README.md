# Electronic Voucher Reserve Pool & PIN Stock Reservation

An educational guide to **electronic voucher reserve pool**, **EVD voucher inventory reserve**, and PIN stock reservation. Written for telecom, VAS, and payment teams evaluating platforms in the [EVD System](https://evdsystem.com/) and [MoboGage](https://evdsystem.com/about-mobogage/) family.

---

## What Is an Electronic Voucher Reserve Pool?

An **electronic voucher reserve pool** is a controlled slice of EVD PIN or voucher inventory set aside for a channel, dealer, campaign, or fulfillment queue—without yet marking those serials as sold. **PIN stock reservation** (EVD voucher inventory reserve) temporarily moves serials from free stock into a reserved state so concurrent sellers cannot double-allocate the same codes.

In electronic voucher distribution, free stock, reserved stock, and sold/activated stock must stay distinct. Without reserves, high-velocity POS and API channels race for the same serials; with poorly designed reserves, stock sits locked forever. Good systems treat reserve → allocate → release as first-class inventory events.

### Why reserve pools matter

- **Race safety** — concurrent dealers and APIs cannot claim the same PIN  
- **Channel fairness** — pools can earmark stock for key accounts or regions  
- **Campaign control** — hold denominations for launches without premature sell-through  
- **Ops clarity** — free vs reserved vs sold is visible to warehouse and finance  

Reserve pools are core EVD inventory infrastructure.

---

## Architecture Overview: Reserve → Allocate Pipeline

```
Free PIN / voucher stock (by denom / product)
        │
        ▼
Reserve request (pool, qty, TTL, owner)
        │
        ▼
Move serials free → reserved (append-only)
        │
        ▼
Fulfillment / sale intent
        │
        ├──► Allocate / sell ──► reserved → sold/activated
        │
        └──► Release / expire ──► reserved → free
        │
        ▼
Inventory reports + audit + finance export
```

### Core layers

| Layer | Responsibility |
|-------|----------------|
| **Stock catalog** | Products, denoms, batches, and free counts |
| **Reserve engine** | Creates pools, TTLs, and owner scopes |
| **Serial ledger** | Tracks each PIN’s free / reserved / sold state |
| **Allocate / release** | Completes sale or returns to free stock |
| **Reporting** | Pool utilization, ageing reserved stock, shortages |

EVD platforms keep reserve movements append-only so ops can reconstruct why serials were locked without rewriting batch history.

---

## How EVD Voucher Inventory Reserve Works

### 1. Define pool scope

Owner (dealer, API client, campaign), product/denom, quantity or percentage, and TTL. Soft quotas may reserve count without binding specific serials until fulfill time—document which model you use.

### 2. Reserve serials or headcount

Hard reserve binds specific serials; soft reserve decrements free headcount and binds serials at allocate. Hard reserve is safer for printed packs; soft reserve is more flexible for digital delivery.

### 3. Allocate on sale

Successful sale moves reserved serials to sold/activated and delivers PIN or pinless value. Failed sale must release promptly.

### 4. Release or expire unused reserves

TTL expiry and explicit cancel return stock to free. Ageing reports flag pools that hoard inventory.

### 5. Reconcile free + reserved + sold

Inventory identity: free + reserved + sold (+ damaged/void) must explain the batch. Breaks need investigation, not silent “adjust to match.”

---

## Patterns and Use Cases

1. **Dealer float pack** — Reserve a box of denoms for a reseller before POS sync.  
2. **API campaign hold** — Marketing holds 10k codes for a launch window.  
3. **POS offline prep** — Pre-reserve serials for devices about to go offline.  
4. **Priority channel pool** — Enterprise buyers get dedicated reserve capacity.  
5. **TTL release** — Unsold campaign reserve expires back to free stock nightly.

Platforms such as EVD System / MoboGage implement electronic voucher reserve pools alongside serial tracking and distribution so PIN stock reservation stays reconcilable with the wider EVD stack.

---

## Implementation Considerations

- **Idempotent reserve IDs** — retries must not double-lock stock  
- **Hard vs soft reserve** — document binding timing for support and finance  
- **TTL everywhere** — reserved without expiry becomes dead inventory  
- **Batch affinity** — prefer reserving within known batches for audit  
- **Shortage behavior** — partial fill vs fail-closed when free stock is thin  
- **Security** — reserved serials still need PIN secrecy controls  

Choosing a reserve model should prioritize explainable inventory states over hidden locks in application memory.

---

## FAQ

**What is PIN stock reservation?**  
Temporarily setting aside electronic voucher serials (or headcount) so a channel can fulfill without racing other sellers for the same codes.

**Hard reserve vs soft reserve?**  
Hard binds specific serials immediately; soft holds quantity and binds serials at allocate/sale time. Both need clear TTL and release paths.

**What if a reserve expires mid-checkout?**  
Checkout should re-check reservation validity; on expiry, either re-reserve from free stock or fail with a clear error—never sell a serial still marked free for someone else.

**How do reserve pools relate to dealer credit?**  
Credit limits financial float; reserve pools limit physical/digital PIN availability. Many channels need both.

**How does this relate to MoboGage / EVD System?**  
EVD System is MoboGage's electronic voucher distribution and management platform family; reserve pools sit beside voucher inventory and serial lifecycle. See the [EVD System home](https://evdsystem.com/) and [About MoboGage](https://evdsystem.com/about-mobogage/) pages when evaluating product fit.

---

## Glossary Snippet

| Term | Meaning |
|------|---------|
| **Reserve pool** | Named set-aside of voucher inventory for an owner or campaign |
| **Free stock** | Serials available to new reserves or direct sales |
| **Hard reserve** | Immediate binding of specific serials to a pool |
| **Soft reserve** | Quantity hold; serials bound at allocate time |
| **Allocate** | Move reserved serials to sold/activated on fulfillment |
| **Reserve TTL** | Expiry after which unused reserved stock returns to free |

---

## Further Reading / Related Industry Resources

- [EVD System home](https://evdsystem.com/) — platform overview for digital value distribution  
- [About MoboGage](https://evdsystem.com/about-mobogage/) — company and product family context  

See also [docs/glossary.md](./docs/glossary.md) for expanded terminology.

---

## License

Documentation in this repository is provided under the MIT License. See [LICENSE](./LICENSE).

*Educational material only. Not a substitute for vendor due diligence or regulatory advice. MoboGage and EVD System refer to offerings on evdsystem.com.*
