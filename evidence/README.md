# Evidence index

Screenshots are redacted before being added: account IDs, email addresses, credentials and tokens are covered or cropped. Each entry states what the screenshot shows and what it does not show.

## Phase 2: Starting state of the account (captured 12 Nov 2026)

| File | What it shows | What it does not show |
|---|---|---|
| `screenshots/phase2/s3-account-block-public-access.png` | All four account-level S3 Block Public Access settings were off at the start of the project. | Whether any bucket was exposed. No buckets were examined. |
| `screenshots/phase2/vpc-default-vpc-overview.png` | One VPC in N. Virginia, marked as the default VPC, with the IPv4 range 172.31.0.0/16. | Subnets, route tables and security groups. They were not reviewed. |
| `screenshots/phase2/cloudtrail-event-history-root-activity.png` | CloudTrail event history rows attributed to the root user, confirming root was in everyday use. | A long-term trail. Event history covers recent management events only. |
| `screenshots/phase2/tooling-versions.png` | Git 2.56.0, Terraform v1.17.1 and AWS CLI 2.36.50 installed on the workstation. | That any of them has been used against the account. |

## Phase 3: Account safety (captured 16 to 24 Nov 2026)

| File | What it shows | What it does not show |
|---|---|---|
| `screenshots/phase3/billing-budget-alerts-unverified-before.png` | An existing $5 monthly budget whose two alert recipients were both pending verification. One address had a domain typo. | That alerts would or would not have been delivered. Delivery was not tested. |
| `screenshots/phase3/billing-budget-alert-verified-after.png` | The correctly spelled recipient showing as verified, with the misspelled one still pending. | The final state of the budget. |
| `screenshots/phase3/billing-budget-typo-fixed-after.png` | After the typo was corrected, a single verified recipient on the budget. | Delivery of a real alert. Spend is $0, so no threshold has been crossed. |
| `screenshots/phase3/iam-dashboard-before.png` | The IAM dashboard with zero security recommendations: root MFA enabled and no active root access keys. | Whether root was used day to day. The CloudTrail screenshot in Phase 2 shows that. |
| `screenshots/phase3/iam-lab-group-permissions-after.png` | The group carrying the AWS managed ReadOnlyAccess policy. | The full list of actions ReadOnlyAccess allows. |
| `screenshots/phase3/iam-lab-group-members-after.png` | The non-root user `Alicedaily` as a member of that group. | Anything about the user's sign-in. |
| `screenshots/phase3/iam-alicedaily-mfa-assigned-after.png` | Console access enabled with MFA, no access key, and no sign-in yet. | That MFA worked. This was observed at first sign-in, which was not screenshotted. |
| `screenshots/phase3/alicedaily-allowed-s3-list.png` | The non-root user listing S3 buckets without an error. The list was empty. | That the user can read objects, since no buckets exist. |
| `screenshots/phase3/alicedaily-denied-create-bucket.png` | The same user failing to create a bucket, with a message that the s3:CreateBucket permission is required. | The reason for the denial. No bucket existed afterwards (observed, not screenshotted). |
| `screenshots/phase3/identity-center-not-enabled-after.png` | The IAM Identity Center page still showing the Enable button, with no instance created. | The setup screen that led to the decision. That is described in the decision record. |

## Phase 5: Risk assessment (captured 26 Nov 2026)

| File | What it shows | What it does not show |
|---|---|---|
| `screenshots/phase5/cloudtrail-trails-list.png` | The CloudTrail Trails page in N. Virginia listing no trail. | Trails in other regions, or an organisation trail. Only this region was viewed. |