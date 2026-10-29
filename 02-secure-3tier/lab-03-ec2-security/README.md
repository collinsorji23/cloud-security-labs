
# Lab 03 — AWS EC2 Security Hardening

## Objective

Assess and improve the network security of an
Amazon EC2 instance using AWS security groups.

## Environment

- AWS Region: eu-north-1 (Stockholm)
- AMI: Amazon Linux 2023
- Instance Type: t3.micro
- Storage: 8 GiB EBS
- Operating System: Linux
- SSH Client: PuTTY

## Initial Security Findings

| Port | Source | Finding |
|---|---|---|
| SSH 22 | 0.0.0.0/0 | Unrestricted SSH exposure |
| HTTP 80 | 0.0.0.0/0 | Unnecessary public access |

## Remediation Implemented

1. Restricted SSH access to a trusted public
   IPv4 address using /32 CIDR notation.
2. Removed the unnecessary HTTP inbound rule.
3. Verified the updated security group rules
   using the AWS Management Console.

## Final Inbound Configuration

| Port | Source | Purpose |
|---|---|---|
| SSH 22 | Trusted IP /32 | Restricted administration |

## Security Principles

- Network-level least privilege
- Reducing attack surface
- Restricting unnecessary inbound traffic
- Defence in depth

## Limitations

Security group rules were verified in the console.
Live SSH connectivity under the new rules has
not yet been tested.

## Current Status

Inbound security group hardening implemented.
Live SSH verification and further security
assessment remain pending.

## Outbound Security Assessment

### Finding

The EC2 security group permits all outbound
IPv4 traffic to 0.0.0.0/0.

### Security Risk

If the EC2 instance is compromised,
unrestricted outbound access could facilitate
data exfiltration, command-and-control
communications, and malicious downloads.

### Recommended Remediation

Identify the instance's legitimate outbound
network requirements before implementing
least-privilege egress restrictions.

### Current Status

Risk identified and documented.
Outbound rules remain unchanged.
Further assessment is required.

## IAM Least-Privilege Policy Simulation

### Objective

Verify that a custom IAM policy permits
reading S3 objects under the reports/
prefix while excluding other prefixes.

### Policy Tested

ec2-reports-read-policy.json

### Simulated Action

s3:GetObject

### Test Results

| Resource | Decision | Result |
|---|---|---|
| reports/audit.pdf | allowed | PASS |
| confidential/salaries.pdf | implicitDeny | PASS |

### Security Findings

The policy grants s3:GetObject permissions
for objects within the reports/ prefix.

Objects outside that prefix are not granted
access by this policy.

### Limitations

- Tests used AWS IAM Policy Simulator.
- No S3 resources were created.
- The policy was not attached to the EC2 role.
- Other permissions could affect actual access.
- Live S3 access was not tested.

### Outcome

Both simulated permission tests passed.
Resource-level least privilege was demonstrated.



## EC2 IAM Role Security Assessment

### Role Examined
DemoRolesForEC2

### Trust Relationship
- Trusted principal: ec2.amazonaws.com
- Allowed action: sts:AssumeRole
- Expected EC2 service trust confirmed.

### Permissions Audit
- Attached managed policy: IAMReadOnlyAccess
- Inline policies: None
- AdministratorAccess: Not attached
- Permissions boundary: None configured

### Security Assessment
The role uses the expected EC2 service trust
relationship.

Its attached managed policy grants broad
read-only access to IAM information.

Whether this meets least privilege depends
on the intended workload requirements.

### Recommendations
- Confirm the EC2 application's actual needs.
- Remove unnecessary permissions only after
  determining the required access.
- Prefer resource-scoped permissions.
- Avoid storing long-term AWS access keys
  on EC2 instances.

### Remediation Status
Assessment completed.
No IAM role permissions were modified.
Further workload-specific review is required.
