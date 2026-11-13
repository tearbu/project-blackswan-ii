# Internal Audit and Control Testing Plan — Signing-Compromise Control Environment

**Project BLACKSWAN II — Interim GRC Assessment of the October 2026 Binance Exchange Incident**
Companion artefact to the investigation report (Sections 11–12). Status: **interim** — designed against public evidence as of 13 November 2026 and to be re-baselined when the operator's final post-mortem is published.

---

## 1. Purpose, scope and standards basis

This plan gives Internal Audit a reperformance-based programme for the control families implicated in the 29 October 2026 incident: an intrusion that began with a tampered developer-tool download (June 2026) and a fake recruitment process (September 2026), pivoted through an internal AI tool (20 October 2026), and ended with valid signatures obtained from the internal wallet-signing service (Binance, *Security Incident on October 30, 2026: Update on Preliminary Investigation Findings and Response Progress*, 3 Nov 2026, https://www.binance.com/en/square/post/384314641614134).

The programme tests **control design, operating effectiveness and evidence quality** over a defined period. It is deliberately not a penetration test (see §10 for the distinction). Where the October incident provides a known-bad test case — the 50 EIP-7702 authorisations submitted at 20:20 UTC on 29 Oct (TRM Labs, 30 Oct 2026, https://www.trmlabs.com/resources/blog/north-korea-steals-usd-6-36-billion-from-binance-in-the-largest-hack-in-history) — that case is built into procedures as a regression scenario.

**Sampling philosophy.** Populations that are small and individually critical (signing authorisations, delegation events, revocation events, privileged tokens) are examined **in full**. Large homogeneous populations (engineering endpoints, install events) are sampled statistically with zero-tolerance attributes for failures on wallet-team devices. Inquiry is never sufficient evidence on its own: every test requires inspection, reperformance, or observation.

---

## 2. Test T-01 — Endpoint tooling integrity and allow-listing

- **Audit Objective:** Determine whether software installation on engineering endpoints is restricted to verified, allow-listed packages, so that a tampered installer or trojanised tool component cannot execute.
- **Risk Statement:** R-02 (malicious dev tooling / supply-chain downloads), R-03 (endpoint detection gap). In June 2026 a tampered Homebrew installer compromised a wallet engineer's machine; in September 2026 malicious code replaced a component of a legitimate tool (pyenv) on a second engineer's machine and evaded endpoint tooling (Binance, 3 Nov 2026, https://www.binance.com/en/square/post/384314641614134).
- **Control Objective:** All executable code on engineering endpoints originates from an allow-listed catalogue with signature/hash verification; deviations are blocked, or detected and escalated within a defined window.
- **Audit Procedure:**
  1. Obtain the endpoint software inventory for the audit period and reconcile it to the allow-list; investigate every non-catalogue item.
  2. Inspect package-manager and installer configuration (Homebrew and equivalents) for source pinning and signature/hash verification settings.
  3. Reperform: on a test endpoint in the production configuration, attempt installation of (a) an unsigned package, (b) a package with a mismatched hash, (c) a modified component of an allow-listed tool; record block/detect outcome and time.
  4. Inspect EDR coverage reports; reconcile enrolled endpoints to the HR/asset register of engineering staff with production-adjacent roles.
  5. Review EDR alert dispositions for the sample period, tracing a sample of closed alerts to analyst notes.
- **Population:** All engineering endpoints, with the wallet/signing engineering population isolated as a stratum.
- **Sampling Approach:** 100% of wallet/signing-team endpoints; statistical sample of the wider engineering estate sized for zero expected deviations on the allow-list attribute.
- **Evidence Required:** Inventory exports, allow-list change history, EDR console coverage and alert records, reperformance logs with timestamps, exception tickets.
- **Expected Result:** Non-catalogue installs blocked or alerted; EDR coverage 100% for the wallet-team stratum; no unexplained tool-component modifications.
- **Exception Criteria:** Any successful unmanaged install on a wallet-team endpoint; any endpoint without active EDR; any tool component whose hash differs from the vendor release without an approved change record.
- **Reporting:** Exceptions rated individually; any wallet-team exception reported to the Audit Committee as a significant deficiency candidate, given the June/September 2026 precedent.

## 3. Test T-02 — Recruitment-channel and social-engineering controls

- **Audit Objective:** Determine whether staff in targeted roles can verify unsolicited recruitment approaches and are required to run externally supplied code only in isolated environments.
- **Risk Statement:** R-01 (fake-recruitment social engineering). The September 2026 compromise began with a fictitious company's "HR representative" and "CEO" persuading an engineer to download a coding "assignment" onto a work computer (Binance, 3 Nov 2026, https://www.binance.com/en/square/post/384314641614134). Chainalysis documents the same playbook rehearsed across dozens of smaller attacks over ~18 months (Chainalysis, 3 Nov 2026, https://www.chainalysis.com/blog/category/crime/).
- **Control Objective:** A defined verification-callback procedure exists for recruitment approaches to staff in sensitive roles; externally supplied code is executed only in a managed sandbox; approaches are reported and logged.
- **Audit Procedure:**
  1. Inspect the policy and the role register identifying positions subject to enhanced recruitment-approach controls; test completeness of the register against org charts for wallet, signing and infrastructure teams.
  2. Reperform the verification-callback procedure on a sample of logged approaches: confirm the callback used an independently sourced contact channel, and that the approach was logged before any material was downloaded.
  3. Review completion and currency of role-specific training for the in-scope population.
  4. With management authorisation, run a controlled simulated approach against a sample of in-scope staff and measure: download behaviour (sandbox vs endpoint), report rate, and time-to-report.
  5. Inspect the sandbox environment configuration to confirm external code cannot reach production credentials or networks from it.
- **Population:** All staff in roles with production, signing, deployment or privileged-infrastructure access; all logged recruitment approaches in the period.
- **Sampling Approach:** 100% of logged approaches for the wallet/signing population; simulation sample of at least 30 in-scope staff or 25% of the population, whichever is larger.
- **Evidence Required:** Policy, role register, approach log, callback records, training records, simulation results, sandbox configuration.
- **Expected Result:** Verification callbacks completed before engagement in 100% of logged cases; simulation report rate ≥90% with no endpoint downloads outside the sandbox.
- **Exception Criteria:** Any in-scope role missing from the register; any logged approach with no callback record; any simulation download executed on a corporate endpoint.
- **Reporting:** Simulation results reported in aggregate only (no individual naming), with control-level conclusions to the Audit Committee.

## 4. Test T-03 — Credential and token hygiene on endpoints

- **Audit Objective:** Determine whether long-lived credentials and deployment tokens are absent from engineering endpoints and are instead brokered, short-lived and scoped.
- **Risk Statement:** R-05 (credential/token exposure and lateral movement). The attackers found a token with container-deployment permissions stored on Engineer A's computer and used it on 29 October (Binance, 3 Nov 2026, https://www.binance.com/en/square/post/384314641614134).
- **Control Objective:** No standing high-privilege tokens reside on endpoints; deployment authority is issued just-in-time, short-lived, single-purpose, and logged.
- **Audit Procedure:**
  1. Run authorised discovery scans over the endpoint sample for token and key material (cloud keys, deployment tokens, kubeconfigs, SSH keys) and classify findings by privilege.
  2. Inspect the token-broker/vault issuance logs for the period; identify any credential with a lifetime above policy maximum or scope beyond a single deployment purpose.
  3. Trace a sample of deployments to brokered issuance records — no deployment should be traceable to a static endpoint-resident credential.
  4. Inspect exception approvals for any authorised standing credentials, including expiry and compensating monitoring.
- **Population:** Engineering endpoints (wallet/signing stratum isolated); all brokered credential issuances in the period.
- **Sampling Approach:** 100% scan of wallet/signing-team endpoints; statistical sample elsewhere; 100% of issuances with above-maximum lifetime.
- **Evidence Required:** Scan outputs with remediation tickets, issuance logs, policy on credential lifetimes, exception register.
- **Expected Result:** Zero standing deployment-capable tokens on endpoints; issuance lifetimes within policy.
- **Exception Criteria:** Any deployment-capable token found on an endpoint without a current, approved exception; any issuance above maximum lifetime unexplained.
- **Reporting:** Findings quantified by privilege level; wallet-team findings escalated immediately to the CISO, not held for the periodic report.

## 5. Test T-04 — Segmentation between corporate IT and signing infrastructure

- **Audit Objective:** Determine whether network and identity segmentation prevents a compromised corporate endpoint from reaching the wallet-signing service or peer engineers' machines.
- **Risk Statement:** R-06 (segmentation failure). Per the operator's preliminary account, attackers moved from one compromised machine to two further engineers' devices via an internal AI tool on 20 Oct 2026 and from there to the signing service (Binance, 3 Nov 2026, https://www.binance.com/en/square/post/384314641614134). How that path was technically possible remains undisclosed — this test is designed to establish it independently.
- **Control Objective:** Deny-by-default segmentation: signing infrastructure accepts connections only from hardened, individually approved access paths; lateral tooling (including internal AI tools) holds no standing ability to act on other endpoints.
- **Audit Procedure:**
  1. Obtain the network/identity architecture for the signing environment and the approved access-path register; reconcile actual firewall/ACL and service-mesh rules to it.
  2. Identify every identity and tool with cross-endpoint action capability (including internal AI tools and agents); inspect their permission scopes, approval workflows and action logs.
  3. Reperform: from a standard corporate endpoint profile, attempt to reach signing-service interfaces and peer-endpoint management planes; all attempts must fail and alert.
  4. Review rule-change history for the period for emergency or temporary openings and their closure.
- **Population:** All network paths and tool identities capable of reaching the signing environment.
- **Sampling Approach:** 100% of rules and tool identities in scope (population is small and critical).
- **Evidence Required:** Architecture diagrams with data-flow detail, rule exports, tool permission matrices and action logs, reperformance records, change tickets.
- **Expected Result:** No path from a standard endpoint profile to the signing service; cross-endpoint tools act only with per-action approval and full logging.
- **Exception Criteria:** Any unregistered path or tool capability; any standing cross-endpoint permission without approval workflow; any unclosed temporary rule.
- **Reporting:** A path diagram of actual (not documented) reachability is included in the audit report; discrepancies between documented and actual topology are reported as a design deficiency.

## 6. Test T-05 — Signing-policy enforcement and EIP-7702 authorisation governance

- **Audit Objective:** Determine whether the signing service signs only transactions that match an approved withdrawal or an approved policy envelope, and whether account-abstraction delegations on treasury/hot wallets are governed.
- **Risk Statement:** R-07 (signing-authority policy weakness). On Ethereum the attackers used 50 pre-signed EIP-7702 authorisations so a Binance hot wallet executed attacker code and swept itself, repeatedly, outside the withdrawal process (Chainalysis, 30 Oct 2026, updated 5 Nov, https://www.chainalysis.com/blog/binance-exploit-latest-findings-and-response/). Independent commentary identifies a signer that signs whatever an authorised caller sends, without checking against an approved withdrawal, as the core failure (Hacken, Nov 2026, https://hacken.io/insights/binance-hack/).
- **Control Objective:** Signing policy enforces destination, value and velocity limits per wallet tier and chain; unexpected EIP-770
...[truncated 9113 chars]