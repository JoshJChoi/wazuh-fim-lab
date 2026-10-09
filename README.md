# Wazuh File Integrity Monitoring Lab

## Summary

I built a local Wazuh lab to practice detecting and investigating file changes. In the lab, an Ubuntu ARM64 virtual machine was used to run Wazuh, and a Wazuh agent on my Mac monitored a folder of test files I created. I wrote a custom rule for one critical file, ran three file-change tests, and documented the results.

## Environment

| Component | Setup |
|---|---|
| Host | Apple silicon Mac |
| Virtual machine | Ubuntu Server 24.04 ARM64 in UTM |
| Wazuh | Version 4.14.8, all-in-one deployment |
| Endpoint | macOS Wazuh agent |
| Monitoring | Scheduled file integrity scans every 300 seconds |
| Test folder | `~/cyber-lab/` |

The VM used UTM Shared Network, and I accessed the dashboard from my Mac.

## Detection Rule

My rule is in [`rules/lab_fim.xml`](rules/lab_fim.xml). It builds on Wazuh's file modification rule `550` and matches only a file named `critical.txt` in the `cyber-lab` test folder. When it matches, the alert uses custom rule `100120` at level 10.

## Validation

| Test | What I changed | Observed result |
|---|---|---|
| Critical file | Appended text to `critical.txt` | Custom rule `100120`, level 10, at 2026-10-08 05:48:50 UTC |
| Ordinary file | Appended text to `ordinary.txt` | Standard rule `550`, level 7, at 2026-10-08 05:48:50 UTC |
| Outside file | Appended text to a file outside `cyber-lab` | No matching FIM alert for that path in the alerts I checked |

I verified the critical alert in Wazuh's JSON alerts. It identified the event as a scheduled file modification, and the before and after SHA-256 hashes were different.

## Analyst Assessment

I made the changes myself, so the critical-file alert was expected. `critical.txt` triggered my level 10 rule, while `ordinary.txt` stayed at the standard level 7 alert.

My investigation and timeline are in [`cases/incident-report.md`](cases/incident-report.md).

## Limitations

- I tested one Mac and a small folder of test files.
- The agent scanned every 300 seconds, so it reported the change after it happened.
- The alert showed which file changed, but not who changed it, which process did it, or why.
- I found no alert for the one outside file I tested and I did not test every location on the Mac.

## Evidence

- [Redacted FIM event comparison](evidence/screenshots/fim-event-comparison-redacted.png): critical file rule 100120 versus ordinary file rule 550.
- [Redacted active agent](evidence/screenshots/active-agent-redacted.png): macOS agent connected to Wazuh.
- [Sanitized critical-file alert](evidence/public/alert-public-summary.json): selected alert fields and before/after hashes.
- [Incident report](cases/incident-report.md): test timeline, assessment, and limitations.
