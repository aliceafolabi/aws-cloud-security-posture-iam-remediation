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