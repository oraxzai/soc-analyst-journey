# 📚 Learning Notes

Quick reference notes as I study.

## Alert Triage Workflow
1. **Understand the alert** — what triggered it?
2. **Validate** — is it real or a false positive?
3. **Enrich** — check IP/domain/hash reputation
4. **Correlate** — check related logs
5. **Decide** — TP/FP, escalate or close
6. **Document** — reasoning matters more than verdict

## Key Questions Per Alert
- What is the source? Internal or external?
- What process/user is involved?
- Is this expected behavior?
- What does the ATT&CK mapping suggest?
- Would I escalate this to L2?

## MITRE ATT&CK Quick Map
| Activity | Tactic | Common Technique |
|----------|--------|------------------|
| Phishing | Initial Access | T1566 |
| PowerShell abuse | Execution | T1059.001 |
| Credential dumping | Credential Access | T1003 |
| Lateral movement | Lateral Movement | T1021 |

*(Adding notes daily)*
