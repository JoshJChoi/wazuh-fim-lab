# Authorized file-change test: Wazuh FIM triage

## Scope
I tested a custom Wazuh rule by changing files I created on my Mac, both inside and outside the monitored folder.

## Timeline (UTC)
- 2026-10-08 05:47:36: I recorded the time immediately before changing `critical.txt`.
- 2026-10-08 05:47:49: I recorded the time immediately before changing `ordinary.txt`.
- 2026-10-08 05:48:00: I recorded the time immediately before changing a file outside the monitored folder.
- 2026-10-08 05:48:50.857: Wazuh recorded the critical and ordinary file modification alerts.

## Evidence
- `critical.txt`: modified; custom rule 100120; level 10; scheduled scan. Its before and after SHA-256 hashes differed.
- `ordinary.txt`: modified; built-in rule 550; level 7. The custom rule did not match it.
- Outside file: no matching FIM alert for that path.
- I saved the complete critical-file alert and original screenshots privately.

## Assessment
The rule worked for this test: the critical file received a level 10 alert, and the ordinary file received the standard level 7 alert. Since I made the changes, I treated the alerts as expected test activity.

## Limitations
The agent scanned every 300 seconds, so Wazuh reported the file changes after they happened. The alerts showed which files changed, but not who changed them, which process made the changes, or why. I tested this on one Mac and a small folder of test files.
