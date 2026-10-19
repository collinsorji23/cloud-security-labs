# Lab 01 — AWS IAM Security Audit

## Project Horizon 2.0 | Cloud Security Engineering

**Date:** 19 October 2026  
**Platform:** Amazon Web Services (AWS)  
**Tools:** AWS CLI v2, IAM, Windows 11  
**Region:** Europe (Stockholm) — eu-north-1

## 1. Objective

Perform a foundational security audit of a newly configured AWS environment, focusing on identity and access management (IAM), group membership, administrative permissions, MFA and long-term access keys.

## 2. Environment Setup

- Created an AWS account for hands-on cloud security training.
- Configured MFA for the root user.
- Created IAM user `orji23`.
- Configured passkey MFA for the IAM user.
- Created the `ProjectHorizonAdmins` IAM group.
- Configured AWS CLI with browser-based authentication and temporary credentials.
- Created a $5 monthly AWS Budget for cost alerts.

## 3. IAM Security Audit

### Check 1 — Enumerate IAM Users

```bash
aws iam list-users --profile horizon --query "Users[].UserName" --output table
```

**Finding:** Identified the IAM user `orji23`.

### Check 2 — Inspect Group Policies

```bash
aws iam list-attached-group-policies --group-name ProjectHorizonAdmins --profile horizon --output table
```

**Finding:** The group has the AWS-managed `AdministratorAccess` policy attached.

**Risk:** Members inherit broad administrative access, which exceeds the permissions required for routine lab activities.

### Check 3 — Inspect Group Membership

```bash
aws iam list-groups-for-user --user-name orji23 --profile horizon --output table
```

**Finding:** Confirmed that `orji23` belongs to `ProjectHorizonAdmins`.

### Check 4 — Inspect Direct User Policies

```bash
aws iam list-attached-user-policies --user-name orji23 --profile horizon --output table
```

**Finding:** The user has `SignInLocalDevelopmentAccess` attached directly.

This illustrates the difference between direct IAM permissions and permissions inherited through group membership.

### Check 5 — Audit Permanent Access Keys

```bash
aws iam list-access-keys --user-name orji23 --profile horizon --output table
```

**Finding:** No IAM access keys were returned.

**Security benefit:** Avoiding long-term access keys reduces the risk of credential exposure.

## 4. Security Findings

| Finding | Assessment |
|---|---|
| Root MFA configured | Positive security control |
| IAM user passkey MFA | Positive security control |
| No permanent access keys | Positive security control |
| Temporary CLI credentials | Positive security control |
| AdministratorAccess inherited by IAM user | Least-privilege concern |
| $5 AWS Budget | Cost monitoring control, not a spending cap |

## 5. Recommended Remediation

1. Replace routine administrator access with narrowly scoped IAM policies or roles.
2. Continue using MFA and temporary credentials.
3. Audit IAM permissions regularly.
4. Avoid creating unnecessary access keys.
5. Protect credentials and exclude sensitive information from GitHub.
6. Review AWS billing and budget alerts throughout the project.

## 6. Lessons Learned

This lab demonstrated how to use AWS CLI to enumerate IAM identities, trace inherited permissions, identify excessive privileges and audit long-term credentials.

The key security principle demonstrated was **least privilege**: an identity should have only the permissions required to perform its tasks.

## 7. Next Steps

- Implement least-privilege permissions.
- Explore IAM policy evaluation.
- Build a secure AWS networking environment.
- Enable and investigate AWS security logging where supported.

**Status:** Initial IAM audit completed. Least-privilege remediation pending.