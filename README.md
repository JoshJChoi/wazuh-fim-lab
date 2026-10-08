# Wazuh File Integrity Monitoring Lab

## Summary

I built a local Wazuh lab to practice detecting and investigating file changes. An Ubuntu ARM64 virtual machine runs Wazuh, and a Wazuh agent on my Mac monitors a folder of synthetic test files. I wrote a custom rule for one critical file, tested it against two controls, and documented the result.

All file changes were authorized lab activity, not a real intrusion.

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

My rule is in [`rules/lab_fim.xml`](rules/lab_fim.xml). It builds on Wazuh's file modification rule `550` and matches only a file named `critical.txt` inside the synthetic `cyber-lab` folder. When it matches, the alert uses custom rule `100120` at level 10.

The path expression is anchored so that a similarly named file elsewhere should not match this rule.

## Validation

| Test | What I changed | Observed result |
|---|---|---|
| Critical file | Appended text to `critical.txt` | Custom rule `100120`, level 10, at 2026-10-08 05:48:50 UTC |
| Ordinary file | Appended text to `ordinary.txt` | Standard rule `550`, level 7, at 2026-10-08 05:48:50 UTC |
| Outside file | Appended text to a file outside `cyber-lab` | No matching FIM alert for that path in the alerts I checked |

I verified the critical alert in Wazuh's JSON alerts. It identified the event as a scheduled file modification, and the before and after SHA-256 hashes were different.

## Analyst Assessment

The critical-file alert was expected: I made the change as part of this test. I classified it as benign, authorized activity. The ordinary-file control showed that my custom rule did not promote every modification in the monitored folder.

My investigation and timeline are in [`cases/incident-report.md`](cases/incident-report.md).

## Limitations

- This test used one Mac endpoint and a small folder of synthetic files.
- Scheduled scanning means an alert can arrive after the file change.
- A file modification alert alone does not identify the person or process responsible or establish malicious intent.
- The outside-file check means I found no alert for that path during this test; it does not prove that every possible change outside the folder would go unobserved.

## Next Improvements

I would repeat the test several times to measure detection delay, then compare FIM alerts with additional endpoint logs and an approval record for each change.

## Evidence

- [Redacted FIM event comparison](evidence/screenshots/fim-event-comparison-redacted.png): critical file rule 100120 versus ordinary file rule 550.
- [Redacted active agent](evidence/screenshots/active-agent-redacted.png): macOS agent connected to Wazuh.
- [Sanitized critical-file alert](evidence/public/alert-public-summary.json): selected alert fields and before/after hashes.
- [Incident report](cases/incident-report.md): test timeline, assessment, and limitations.
