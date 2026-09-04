# Azure AD Home Lab

A hands-on Active Directory environment built on Azure to practice real
sysadmin/IT support skills: AD DS, DNS, Group Policy, and diagnosing
simulated production incidents.

## Why this exists
Built to close a specific gap surfaced in a systems administrator
internship interview around real troubleshooting scenarios (e.g., RDP
authorization issues). Rather than just standing up AD and stopping,
this project deliberately breaks things afterward and documents the
diagnosis process like real support tickets.

## Architecture
- Azure VNet (Belgium Central)
- DC01 — Windows Server 2022, Active Directory Domain Services + DNS
- CLIENT01 — Windows 11, domain-joined workstation
- Domain: `homelab.local`

See [`architecture/network-design.md`](architecture/network-design.md) for details.

## Structure
- `architecture/` — network design and diagrams
- `active-directory/` — DC setup, users/groups/OUs, Group Policy
- `networking/` — DNS configuration
- `powershell/` — automation scripts used in the lab
- `troubleshooting/` — 5 simulated incident tickets, fully diagnosed
- `security/` — least-privilege notes, Entra ID (stretch goal)

## Status
In progress — CLIENT01 successfully domain-joined to homelab.local. Next: create OUs, security groups, and test users.
