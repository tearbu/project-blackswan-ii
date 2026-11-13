# Internal Audit and Control Testing Plan: Signing-Compromise Control Environment

**Project BLACKSWAN II, Interim GRC Assessment of the October 2026 Binance Exchange Incident**
Companion artefact to the investigation report (Sections 11 to 12). Status: **interim**. It was designed against public evidence as of 13 November 2026 and is to be re-baselined when the operator's final post-mortem is published.

---

## 1. Purpose, scope and standards basis

This plan gives Internal Audit a reperformance-based programme for the control families implicated in the 29 October 2026 incident. The intrusion began with a tampered developer-tool download (June 2026) and a fake recruitment process (September 2026), pivoted through an internal AI tool (20 October 2026), and ended with valid signatures obtained from the internal wallet-signing service (Binance, *Security Incident on October 30, 2026: Update on Preliminary Investigation Findings and Response Progress*, 3 Nov 2026, https://www.binance.com/en/square/post/384314641614134).

The programme tests **control design, operating effectiveness and evidence quality** over a defined period. It is deliberately not a penetration test (see §10 for the distinction). The October incident provides a known-bad test case: the 50 EIP-7702 authorisations submitted at 20:20 UTC on 29 Oct (TRM Labs, 30 Oct 2026, https://www.trmlabs.com/resources/blog/north-korea-steals-usd-6-36-billion-from-binance-in-the-largest-hack-in-history). That case is built into procedures as a regression scenario.

**Sampling philosophy.** Some populations are small and individually critical (signing authorisations, delegation events, revocation events, privileged tokens). Those are examined **in full**. Large homogeneous populations (engineering endpoints, install events) are sampled statistically, with zero-tolerance attributes for failures on wallet-team devices. Inquiry on its own is never sufficient evidence. Every test requires inspection, reperformance, or observation.

---

## 2. Test T-01: Endpoint tooling integrity and allow-listing

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

## 3. Test T-02: Recruitment-channel and social-engineering controls

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

## 4. Test T-03: Credential and token hygiene on endpoints

- **Audit Objective:** Determine whether long-lived credentials and deployment tokens are absent from engineering endpoints and are instead brokered, short-lived and scoped.
- **Risk Statement:** R-05 (credential/token exposure and lateral movement). The attackers found a token with container-deployment permissions stored on Engineer A's computer and used it on 29 October (Binance, 3 Nov 2026, https://www.binance.com/en/square/post/384314641614134).
- **Control Objective:** No standing high-privilege tokens reside on endpoints; deployment authority is issued just-in-time, short-lived, single-purpose, and logged.
- **Audit Procedure:**
  1. Run authorised discovery scans over the endpoint sample for token and key material (cloud keys, deployment tokens, kubeconfigs, SSH keys) and classify findings by privilege.
  2. Inspect the token-broker/vault issuance logs for the period; identify any credential with a lifetime above policy maximum or scope beyond a single deployment purpose.
  3. Trace a sample of deployments to brokered issuance records. No deployment should be traceable to a static endpoint-resident credential.
  4. Inspect exception approvals for any authorised standing credentials, including expiry and compensating monitoring.
- **Population:** Engineering endpoints (wallet/signing stratum isolated); all brokered credential issuances in the period.
- **Sampling Approach:** 100% scan of wallet/signing-team endpoints; statistical sample elsewhere; 100% of issuances with above-maximum lifetime.
- **Evidence Required:** Scan outputs with remediation tickets, issuance logs, policy on credential lifetimes, exception register.
- **Expected Result:** Zero standing deployment-capable tokens on endpoints; issuance lifetimes within policy.
- **Exception Criteria:** Any deployment-capable token found on an endpoint without a current, approved exception; any issuance above maximum lifetime unexplained.
- **Reporting:** Findings quantified by privilege level; wallet-team findings escalated immediately to the CISO, not held for the periodic report.

## 5. Test T-04: Segmentation between corporate IT and signing infrastructure

- **Audit Objective:** Determine whether network and identity segmentation prevents a compromised corporate endpoint from reaching the wallet-signing service or peer engineers' machines.
- **Risk Statement:** R-06 (segmentation failure). Per the operator's preliminary account, attackers moved from one compromised machine to two further engineers' devices via an internal AI tool on 20 Oct 2026 and from there to the signing service (Binance, 3 Nov 2026, https://www.binance.com/en/square/post/384314641614134). How that path was technically possible remains undisclosed. This test is designed to establish it independently.
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

## 6. Test T-05: Signing-policy enforcement and EIP-7702 authorisation governance

- **Audit Objective:** Determine whether the signing service signs only transactions that match an approved withdrawal or an approved policy envelope, and whether account-abstraction delegations on treasury/hot wallets are governed.
- **Risk Statement:** R-07 (signing-authority policy weakness). On Ethereum the attackers used 50 pre-signed EIP-7702 authorisations so a Binance hot wallet executed attacker code and swept itself, repeatedly, outside the withdrawal process (Chainalysis, 30 Oct 2026, updated 5 Nov, https://www.chainalysis.com/blog/binance-exploit-latest-findings-and-response/). Independent commentary identifies a signer that signs whatever an authorised caller sends, without checking against an approved withdrawal, as the core failure (Hacken, Nov 2026, https://hacken.io/insights/binance-hack/).
- **Control Objective:** Signing policy enforces destination, value and velocity limits per wallet tier and chain; unexpected EIP-7702 authorisations are rejected by default; every signing event is matched to an approved withdrawal or an approved policy envelope; delegations on treasury and hot wallets are created only under an approved change and closed inside its window; unmatched signing events alert immediately, and signing for the affected wallet tier can be suspended automatically at defined thresholds.
- **Audit Procedure:**
  1. Obtain the signing policy for every chain and wallet tier (destination allow-lists, per-transaction value caps, velocity caps, delegation rules) and reconcile it to the approved policy register; investigate any live policy element with no approval record.
  2. Inspect signing-service logs for the audit period and identify every signing event with no matching approved withdrawal or policy envelope; trace each unmatched event to its alert and its disposition.
  3. Inspect on-chain monitoring records for delegation events on treasury and hot wallets in the period; reconcile every delegation to an approved change record, and confirm that approved delegations were closed inside their change windows.
  4. Reperform in a production-equivalent environment: submit (a) a transfer to a destination outside the allow-list, (b) a transfer above the per-transaction cap, (c) a burst of transfers breaching the velocity cap, (d) an EIP-7702 authorisation request for a treasury wallet outside any change window. Record the outcome of each. Rejection is the only pass.
  5. Regression test: replay the 29 October pattern, 50 authorisations across consecutive nonces from a single caller, against the current policy configuration in the test environment; confirm the first authorisation is rejected and an alert is raised.
- **Population:** All signing events in the period; all delegation events affecting treasury and hot wallets; signing policy configurations for every chain and wallet tier.
- **Sampling Approach:** 100% of delegation events and unmatched signings, and 100% of policy configurations, as small and individually critical populations (see §1); statistical sample of matched routine signings to confirm that approval linkage also operates for ordinary traffic.
- **Evidence Required:** Approved policy register and live configuration exports, signing logs with withdrawal-approval correlation, alert and triage records, delegation change records, on-chain monitoring reports, reperformance and regression logs with timestamps.
- **Expected Result:** Zero executed signings without a matching approval; zero delegations without an approved change; every reperformance attempt rejected and alerted; the regression pattern stopped at the first authorisation.
- **Exception Criteria:** Any executed unmatched signing; any delegation lacking an approved change or left open beyond its window; any live policy element that differs from the approved register; any reperformance or regression attempt that gets signed.
- **Reporting:** Any unapproved delegation or executed unmatched signing is notified to the CISO on discovery and reported to the Audit Committee as a significant deficiency candidate, given that the 29 October loss began with exactly these event types.

## 7. Test T-06: Revocation speed and delegation-revocation drills

- **Audit Objective:** Determine whether delegated signing authority can be revoked completely and quickly in a crisis, on every chain where it can be created, using a method that was documented and approved before it is needed.
- **Risk Statement:** R-09 (slow revocation, MPC latency). The malicious delegations were created at 20:20 UTC on 29 October 2026 and re-swept newly deposited funds repeatedly, from 20:41 UTC until 23:56 UTC; they were not revoked until 00:10 UTC on 30 October, about 3 hours 50 minutes after the delegation transaction. Binance states that the delay arose from its MPC design: no single key could revoke, and a specialised revocation procedure had to be designed during the incident (Binance, 3 Nov 2026, https://www.binance.com/en/square/post/384314641614134). TRM Labs' defensive guidance from the incident is to hold a tested, pre-approved revocation-signing method in advance (TRM Labs, 30 Oct 2026, https://www.trmlabs.com/resources/blog/north-korea-steals-usd-6-36-billion-from-binance-in-the-largest-hack-in-history).
- **Control Objective:** A documented and pre-approved revocation method exists for delegations and equivalent authorisations on every supported chain; quorum members are reachable at all hours; and a full revocation, from recorded decision to confirmed on-chain revocation, completes within the adopted target time, proposed at under 30 minutes in the report's governance dashboard (Section 13).
- **Audit Procedure:**
  1. Inspect the revocation runbook for every supported chain; confirm each method is documented, version-controlled and pre-approved, names the quorum and roles it needs, and contains no step that has to be designed during execution.
  2. Inspect the on-call rota and quorum pre-staging arrangements for the period; with authorisation, test the contactability of a sample of rota members outside business hours.
  3. Observe a timed live drill: a test delegation is created on a production-equivalent wallet, the revocation decision is recorded, and the elapsed time to confirmed on-chain revocation is measured per chain. The slowest chain is the reported result.
  4. Review drill history for the period: frequency, completion, times achieved, and whether drill findings were closed.
  5. Regression review: reconstruct the 29 October revocation from retained records (delegation at 20:20 UTC, revocation at 00:10 UTC) and map each delay in that sequence to the step of the current runbook that now covers it. Any delay with no covering step is an exception.
- **Population:** Revocation methods and runbooks for every chain supporting delegated authorisations; all revocation drills performed in the period; the quorum and rota arrangements; the 29 October revocation event as the regression case.
- **Sampling Approach:** 100% throughout. The population is small and individually critical, and drill observation covers every chain on which treasury or hot wallets can be delegated, including Ethereum.
- **Evidence Required:** Runbooks with approval and version history, rota and contact-test records, drill logs carrying the decision timestamp and the on-chain confirmation timestamp, drill findings and closure evidence, retained records of the 29 October revocation.
- **Expected Result:** A current, pre-approved method exists for every in-scope chain; the observed drill completes full revocation inside the target time on every chain tested; no step depends on designing a method, or on locating one particular individual.
- **Exception Criteria:** Any in-scope chain with no documented method; any quarter with no completed drill; any drill exceeding the target time; any method containing a step to be designed during execution; any single point of human failure in the quorum.
- **Reporting:** Drill times are reported per chain, slowest chain stated, to the Audit Committee each quarter. A missing method or a missed target on any chain is reported as a significant deficiency candidate: on 29 October the revocation delay left the delegation live through repeated re-sweeps until 00:10 UTC.

## 8. Test T-07: Signing-anomaly detection, alerting and freeze coordination

- **Audit Objective:** Determine whether anomalous signing activity is detected internally and triaged in time to matter, and whether the freeze requests that containment depends on can be sent completely and quickly once a theft is confirmed.
- **Risk Statement:** R-10 (detection assurance gap), R-11 (external freeze dependency and unfreezable share). Binance's preliminary report states only that an "internal alert about large transfers" was received, with no time given. External on-chain observers described the Binance 14 sweep within about eight minutes of the 20:20 UTC delegation, and CoinDesk reported a possible multi-billion hack at 21:12 UTC, so the operator's own detection time cannot be established for the incident (Binance, 3 Nov 2026, https://www.binance.com/en/square/post/384314641614134; LunarCrush, Nov 2026, https://lunarcrush.com/research/binance-hack-reconstructed/LunarCrush-Binance-Hack-Reconstructed.pdf). On containment, issuer freezes began at 21:01 UTC (Tether) and 21:04 UTC (Circle) and ultimately blocked about $2.7B to $2.85B, of which 94 to 100% was issuer-controlled stablecoins, while about $3.0B in assets no issuer can freeze stayed beyond that route (TRM Labs, 30 Oct 2026, https://www.trmlabs.com/resources/blog/north-korea-steals-usd-6-36-billion-from-binance-in-the-largest-hack-in-history; Elliptic, updated 10 Nov 2026, https://www.elliptic.co/insights/how-north-korea-is-laundering-the-6-4-billion-stolen-from-binance/).
- **Control Objective:** Every delegation event on a treasury or hot wallet, every unmatched signing and every abnormal transfer-velocity event raises an alert that is triaged inside the adopted window, proposed at 15 minutes in the report's governance dashboard (Section 13); freeze-request packages for the major issuers are prepared and kept current in advance, and can be dispatched inside the adopted target, also proposed at 15 minutes from confirmation of theft; detection performance is measured on real and injected events, not asserted.
- **Audit Procedure:**
  1. Inspect the alert rule set over signing telemetry (unmatched signings, delegation creation, velocity anomalies) and reconcile it to the control design; confirm from change history that the rules were in force, unmodified outside approved changes, for the whole audit period.
  2. Reperformance by injection, under written authorisation in a production-equivalent environment: introduce (a) a single delegation on a treasury wallet, (b) a burst of 50 authorisations across consecutive nonces replicating the 29 October pattern, (c) an unmatched signing. Measure the time from each event to a triaged alert.
  3. Inspect alert and triage records for the period; compute detection and triage times for all real alerts; investigate any alert triaged outside the window.
  4. Inspect the freeze-coordination framework: pre-authorised request packages per major issuer, contact trees, agreed evidentiary formats. Reperform a dispatch in an exercise and measure the time from confirmation to issuer acknowledgement.
  5. Historical review: reconstruct the alert history of 29 October 2026 from retained logs and records (first internal alert, triage, escalation, freeze dispatch times). Any timestamp that cannot be evidenced is reported as a finding in its own right.
- **Population:** All signing-anomaly alerts in the period; all freeze exercises and real freeze dispatches; the retained alert and response records for 29 October 2026.
- **Sampling Approach:** 100% of alerts and dispatches, which are small and individually critical populations; injection testing covers each alert class at least once in the period.
- **Evidence Required:** Alert rule configurations with change history, SIEM alert and triage logs with timestamps, injection test records, freeze-request packages and contact trees, exercise records with issuer acknowledgements, retained incident-period logs.
- **Expected Result:** Every injected event alerts and is triaged inside the window; the 50-authorisation pattern alerts on the first authorisation, not the fiftieth; the exercise dispatch completes inside the target time; the 29 October alert history is fully reconstructable from retained records.
- **Exception Criteria:** Any injected event that raises no alert; any triage outside the window without a recorded explanation; any major issuer with no current freeze package; any incident-period timestamp that cannot be evidenced.
- **Reporting:** Detection and triage performance is reported as measured times, event by event and in distribution, not as averages alone. Any event first detected through a public or external channel is reported as a critical exception regardless of its speed, because that was the position on 29 October even though public detection was fast.

## 9. Reporting protocol and follow-up of exceptions

Results are reported test by test, then consolidated for the Audit Committee. The consolidated report separates three kinds of exception, because each has a different owner and a different remedy: design deficiencies, where the control as designed would not work; operating-effectiveness failures, where the control exists but did not operate; and evidence failures, where the control may have operated but the operator cannot show it. The October incident contains all three kinds, and a report that merged them would send remediation in the wrong direction.

Management responses and committed dates are recorded against the remediation roadmap items (REC-01 to REC-22) wherever a mapping exists, so that follow-up testing is scheduled against a commitment. Where the operator states that a capability has already been built, the exception stays open until a test in this programme, or in its re-baselined successor, passes it. The roadmap's own success criteria are written so that an announcement cannot satisfy them, but an audit test can, and this programme applies the same rule to its exceptions.

The immediate notifications required by individual tests (T-03, T-05) are made on discovery and are not held for the consolidated report.

Every exception is retested in the next cycle. An exception that repeats, or whose committed date passes without retesting, is escalated one reporting level, to the Audit Committee chair. Exceptions still open at re-baselining carry into the successor programme with their history intact (see §11).

## 10. Scope boundary: this programme is not a penetration test

Section 1 states that this programme is deliberately not a penetration test. The distinction is worth setting out in full, because three different instruments answer three different questions, and this incident shows the cost of confusing them.

A penetration test asks whether a skilled adversary can defeat the environment today, along paths the tester chooses. It is point-in-time and path-dependent. The failures that enabled this incident are mostly not of that kind. A token resident on a laptop, and a remediation that stopped at the device, are governance facts, and penetration tests routinely walk past them.

The social-engineering simulation in T-02 tests human and procedural controls: verification callbacks, reporting behaviour and sandbox discipline. It produces no evidence about whether endpoint tooling would detect the payload if someone did run it. The endpoint testing in T-01 and T-03 tests technical controls, and produces no evidence about whether staff would report the approach in the first place. In September 2026, one control from each family failed in sequence, which is why a credible opinion needs all three instruments.

Control testing asks a different question from either: did the control operate, for the whole period, for the whole population, and can the operator show it. Results from the three instruments are reported side by side, with no netting off between them. A high simulation report rate does not compensate for an endpoint detection gap, and a complete allow-list does not compensate for staff who can be talked around it.

## 11. Independence, competence and re-baselining

The programme is executed by Internal Audit. No test in it is performed by, or reported through, the owner of the control under test. Observation of the T-06 drill, and the injection steps in T-07, are carried out under written authorisation agreed in advance, with the test wallets, value limits and stop conditions recorded before execution begins.

Two competences are required that a generalist audit team does not always hold. The first is reading public-chain records directly: delegation, sweep and revocation events are on-chain facts, and several procedures in this plan reconcile internal logs to them. The second is interrogating signing-service telemetry at log level. Where either competence is not available inside the team, it is co-sourced, and the report states the reliance placed on it and the limits of that reliance.

This plan is interim, as its header states. It is re-baselined when the operator's final post-mortem is published, when an independent review of the incident reports, or when the signing architecture changes materially, whichever happens first. On re-baselining, the facts used as regression cases in T-05, T-06 and T-07 are reset to the record as finally established, populations are redrawn, and open exceptions carry over under §9. Until then, conclusions drawn from this programme carry the same interim status as the assessment it belongs to.
