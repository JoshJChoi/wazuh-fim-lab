# Authorized file-change test: Wazuh FIM triage

## Scope
I tested a custom Wazuh rule against synthetic files on my own Mac. This was an authorized lab change, not a real intrusion.

## Timeline (UTC)
- 2026-10-08 05:47:36: I recorded the time immediately before changing `critical.txt`.
- 2026-10-08 05:47:49: I recorded the time immediately before changing `ordinary.txt`.
- 2026-10-08 05:48:00: I recorded the time immediately before changing a file outside the monitored folder.
- 2026-10-08 05:48:50.857: Wazuh recorded the critical and ordinary file modification alerts.

## Evidence
- `critical.txt`: modified; custom rule 100120; level 10; scheduled scan. Its before and after SHA-256 hashes differed.
- `ordinary.txt`: modified; built-in rule 550; level 7. The custom rule did not match it.
- Outside file: no matching FIM alert for that path.
- I retained the complete critical-file alert and original screenshots privately.

## Assessment
The custom rule detected the intended critical-file change while leaving the ordinary-file alert at its generic level. I performed the changes myself, so I classify this as benign, authorized test activity and a successful detection validation.

## Limitations
The scheduled scan detected the change after it happened. This alert alone does not establish who made the change, whether it was malicious, or what process performed it. This test covers one Mac endpoint and one small synthetic folder.
