# Board Risk Briefing: October 2026 Security Incident

**Project BLACKSWAN II, Interim assessment. Prepared for the Board / Board Risk Committee. Status as of 13 November 2026. This briefing is based on public evidence. Figures attributed to Binance are management representations that have not been independently verified.**

---

## What happened, in plain terms

Between June and October 2026, attackers attributed by the FBI to North Korea's TraderTraitor group worked their way from our people's laptops to the service that signs transactions for our hot wallets. In June, one wallet engineer installed a tampered copy of a legitimate development tool. Our security team detected that and cleaned it up, but the attackers had already learned who was on the wallet team. In September, a second engineer was run through a convincing fake job interview, with video calls with a fake HR representative and a fake CEO, and persuaded to download a coding "assignment" that infected his work computer. Our endpoint tools did not detect it. In October the attackers used an internal AI tool to reach two more engineers' machines, found a stored deployment token, and on 29 October used it to reach the signing service.

They did not steal our keys. They persuaded our own signing service to sign for them. On Ethereum they used a newer account feature (EIP-7702) to make one major hot wallet pay out to them again and again. In roughly twenty minutes on the evening of 29 October, about **$6.4 billion** left our hot wallets across ten or more blockchains. Losses were contained over the following hours as stablecoin issuers froze funds at our and authorities' request, and we revoked the attackers' authorisations shortly after midnight.

Two facts frame everything else. First, the entry tactic was not a surprise. Our Chief Security Officer had described this exact fake-interview method publicly as a worry in July. Second, no customer has been reported to have lost money. That is because the company absorbed the loss, not because the stolen funds came back.

## The money: kept as separate measures, because they are often confused

| Measure | Figure | Status |
|---|---|---|
| Stolen | $6.34B (Binance) to $6.43B (Elliptic); TRM $6.36B, Chainalysis ~$6.4B | Corroborated range |
| Frozen / blocked | ~$2.7B to $2.85B, almost entirely issuer-controlled stablecoins | Corroborated range |
| Of the frozen amount, under US court-authorised seizure/restraint | More than $1.2B (US DOJ, 12 Nov) | Official statement |
| Still held by the attacker | ~$3.0B, mostly Ether and Bitcoin no issuer can freeze (Elliptic, 10 Nov) | Analytics estimate |
| Actually recovered and returned | **Never disclosed by anyone** | Unknown |
| Customer coverage | Binance announced "coverage complete" on 12 Nov, funded from the SAFU reserve, futures insurance funds and corporate treasury | **Management representation**: replenishing reserves with company money; it is not recovery of the stolen assets |

For the largest proof-of-reserves snapshot (12 November, 198 tokens), the cryptographic proof that reserves back customer balances at least 1:1 was still being generated at the time of writing. It is expected between 13 November 16:00 and 14 November 02:00 UTC. Until it is published and independently checked, "coverage complete" rests on the company's own statement. The proof for the 31 October snapshot was completed and published on 2 November. Whether the 4 November snapshot's proof was ever completed has not been announced.

## Operational impact

- Withdrawals were paused on the evening of 29 October and have reopened in phases from 1 November (batches on 1, 3, 5 and 6 November). They were **still phased on 12 November**. No date for full reopening has been announced.
- Customers withdrew heavily when withdrawals resumed. Net outflows of $4.72B were reported as of 2 November (Binance to Reuters). A blockchain-analytics measure recorded ~28,316 BTC in net outflows from Binance wallets to 5 November, though that measure notes exchange Bitcoin balances had already been falling for four days before the attack, so it cannot all be laid on the incident.
- No customer loss has been reported by any source. Customers did bear roughly two and a half days without withdrawals and restricted access thereafter. That is a real customer harm, and "no loss" wording does not capture it.

## Governance concerns the Board should focus on

1. **A known attack succeeded anyway.** The fake-interview tactic was a named, leadership-level concern months earlier, and a related compromise was detected in June. The gap is not awareness. It is whether awareness was converted into controls on the specific people and machines that mattered.
2. **One path led to everything.** A deployment token on a laptop, an internal tool able to act on other machines, and a single signing service covering every chain together meant one intrusion could reach the whole hot-wallet estate. There was no internal wall left to stop it.
3. **Paying for the loss depended on reserves governed by policy, not by a stress case of this size.** The SAFU fund appears to have been drawn by roughly half. The Board has not yet seen a final account of how the coverage cost was split between SAFU, insurance funds and treasury, or confirmation that SAFU has been restored to its ~$1 billion target.
4. **Our public numbers moved.** Coverage was reported at 90% on 31 October and later restated as 98% for the same date. Acknowledgement times differ between published timelines. In a trust business, inconsistent figures are a risk in themselves.

## Remediation overview

A 22-item programme is set out in the accompanying roadmap. It starts with immediate actions: safe handling of external code, endpoint tool integrity, removal of standing tokens from laptops, signing-output monitoring with automatic freeze thresholds, and a pre-tested, timed revocation capability. Actions for days 31 to 90 follow: hard segmentation to the signing service, a signing policy engine with value limits, a board-approved cap on hot-wallet holdings, standing freeze-coordination arrangements with issuers, a proof-of-reserves completion discipline, and a single-source-of-truth rule for public figures. Then comes structural work over 3 to 12 months: segregated signing per chain, phishing-resistant identity for privileged access, an independently audited custody assurance cycle, and a published transparency standard for incident reporting.

## Decisions requested of the Board / Risk Committee

1. **Approve a maximum hot-wallet share of total reserves**, with automatic sweeping to cold storage above the cap.
2. **Approve the signing-architecture segregation programme** (separate signing and approval paths per chain and wallet tier) and its funding.
3. **Adopt a documented coverage and reserve policy**: sizing method, funding order, and a deadline by which SAFU-class reserves must be refilled after any use. Also require a final coverage allocation report for this incident.
4. **Commission the independent review with a commitment to publish** its conclusions, and set a deadline for the final post-mortem.
5. **Adopt the transparency standard**: a single verified figures log behind every public number, and proof-of-reserves proofs published within their stated windows, with any miss escalated to this committee.

## What we do not yet know

> - The exact time our own systems detected the attack, or whether internal detection beat public reporting.
> - Which internal AI tool was abused, and how.
> - Whether any stolen funds have actually been recovered (as opposed to frozen).
> - The final split of coverage funding, and whether SAFU has been fully replenished.
> - Whether the 4 November proof-of-reserves proof was completed.
> - When withdrawals will fully reopen.
> - When the final post-mortem and the invited independent review will be published.

*This briefing will be revised when the final post-mortem, the independent review, or the 12 November proof-of-reserves verification materially changes any statement above.*
