# Module-16 – Zero-Trust & Micro-Segmentation

## Overview
Zero‑Trust (ZT) is a security model that assumes **no implicit trust** inside or outside the network perimeter. Micro‑segmentation enforces granular policies at the workload level.

## Core Concepts
| Concept | Description |
|---------|-------------|
| **Never Trust, Always Verify** | Every connection is authenticated & authorized. |
| **Policy‑Based Access** | Policies are defined per‑application, user, device, and context. |
| **Least‑Privilege** | Access is granted only for the minimum required resources. |
| **Continuous Monitoring** | Real‑time telemetry feeds decisions. |
| **Micro‑Segmentation** | Segment workloads into small zones (often using overlay networks). |

## Architecture Blocks
1. **Identity Provider (IdP)** – SSO, LDAP, OIDC.
2. **Policy Engine** – Central policy definition (e.g., Cisco SecureX, Palo Alto Prisma Access).
3. **Enforcement Points** – Software‑defined perimeters, firewalls, host agents, SD‑WAN.
4. **Telemetry & Analytics** – Logs, NetFlow, SIEM.

## Implementation Examples
### Cisco Zero‑Trust
```text
policy-set ZT-Policy
  match source-group INTERNAL_USERS
  match dest-group INTERNAL_SERVERS
  action allow inspect
exit
```

### Palo Alto Prisma Access (CLI snippet)
```bash
set security zero-trust policy "ztp-01" source any destination any application any action deny
set security zero-trust policy "ztp-01" source user "corp\admin" destination "10.0.0.0/24" application "ssh" action allow
```

## Interview‑Style Questions
- Explain the difference between **Zero‑Trust Network Access (ZTNA)** and a traditional VPN.
- How does micro‑segmentation improve lateral‑movement protection?
- What are the main components of a Zero‑Trust architecture?
- Describe a use‑case where **software‑defined perimeter** replaces a classic firewall.

## References
- *Zero Trust Architecture* – NIST SP 800‑207
- Cisco **Zero Trust* whitepaper
- Palo Alto **Prisma Access** docs
