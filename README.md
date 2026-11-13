# Project BLACKSWAN II

## Enterprise Cyber Risk & GRC Assessment of the October 2026 Binance Exchange Security Incident

*An interim post-incident security governance, risk assessment, control effectiveness and remediation engagement.*

| | |
|---|---|
| **Role** | Lead GRC Engineer |
| **Engagement type** | Independent cybersecurity risk assessment, **INTERIM** |
| **Subject incident** | Binance centralised-exchange hot-wallet / wallet-signing-service compromise, 29 October 2026 (UTC) |
| **Frameworks** | NIST CSF 2.0 · NIST SP 800-53 Rev. 5 · ISO/IEC 27001:2022 · COBIT 2019 · CIS Controls v8 · SOC 2 TSC (by analogy) |
| **Evidence basis** | Public sources only, individually cited (32 sources indexed) |

> **Companion project.** [Project BLACKSWAN](https://github.com/tearbu/project-blackswan) assessed the **2022 BNB Chain bridge exploit**. That was protocol infrastructure, a different entity and a different incident. This project assesses the **2026 Binance exchange** compromise. Mixing the two up is exactly the category error both reports are written to avoid.

> **Interim status.** This assessment was prepared roughly two weeks after the incident. Binance has published preliminary findings (3 Nov 2026) but no final root-cause report, and there is no independent review. So the report grades every material claim as **corroborated**, **operator-reported**, or **cannot yet be established**. It will need revision when the final post-mortem appears. The methodology lists the revision triggers.

---

## Headline facts (all cited in the report)

- On 29 October 2026, attackers reached the internal **wallet-signing service** and made Binance's hot wallets transfer assets outside the normal withdrawal process. They had spent months inside Binance's engineering environment. Initial access came via a tampered developer-tool download in June and a **fake job interview** in September, per Binance's preliminary report. On Ethereum, 50 pre-signed **EIP-7702 authorisations** were used to sweep a hot wallet repeatedly. Revocation took ~3h50m, a latency Binance attributes to its MPC design.
- **The money, kept as separate measures:** stolen **$6.34B to $6.43B** depending on source (Binance / TRM Labs / Chainalysis / Elliptic); frozen **~$2.7B to $2.85B**; over **$1.2B** of the frozen funds under court-authorised restraint (US DOJ, 12 Nov); **~$3.0B still held by the attacker**, mostly ETH and BTC that cannot be frozen; and the amount actually **recovered has never been disclosed**.
- The FBI attributed the theft to North Korea's **TraderTraitor** on 31 October and published 628 addresses. Binance's CSO had publicly described the fake-interview tactic in **July**. The report treats that as a question about turning threat intelligence into controls. It is evidenced and framed carefully, not presented as a proven governance failure.
- Binance states customer coverage is complete, funded from its SAFU fund, futures insurance funds and corporate treasury. That is reserve replenishment from Binance's own money, **not recovered funds**, and this report records it as a management representation. One coverage figure given at the time (90% on 31 Oct) was later restated as 98% in Binance's own preliminary report. The discrepancy is analysed, not smoothed over.
- **Binance's detection time is unknown.** Binance references an undated "internal alert". Public on-chain reporting appeared within minutes. No credible source establishes when Binance's own systems detected the theft, and this report claims none.

## Deliverables

| Artefact | Location |
|---|---|
| Full interim investigation report (15 sections, ~24,600 words, attack-flow / attack-tree / IR decision diagrams) | [`report/REPORT.md`](report/REPORT.md) |
| Enterprise risk register, 16 risks, inherent & residual scoring (5×5), owners, KRIs | [`data/risk-register.csv`](data/risk-register.csv) |
| Control-gap assessment matrix, 15 domains, design & operating effectiveness, gap classification, test procedure per domain | [`data/control-gap-matrix.csv`](data/control-gap-matrix.csv) |
| Framework crosswalk, 33 mappings across NIST CSF 2.0, SP 800-53, ISO 27001:2022, COBIT 2019, CIS v8, SOC 2 | [`data/framework-crosswalk.csv`](data/framework-crosswalk.csv) |
| Remediation roadmap, 22 recommendations across 4 phases, with owners, success criteria and evidence of completion | [`data/remediation-roadmap.csv`](data/remediation-roadmap.csv) |
| Internal audit & control testing plan, 7 fully specified tests, incl. a timed revocation drill and replay of the 50-authorisation pattern | [`audit/audit-test-plan.md`](audit/audit-test-plan.md) |
| Board-level risk briefing, including an explicit "what we do not yet know" section | [`board/board-briefing.md`](board/board-briefing.md) |
| Executive presentation (17 slides) | [`slides/Project-BLACKSWAN-II-Executive-Presentation.pptx`](slides/Project-BLACKSWAN-II-Executive-Presentation.pptx) |
| Interim assessment methodology, evidence hierarchy, limitations and revision triggers | [`methodology/methodology.md`](methodology/methodology.md) |
| Evidence index (32 sources), conflicting-figures register (CF-01 to CF-09), "could not verify" list | [`evidence/evidence-and-sources.md`](evidence/evidence-and-sources.md) |

## The five questions the report answers

1. What happened, technically and operationally, and which parts of that account are corroborated, operator-reported, or still unknown?
2. Why did the control environment allow a months-long intrusion to reach the signing service?
3. Which governance, risk and compliance weaknesses can *reasonably* be identified on current evidence?
4. What should have been in place before the incident?
5. What corrective programme reduces recurrence risk, and how would an auditor independently verify it worked?

## Method in brief

Evidence is graded on a four-tier hierarchy (official/primary records and court or agency releases → independent technical analyses → reputable reporting → unverified secondary claims, used only when labelled). Findings are classified **confirmed / operator-reported / cannot be established**. Risks use a defined 5×5 matrix with residual-rating discipline (no residual falls below Moderate against a state-level actor without justification). Control gaps use the four-class taxonomy: *Absent / Ineffective / Insufficiently Evidenced / Cannot Be Established*. This interim assessment deliberately carries more "Cannot Be Established" ratings than a mature one would. That is the honest signature of assessing a two-week-old incident from public evidence. Full detail: [`methodology/methodology.md`](methodology/methodology.md).

## Limitations

This is a public-evidence assessment only. There were no internal Binance documents, no interviews, and no system access. Figures are still moving. The final operator post-mortem and the outstanding proof-of-reserves verifications may change specific assessments. Nothing here asserts that any law or regulation was violated. Framework mappings are control-design benchmarks, not certification findings.

## Disclaimer

An independent portfolio assessment prepared for professional demonstration. Not affiliated with, commissioned by, or endorsed by Binance. All incident facts are drawn from the cited public sources.

## License

MIT. See `LICENSE`.
