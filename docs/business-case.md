# Business case

## The problem
A company has excessive permissions, exposed storage, weak network boundaries or insufficient audit logging. These are common causes of cloud security incidents and they tend to build up quietly as a cloud account grows.

Problems amd  Why it matters : 

- Excessive permissions :  A single compromised identity can reach far more than it should. 
- Exposed storage : Data can become readable by people who should never see it. 
- Weak network boundaries :  Systems are reachable from places they don't need to be. - - - Insufficient audit logging :  When something goes wrong, nobody can say who did what, or when. 

## What this project does
It plays the role of a security engineer assessing a small AWS environment:
- A small AWS environment built with Terraform, with a defined network and identity model.
- Least-privilege IAM roles and policies.
- Private storage and encryption controls.
- CloudTrail logging.
- Automated checks with Checkov and a suitable AWS assessment tool (Prowler).
- A workflow that identifies, fixes and rechecks findings.

## Evidence this project produces
- Before-and-after security findings.
- IAM access decisions explained with policy evidence.
- A finding to remediation report.
- A threat model and architecture diagram.
- A documented cost estimate and cleanup procedure.

## Approach to cost
Static analysis and simulated findings come first. Paid resources are deployed only when needed under a budget and with a cleanup plan.

## Status
The starting-state review and the non-root identity work are done. See the README for current progress.