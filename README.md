# AWS Cloud Resources & Hands-on Lab Portfolio

[![AWS](https://img.shields.io/badge/AWS-Cloud%20Architecture-232F3E?logo=amazon-aws&logoColor=white)](https://aws.amazon.com/)
[![DevOps](https://img.shields.io/badge/DevOps-Hands--on%20Labs-blue)](https://aws.amazon.com/devops/)
[![Security](https://img.shields.io/badge/Security-Best%20Practices-green)](https://docs.aws.amazon.com/security/)

This repository serves as a centralized portfolio of hands-on cloud engineering and DevOps labs on Amazon Web Services (AWS). Each module contains practical implementations, architecture designs, policy definitions, and step-by-step verification evidence.

---

## Repository Modules & Hands-on Labs

| Module / Directory | Topic | Key Concepts & Services | Status |
| :--- | :--- | :--- | :--- |
| [**`AWS-IAM/`**](./AWS-IAM/README.md) | **Identity & Access Management (IAM)** | IAM Users, User Groups (RBAC), AWS Managed Policies, Customer Managed Policies, Resource-Level ARNs, Least Privilege, Authorization Troubleshooting | Completed |

---

## Featured Lab: AWS Identity & Access Management (IAM)

The [`AWS-IAM`](./AWS-IAM/README.md) module covers a comprehensive hands-on implementation of security access controls on AWS:

- **RBAC with User Groups**: Structured `devops_team` and `qa_team` user groups to separate administrative compute duties from testing/auditing roles.
- **Managed vs. Customer-Managed Policies**: Progressed from standard `AmazonEC2FullAccess` and `AmazonEC2ReadOnlyAccess` to fine-grained JSON policies.
- **Resource-Level Control & Least Privilege**: Created `mc-ec2-start-stop-instance` to permit operational tasks (`StartInstances`, `StopInstances`) on a specific server ARN while actively preventing unauthorized actions (`TerminateInstances`).
- **Evidence-Based Verification**: Includes all 12 console screenshots verifying permission enforcement and error diagnostics.

For the full breakdown and policy documents, explore the [AWS-IAM Module](./AWS-IAM/README.md).
