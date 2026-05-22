# Module-17 – Network Automation & Infrastructure as Code (IaC)

## Overview
Automation accelerates network provisioning, reduces human error, and enables repeatable deployments. This module covers orchestration tools, configuration languages, and best‑practice workflows for modern network operations.

## Core Concepts
| Concept | Description |
|---------|-------------|
| **Ansible** | Agent‑less automation using YAML playbooks; idempotent network module support (ios, junos, eos). |
| **Terraform** | Declarative IaC; providers for cloud networking (AWS VPC, Azure VNet, GCP VPC) and on‑prem devices via `terraform-provider-nokia`, `terraform-provider-cisco`). |
| **YANG / NETCONF** | Data modeling language; NETCONF provides CRUD operations over SSH. |
| **RESTCONF** | HTTP‑based API for YANG models; often used with modern SD‑N controllers. |
| **CI/CD Pipelines** | Integration of linting, testing (Batfish, NAPALM), and push stages (GitHub Actions, Jenkins). |
| **GitOps** | Source‑of‑truth in Git; automated agents reconcile device state (ArgoCD, Flux). |

## Example Ansible Playbook (Cisco IOS)
```yaml
- name: Configure VLANs on Cisco switches
  hosts: switches
  gather_facts: no
  connection: network_cli
  tasks:
    - name: Ensure VLAN 10 exists
      ios_vlan:
        vlan_id: 10
        name: "HR"
        state: present
    - name: Assign access ports to VLAN 10
      ios_interface:
        name: GigabitEthernet1/0/5
        mode: access
        access_vlan: 10
```

## Example Terraform for AWS VPC
```hcl
provider "aws" {
  region = "us-east-1"
}
resource "aws_vpc" "corp" {
  cidr_block = "10.0.0.0/16"
  tags = { Name = "corp-vpc" }
}
resource "aws_subnet" "public" {
  vpc_id            = aws_vpc.corp.id
  cidr_block        = "10.0.1.0/24"
  map_public_ip_on_launch = true
  tags = { Name = "public-subnet" }
}
```

## Validation Tools
- **Batfish** – Network configuration analysis, reachability, policy compliance.
- **NAPALM** – Unified API for retrieving device state; useful for testing playbooks.
- **Cisco DEVNet Sandbox** – Free cloud‑based labs for validation.

## Interview‑Style Questions
1. **What are the advantages of an idempotent automation framework like Ansible over imperative scripts?**
2. **Explain how Terraform’s state file enables drift detection.**
3. **Describe a CI/CD pipeline for network changes that includes linting, testing, and automated roll‑back.**
4. **When would you choose NETCONF/YANG over a vendor‑specific CLI API?**
5. **How does GitOps improve network change management?**

## References
- Ansible *Network Automation Guide* (2023)
- Terraform *Provider Documentation* – `aws`, `ciscoios` (2024)
- IETF RFC 7950 – YANG 1.1
- *Network Automation at Scale* – O'Reilly, 2022
