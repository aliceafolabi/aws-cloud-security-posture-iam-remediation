# Evidence index

Screenshots are redacted before being added: account IDs, email addresses, credentials and tokens are covered or cropped. Each entry states what the screenshot shows and what it does not show.

## Phase 2: Starting state of the account (captured 12 Nov 2026)

| File | What it shows | What it does not show |
|---|---|---|
| `screenshots/phase2/s3-account-block-public-access.png` | All four account-level S3 Block Public Access settings were off at the start of the project. | Whether any bucket was exposed. No buckets were examined. |
| `screenshots/phase2/vpc-default-vpc-overview.png` | One VPC in N. Virginia, marked as the default VPC, with the IPv4 range 172.31.0.0/16. | Subnets, route tables and security groups. They were not reviewed. |
| `screenshots/phase2/cloudtrail-event-history-root-activity.png` | CloudTrail event history rows attributed to the root user, confirming root was in everyday use. | A long-term trail. Event history covers recent management events only. |
| `screenshots/phase2/tooling-versions.png` | Git 2.56.0, Terraform v1.17.1 and AWS CLI 2.36.50 installed on the workstation. | That any of them has been used against the account. |