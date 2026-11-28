- [ADR-002: build role for Terraform](decisions/002-build-role.md) (proposed, not yet tested)

Date: 28 Nov 2026
Status: **Proposed.** Nothing in this record has been built or tested yet. It will be marked Accepted after the tests below pass.

## Context
Terraform has to create the lab resources, and it needs an identity to do that. The everyday user (`Alicedaily`) is read-only and has no access keys, by decision (see ADR-001). Giving Terraform an administrator identity, or adding access keys, would recreate the over-permission problem this project examines (threat T2 in the threat model).

## Requirements
- No long-lived access keys.
- Temporary credentials that expire on their own.
- MFA required when the role is borrowed.
- Permissions limited to lab resources.
- Actions that would let the role escalate its own power are explicitly denied.

## Options considered
| Option | Outcome |
|---|---|
| Use the root user | Rejected. Root cannot be restricted by IAM policies. |
| Give `Alicedaily` administrator or broad write access | Rejected. Defeats least privilege and ADR-001. |
| Create access keys for a Terraform user | Rejected. Long-lived secrets are the thing being avoided. |
| IAM Identity Center | Not enabled in this account (see ADR-001). |
| **A scoped role, borrowed through temporary credentials** | **Selected.** |

## Decision
1. Create a role named `terraform-build-role`. It is deliberately named without the `lab-` prefix, so the role's own permissions, which cover `lab-*` names, do not apply to itself and it cannot edit its own policy.
2. The role trusts only `Alicedaily`, and only when MFA is present. Maximum session length is 1 hour.
3. `Alicedaily` receives two additions: the AWS managed policy `SignInLocalDevelopmentAccess`, so `aws login` can issue temporary credentials from console sign-in, and permission to borrow this one role.
4. Lab resources are named with the prefix `lab-`, and the role's permissions are limited to those names where AWS allows it.
5. A set of explicit denies blocks actions that could create new identities, keys or powerful policies.

### Trust policy (draft)
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": { "AWS": "arn:aws:iam::<ACCOUNT_ID>:user/Alicedaily" },
      "Action": "sts:AssumeRole",
      "Condition": { "Bool": { "aws:MultiFactorAuthPresent": "true" } }
    }
  ]
}
```

### Permission added to the user (draft)
```json
{
  "Effect": "Allow",
  "Action": "sts:AssumeRole",
  "Resource": "arn:aws:iam::<ACCOUNT_ID>:role/terraform-build-role"
}
```

### Permissions of the build role (draft)
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "LabBuckets",
      "Effect": "Allow",
      "Action": ["s3:*"],
      "Resource": ["arn:aws:s3:::lab-*", "arn:aws:s3:::lab-*/*"]
    },
    {
      "Sid": "ListBuckets",
      "Effect": "Allow",
      "Action": "s3:ListAllMyBuckets",
      "Resource": "*"
    },
    {
      "Sid": "LabNetworkInOneRegion",
      "Effect": "Allow",
      "Action": [
        "ec2:CreateVpc", "ec2:DeleteVpc", "ec2:Describe*",
        "ec2:CreateSecurityGroup", "ec2:DeleteSecurityGroup",
        "ec2:AuthorizeSecurityGroupIngress", "ec2:RevokeSecurityGroupIngress",
        "ec2:AuthorizeSecurityGroupEgress", "ec2:RevokeSecurityGroupEgress",
        "ec2:CreateTags", "ec2:DeleteTags"
      ],
      "Resource": "*",
      "Condition": { "StringEquals": { "aws:RequestedRegion": "us-east-1" } }
    },
    {
      "Sid": "LabRoles",
      "Effect": "Allow",
      "Action": [
        "iam:CreateRole", "iam:DeleteRole", "iam:GetRole", "iam:TagRole",
        "iam:PutRolePolicy", "iam:DeleteRolePolicy", "iam:GetRolePolicy",
        "iam:ListRolePolicies", "iam:ListAttachedRolePolicies",
        "iam:ListInstanceProfilesForRole"
      ],
      "Resource": "arn:aws:iam::<ACCOUNT_ID>:role/lab-*"
    },
    {
      "Sid": "LabTrail",
      "Effect": "Allow",
      "Action": [
        "cloudtrail:CreateTrail", "cloudtrail:DeleteTrail",
        "cloudtrail:StartLogging", "cloudtrail:StopLogging",
        "cloudtrail:GetTrail", "cloudtrail:GetTrailStatus",
        "cloudtrail:PutEventSelectors", "cloudtrail:GetEventSelectors",
        "cloudtrail:ListTags", "cloudtrail:AddTags"
      ],
      "Resource": "arn:aws:cloudtrail:us-east-1:<ACCOUNT_ID>:trail/lab-*"
    },
    {
      "Sid": "ReadTrails",
      "Effect": "Allow",
      "Action": "cloudtrail:DescribeTrails",
      "Resource": "*"
    },
    {
      "Sid": "NeverAllowed",
      "Effect": "Deny",
      "Action": [
        "iam:CreateUser", "iam:CreateAccessKey", "iam:CreateLoginProfile",
        "iam:AttachUserPolicy", "iam:AttachRolePolicy", "iam:AttachGroupPolicy",
        "iam:PutUserPolicy", "iam:PutGroupPolicy", "organizations:*", "account:*"
      ],
      "Resource": "*"
    }
  ]
}
```

## Test plan
| # | Test | Expected result | Result |
|---|---|---|---|
| 1 | Borrow the role with MFA | Allowed | Not run |
| 2 | Create a bucket named with the `lab-` prefix | Allowed | Not run |
| 3 | Create a bucket without the `lab-` prefix | Denied | Not run |
| 4 | Create an IAM user | Denied | Not run |
| 5 | Attach an administrator policy to a role | Denied | Not run |
| 6 | Borrow the role without MFA | Denied | Not run |

## Trade-offs and limitations
- `Alicedaily` is no longer purely read-only. It can now obtain temporary command-line credentials and borrow one role. This weakens the ADR-001 description, which is why this record states it openly.
- Most EC2 actions cannot be restricted by resource name, so network permissions are limited by Region instead.
- `s3:*` on lab buckets is broader than necessary. It will be narrowed to the exact actions Terraform uses.
- A role that can write policies on `lab-*` roles is an escalation risk. A permissions boundary would be a stronger control and is parked for a later project.
- It is not verified that credentials issued by `aws login` satisfy the MFA condition. If they do not, the design changes and this record will be updated.
- Terraform has not been tested with `aws login` credentials.

## Revisit when
The tests are run, when Terraform shows which permissions are really needed or when the environment becomes multi-account.