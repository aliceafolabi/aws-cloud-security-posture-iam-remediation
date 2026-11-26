# Threat model

Status key: **Observed** = seen in the account. **Tested** = a check was run and the result recorded. **Configured** = set up but not proven to work. **Planned** = not done yet.

## What is being protected
| Asset | Why it matters |
|---|---|
| The AWS account and its root login | Full control of the account, its billing and its closure |
| IAM identities and their permissions | They decide who can do what |
| Data in S3 | Exposure would be a data leak |
| Audit logs | They are the only way to answer "who did what, and when" |
| Billing | Mistakes or misuse can create costs |
| This public repository | Leaked identifiers or false claims would damage trust in the project |

## Boundaries and actors
- **Public internet** to **this GitHub repository**: anything committed is public and permanent in history.
- **My workstation** to **the AWS account**: console sign-in and, later, command-line access.
- **Inside the AWS account**: the root user, the read-only user `Alicedaily`, and a build identity that is not yet designed.

Actors considered: an outside attacker with a stolen credential, a phishing attacker targeting the root or email account, my own mistakes, a tool doing more than intended (Terraform), and an accidental leak through GitHub.

## Threats, from the business problem
| ID | Problem | Threat | Evidence so far | Control | Status |
|---|---|---|---|---|---|
| T1 | Excessive permissions | The root login is compromised and the whole account is lost | Root was in daily use (CloudTrail event history). Root MFA enabled and no active root access keys (IAM dashboard) | A non-root identity for daily work with MFA and no access keys | Tested (allowed and denied actions). Root is still used for IAM changes |
| T2 | Excessive permissions | A build identity or lab policy is far broader than needed | Not yet built | A scoped build role assumed with MFA, with allowed and denied tests | Planned |
| T3 | Exposed storage | A bucket is made public by mistake | Account-level Block Public Access was off on 12 Nov. No buckets were listed on 24 Nov | Block Public Access, encryption, tested bucket policy | Planned |
| T4 | Weak network boundaries | A security group exposes an administrative port to the internet | Only the default VPC was seen. Security groups were not reviewed | Reviewed and tightened security groups in the lab | Planned |
| T5 | Insufficient audit logging | An incident cannot be investigated | No CloudTrail trail was listed in the console on 26 Nov 2026 (region viewed: N. Virginia). Event history is available but covers 90 days of management events only | A CloudTrail trail with log delivery checked by finding a real event | Planned |
| T6 | Leak through GitHub | A secret or account identifier is published in this repository | None found. The first commit, made by GitHub, carries a personal email | `.gitignore` protections and manual screenshot checks | `*.pem` rule tested. Other patterns configured only. Screenshots checked by hand |
| T7 | Cost | Unexpected spend from forgotten resources | A $5 monthly budget exists with one verified recipient | Budget alerts and a cleanup procedure | Budget configured, delivery not tested. Cleanup planned |
| T8 | My own mistakes | A wrong command changes or deletes something | One mistyped command created a stray file, caught by `git status` before commit | Read each plan before applying, `git status` before each commit | Practised, not formally tested |

## Assumptions and limits
- This is a single-account lab with no real customer data.
- Detection of attacks in progress is not part of this project.
- The threat list is based on the storyline and on what was observed, so it is not exhaustive.
- Nothing here is a claim of compliance with any standard.

## What happens next
The architecture diagram will show where each control sits. Findings from Prowler and Checkov will be added to the finding register and mapped back to T1 to T8.