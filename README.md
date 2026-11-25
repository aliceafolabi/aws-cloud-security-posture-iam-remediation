# AWS Cloud Security Posture and IAM Remediation Lab

> **Status: in progress.** This repository is being built phase by phase. It is a learning lab, not a production deployment. Items below are marked as tested, configured, or planned, and nothing is claimed beyond that.

## Summary

A hands-on project to identify, prioritise, remediate and validate cloud security weaknesses in a single AWS account, with before-and-after evidence for every fix.

## Business problem

Many organisations accumulate cloud risk without noticing: over-permissioned identities, storage without account-level safeguards, weak network boundaries, and incomplete audit logging. When something goes wrong, nobody can easily say who had access or what changed. This project works through those problems as a security engineer would, from assessment to verified fix.

## Objectives

1. Build a reproducible lab environment with Terraform.
2. Run a baseline posture assessment and record every finding.
3. Prioritise findings by severity and business impact.
4. Replace over-permissive IAM policies with least-privilege policies and review trust policies.
5. Demonstrate temporary credentials through STS.
6. Secure S3 storage and verify CloudTrail logging.
7. Test both allowed and denied actions for each fix.
8. Re-run the assessment and compare before and after.
9. Destroy the lab and confirm no billable resources remain.

## Scope

**In scope:** a single AWS account, IAM, S3, basic VPC networking, CloudTrail, Terraform, and posture scanning with Prowler and Checkov.

**Out of scope:** production or third-party environments, threat detection and incident response, CI/CD security gates, container security, multi-account AWS Organizations, and any claim of regulatory compliance.

## Progress

| Area | Status |
|---|---|
| Starting-state review of the account | Done (findings below) |
| Non-root IAM user with MFA, no access keys, read-only permissions | Tested: listing S3 buckets was allowed, creating a bucket was denied |
| Billing budget alerts | Configured, recipient verified. Alert delivery is not tested |
| Repository protections (.gitignore) | Tested for the `*.pem` pattern only |
| Architecture and threat model | Planned |
| Terraform lab environment | Planned |
| Prowler and Checkov assessments | Planned |
| Remediation, validation and re-assessment | Planned |
| Final report | Planned |

## Starting-state findings

These were observed in the account before any changes:

- All four account-level S3 Block Public Access settings were off.
- Root was the only identity in daily use. CloudTrail event history showed activity by the root user.
- An existing budget had two alert recipients, both unverified. One address contained a domain typo.

## Decisions

- An IAM Identity Center setup was reviewed and not enabled in this account, because the setup flow would have created an AWS Organization with this account as its management account. See the decision record (to be added to the docs folder).

## Limitations so far

- Evidence screenshots are being prepared and will be added after being checked for account identifiers.
- The single non-root user is read-only. A scoped identity for building the lab has not been designed yet.
- Nothing in this repository should be treated as production-ready.

## Privacy

Account IDs, email addresses, credentials and tokens are never committed. Screenshots are redacted before being added.
