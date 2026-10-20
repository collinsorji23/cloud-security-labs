
# Lab 02 — Implementing Least Privilege in AWS IAM

## Objective
Design and test IAM policies using the principle of least privilege.

## Environment
- AWS Region: eu-north-1
- AWS CLI v2
- VS Code and Git Bash
- Temporary CLI authentication (horizon profile)

## Policies Created
1. iam-auditor-policy.json
   - Allows listing IAM users, groups and roles.
   - Does not grant IAM modification permissions.

2. iam-explicit-deny-test.json
   - Demonstrates how an explicit Deny overrides an Allow.


## Test Results

### Test 1: Least-Privilege Auditor Policy

Policy: `iam-auditor-policy.json`

| IAM Action | Expected | Actual |
|---|---|---|
| iam:ListUsers | allowed | allowed |
| iam:ListRoles | allowed | allowed |
| iam:CreateRole | implicitDeny | implicitDeny |

**Finding:** The proposed policy permits IAM
inventory operations without granting role
creation permissions.

### Test 2: Explicit Deny Evaluation

Policy: `iam-explicit-deny-test.json`

| IAM Action | Expected | Actual |
|---|---|---|
| iam:ListUsers | allowed | allowed |
| iam:ListRoles | explicitDeny | explicitDeny |
| iam:CreateRole | implicitDeny | implicitDeny |

**Finding:** An explicit Deny overrides an
Allow for the same action.

### Testing Method

Both policies were evaluated using AWS CLI
`iam simulate-custom-policy`.

The simulations did not modify existing IAM
permissions or execute the tested IAM actions.

### Limitation

The tests evaluated the custom policies in
isolation. They do not prove that least privilege
is enforced on the existing administrator
identity, orji23.


## Reproducing the Tests

Run these commands from the root directory
of the cloud-security-labs repository using Git Bash.

### Test 1: Auditor Policy

```bash
aws iam simulate-custom-policy \
  --policy-input-list "$(cat 01-aws-foundations/lab-02-least-privilege/iam-auditor-policy.json)" \
  --action-names iam:ListUsers iam:ListRoles iam:CreateRole \
  --profile horizon \
  --query 'EvaluationResults[].{Action:EvalActionName,Decision:EvalDecision}' \
  --output table
```

### Test 2: Explicit Deny Policy

```bash
aws iam simulate-custom-policy \
  --policy-input-list "$(cat 01-aws-foundations/lab-02-least-privilege/iam-explicit-deny-test.json)" \
  --action-names iam:ListUsers iam:ListRoles iam:CreateRole \
  --profile horizon \
  --query 'EvaluationResults[].{Action:EvalActionName,Decision:EvalDecision}' \
  --output table
```

These commands require an authenticated AWS CLI
profile with permission to run IAM policy simulations.

The policy simulator evaluates proposed permissions
without performing the requested IAM operations.


## Security Findings
- AWS IAM denies actions by default.
- An applicable explicit Deny overrides an Allow.
- Multiple identity policies can contribute permissions.
- Testing a custom policy alone does not evaluate every permission granted to an existing IAM user.

## Remaining Risk
The IAM user orji23 retains AdministratorAccess through ProjectHorizonAdmins.

Least privilege has been demonstrated through policy simulation, but has not yet been enforced on the existing administrator identity.

## Cost and Safety
No paid AWS resources were deployed.
No permanent IAM access keys were created.
Existing administrator permissions were not modified.


## Recommended Remediation Plan

### Finding
The IAM user `orji23` inherits AdministratorAccess
through the ProjectHorizonAdmins group.

### Risk
Excessive permissions increase the potential
impact of accidental changes or compromised
credentials.

### Recommended Actions

1. Identify the AWS permissions required for
   normal daily activities.

2. Design least-privilege policies granting
   only the necessary actions.

3. Test the proposed policies using IAM
   policy simulation.

4. Establish and verify a secure administrative
   recovery method with MFA before modifying
   existing administrator permissions.

5. Preserve the permissions required for
   temporary browser-based AWS CLI login.

6. Replace unnecessary administrator access
   only after the new access model has been
   tested and a recovery procedure verified.

7. Validate effective permissions after
   the change and document the results.

### Current Remediation Status
Open - Remediation planned but not implemented.

The existing administrator permissions have
not been modified during this lab.
