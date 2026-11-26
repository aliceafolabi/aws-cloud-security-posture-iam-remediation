# ADR-001: Everyday identity for the single-account lab

Date: 25 Nov 2026
Status: Accepted

## Context
The AWS account was being used only through the root user. Root cannot be limited by IAM policies, so a compromise would have given full control of the account, its billing and its closure. The lab needed a safer everyday identity.

## Requirements
- A non-root identity for daily work
- MFA required at sign-in
- No long-lived access keys
- Permissions limited to what the work needs
- Low cost and low complexity in a single-account lab

## Options considered
| Option | Summary |
|---|---|
| A. Keep using root | Rejected. Violates least privilege and carries the largest blast radius. |
| B. IAM user with MFA, no access keys, permissions through a group | Selected. |
| C. IAM Identity Center | Evaluated and not enabled in this account (reason below). |

## Why IAM Identity Center was not enabled
The console setup flow offered "Enable IAM Identity Center with AWS Organizations". Based on what the screen stated:
- It would create an AWS Organization and make this account its management account. AWS recommends not keeping resources in a management account, which conflicts with running the lab here.
- The default setup was a multi-Region instance using a customer managed KMS key and a replica Region. The cost of this was not verified.
- Some settings, including the primary Region, were stated as not changeable later.
- Multi-account AWS Organizations was out of scope for this project.

An account instance was also offered. It was not evaluated in depth, so no claim is made about its suitability.

## Decision
Use an IAM user with MFA, no access keys and read-only permissions through a group carrying the AWS managed ReadOnlyAccess policy.

## Evidence
- Sign-in forced a password change and required an MFA code (observed at first sign-in, not screenshotted for security reason).
- The console showed MFA enabled and no access key (`iam-alicedaily-mfa-assigned-after.png`).
- Listing S3 buckets was allowed (`alicedaily-allowed-s3-list.png`). Creating a bucket was denied (`alicedaily-denied-create-bucket.png`).
- After leaving the setup screen, the Identity Center page still showed the Enable button (`identity-center-not-enabled-after.png`).

## Trade-offs and limitations
- An IAM user has a permanent password. Identity Center issues short-lived credentials which is a stronger model.
- The group name does not describe its permissions. This is a governance weakness, so the group's actual policy is documented here.
- ReadOnlyAccess can still read stored data. This is acceptable only because the lab holds no real data.
- Billing alert delivery was not tested.

## Revisit when
The environment becomes multi-account or a project needs workforce single sign-on.