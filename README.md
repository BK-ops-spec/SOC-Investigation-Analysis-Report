# SOC Investigation & Analysis Report — The Golden Invoice

A completed Tier 1 SOC investigation into a spearphishing email that impersonated an invoicing provider and targeted the Accounts Payable team at a UK NHS foundation trust.

**Incident:** `INC-20260825-0143`  
**Case name:** The Golden Invoice  
**Verdict:** Confirmed Phishing — Credential Harvesting (`T1566.002`)  
**Analyst:** Baris Koca

## Purpose

This repository documents the investigation, analysis and response to a realistic phishing incident. The purpose of the exercise is to demonstrate a complete, defensible SOC workflow: preserving evidence, analysing email headers, validating sender authentication, extracting indicators of compromise (IOCs), enriching them with threat intelligence, mapping activity to MITRE ATT&CK, and recommending containment actions.

The scenario is a training case based on the Brackenmoor NHS Foundation Trust "Golden Invoice" case study. The organisation, users and some telemetry are fictional, and the IP addresses use the RFC 5737 documentation ranges.

## Scenario

On Monday 25 August 2026 at 09:16:42 +0100, an Accounts Payable coordinator reported a suspicious invoice email claiming to be from "PayHub Invoicing". The message said invoice **INV-33421** was overdue and urged the recipient to review it within 24 hours via a "View Invoice" link. The task was to determine whether the message was genuinely from the invoicing provider or an impersonation attempt.

## Task Completed

The investigation answered the full set of analyst requirements in the task brief:

| Requirement | Outcome |
|---|---|
| Originating IP and mail path | Traced the Received chain in order: `203.0.113.77` → `198.51.100.42` → internal `10.24.8.15` |
| SPF, DKIM and DMARC | Recorded **SPF fail**, **DKIM none** and **DMARC permerror** and explained each result |
| Received header analysis | Identified three mail-handling hops and the anomalies supporting impersonation |
| Sender identity | Compared From, Return-Path, Message-ID and Reply-To and found no alignment with the claimed brand |
| Gateway authentication headers | Interpreted `X-Trust-Mail-Gateway-Auth: unauthenticated-external` |
| Invoice legitimacy | Reached a **Confirmed Phishing** verdict with supporting evidence |
| Reply-To investigation | Identified the free Gmail Reply-To and the homoglyph `supp0rt` (zero replacing "o") as a red flag |
| IOC extraction | Produced a defanged IOC set covering domains, IPs, email addresses and URLs |
| MITRE ATT&CK mapping | Mapped initial access to `T1566.002` (Spearphishing Link) |
| Final verdict and escalation | Recommended containment, blocking and validation actions |

## Key Findings

- **Authentication failed at every layer:** SPF fail, DKIM none and DMARC permerror.
- **Sender/reply-to mismatch:** the From domain was `payhub-notify-secure.com`, but replies were redirected to `payhub.billingsupp0rt@gmail.com`.
- **Homoglyph deception:** the "0" in `supp0rt` replaces the letter "o".
- **Malicious link:** the "View Invoice" button pointed to a credential-harvesting URL, `hxxps://invoice-review[.]link/inv/33421-settle`.
- **Suspicious infrastructure:** the originating address sat under an APNIC-203 Asia-Pacific parent allocation, a geographic anomaly for a UK-only NHS trust.
- **VirusTotal enrichment:** both IPs returned 0/92 malicious verdicts and had no case-domain link; the relations were assessed as laboratory/sandbox contamination and were not blocklisted.
- **Tracking and social engineering:** a hidden 1x1 tracking pixel, urgency, a 24-hour deadline and a service-interruption threat were documented.
- **Containment:** both public IPs were blocked at the email gateway and network edge, and all case IOCs were added to the gateway blocklist.
- **Correlated activity:** SOC Command Center records showed DNS, VPN, PowerShell and EDR events that conflict with the initial "no interaction" summary and require production validation before final closure.

## Indicators of Compromise (Defanged)

| Type | Indicator |
|---|---|
| Sending domain | `payhub-notify-secure.com` |
| Originating IP | `203.0.113.77` |
| Relay IP | `198.51.100.42` |
| From address | `no-reply@payhub-notify-secure.com` |
| Reply-To address | `payhub.billingsupp0rt@gmail.com` |
| Phishing domain | `invoice-review[.]link` |
| Phishing URL | `hxxps://invoice-review[.]link/inv/33421-settle` |

## Repository Contents

| File | Description |
|---|---|
| [Golden Invoice Incident SOC Investigation.pptx](<Golden Invoice Incident SOC Investigation.pptx>) | The completed investigation presentation. It covers triage, evidence collection, header forensics, URL and content analysis, IOC compilation, containment, reporting and closure, plus the appendix with the full investigation report, VirusTotal enrichment and SOC Command Center correlation. |
| [TASK-Brackenmoor_Golden_Invoice_Email_Header_Analysis_Task.pdf](<TASK-Brackenmoor_Golden_Invoice_Email_Header_Analysis_Task.pdf>) | The original task brief describing the scenario, the evidence, the ten analyst questions, the expected outputs and the analyst safety rules. |

## Skills Demonstrated

- Email header forensics and Received-chain reconstruction
- SPF, DKIM and DMARC analysis
- Sender identity and Reply-To mismatch investigation
- Phishing content and URL analysis without clicking live links
- IOC extraction and defanging
- VirusTotal and WHOIS/APNIC enrichment
- MITRE ATT&CK mapping
- Incident triage, evidence preservation and chain of custody
- Containment, escalation and SOC reporting
- Technical writing and executive reporting

## Important Notes

- This is a **training and educational exercise**. Brackenmoor NHS Foundation Trust, the users and parts of the telemetry are fictional.
- `203.0.113.77` and `198.51.100.42` are RFC 5737 documentation-range addresses. They are not real attacker infrastructure.
- No real patient data, credentials or production systems are included.
- The material is classified **TLP:AMBER** in the original report and should be shared only within the intended audience.

## Author

**Baris Koca** — [GitHub](https://github.com/BK-ops-spec) · [Portfolio](https://bk-ops-spec.github.io/profile/)
