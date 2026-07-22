# Solum — Security Audit

**Status: 🟡 UNDER REVIEW — the audit is in progress.**

This repository tracks the independent security review of the Solum protocol and will host the
**full audit report** once it is published. Solum's on-chain program has been submitted to
**OtterSec** for a comprehensive audit; OtterSec has accepted the engagement and is **currently
reviewing the code**.

> ### ⚠️ Read this first
> An audit that is *in progress* is **not** a clean bill of health, and **Solum is not "audited"
> yet.** Until OtterSec's final report is published in this repository, treat the protocol as
> **unaudited**. Solum is **devnet-only** today — no real value is at risk. Nothing here is a
> guarantee of safety, and nothing here is investment advice.

---

## What Solum is

Solum gives a memecoin a **real, redeemable floor**: a non-custodial on-chain vault that holds
tokenized stocks (e.g. Apple, NVIDIA, Tesla), which holders can redeem against, plus a
**stake-to-earn** engine that distributes reward stock to stakers. The vault is a program-derived
account with **no private key and no withdraw path** — value leaves only when a holder redeems or
claims their own share.

Design + threat model: [`SECURITY-ARCHITECTURE.md`](SECURITY-ARCHITECTURE.md) ·
Mechanics: [`MECHANISM.md`](MECHANISM.md), [`STAKING.md`](STAKING.md) ·
Full audit package: [`AUDIT.md`](AUDIT.md)

## What is under review (scope)

- The **Solum Anchor program** — the vault + redemption, `add_backing` (oracle-floored swap into
  the vault), the Pyth oracle path, and the stake-to-earn module.
- **Program source:** the Solum protocol repository (Anchor 0.31.1, Solana SBF / rustc 1.79),
  fully open-source.
- **Out of scope:** the test-only mock swap venue (never deployed to mainnet).

## The one invariant the whole design defends

> **No instruction can move a vault asset except (1) a holder redeeming their own pro-rata share,
> or (2) a swap that only ever *adds* backing.** There is no `withdraw`, `admin_withdraw`,
> `emergency_withdraw`, or `sweep`. Admin and engine keys can configure and trigger backing but can
> **never extract value, even if fully compromised.**

## Our posture going into the audit

Before engaging OtterSec, the code was hardened with:

- **Two internal adversarial review rounds** — 6 HIGH/MEDIUM findings identified and fixed
  (front-run vault takeover, funding-substitution, oracle pollution, redemption-freeze,
  venue-delegate-tampering, dust-floor). All are documented with fixes in [`AUDIT.md`](AUDIT.md).
- **25 deterministic adversarial tests** across redeem / backing / fees / hardening.
- **24 Rust unit tests** on the security-critical arithmetic (the oracle min-out floor, the
  redeem pro-rata payout, and the staking reward accumulator).
- **11 stake-to-earn integration tests** on a validator (proportional split, no pre-stake rewards,
  double-claim / cross-account / over-unstake all revert).
- **~9,800 fuzzed operations** against an off-chain reference model — every invariant held, zero
  violations.

None of this substitutes for the independent audit — it's the baseline OtterSec is reviewing.

## What happens next

1. OtterSec completes its review and delivers findings.
2. We remediate every finding.
3. **The full report and our remediation are published here**, and only then does Solum move toward
   a mainnet path (which additionally requires legal review).

Follow [@solumhq](https://x.com/solumhq) for the announcement when the report lands.

---

*Solum is devnet-only and pre-audit. This repository will be updated as the review progresses.
Nothing in it constitutes a claim that the protocol is audited, approved, or safe to use with real
funds.*
