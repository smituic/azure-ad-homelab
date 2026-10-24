# Azure Home Lab: Active Directory and Intune

Two hands-on labs built on Azure to practice real sysadmin/IT support skills: an on-prem style Active Directory environment (AD DS, DNS, Group Policy) and a cloud endpoint management lab (Entra ID, Intune), plus simulated production incidents that are diagnosed and documented like real support tickets.

## Labs

| Lab                              | Focus                                                                       | Docs                                   |
| -------------------------------- | --------------------------------------------------------------------------- | -------------------------------------- |
| Lab 1: Active Directory          | Domain controller, DNS, OUs, Group Policy, domain join                      | [active-directory/](active-directory/) |
| Lab 2: Cloud endpoint management | Entra ID join, Intune compliance and configuration policies, app deployment | [intune/](intune/README.md)            |

## Why this exists

Built to close a specific gap surfaced in a systems administrator
internship interview around real troubleshooting scenarios (e.g., RDP
authorization issues). Rather than just standing up AD and stopping,
this project deliberately breaks things afterward and documents the
diagnosis process like real support tickets.

## Architecture

**Lab 1**

- Azure VNet (Belgium Central)
- DC01 — Windows Server 2022, Active Directory Domain Services + DNS
- CLIENT01 — Windows 11, domain-joined workstation
- Domain: `homelab.local`

See [`architecture/network-design.md`](architecture/network-design.md) for details.

**Lab 2**

- `win11-intune` — Windows 11 Enterprise, Entra ID joined and managed by Intune
- Separate trial tenant (`smitlab7.onmicrosoft.com`)

See [`intune/README.md`](intune/README.md) for details.

## Structure

- `architecture/` — network design and diagrams
- `active-directory/` — DC setup, users/groups/OUs, Group Policy
- `networking/` — DNS configuration
- `powershell/` — automation scripts used in the lab
- `troubleshooting/` — simulated incident tickets for Lab 1, fully diagnosed
- `intune/` — Lab 2: enrollment walkthrough, screenshots, and two incident tickets
- `security/` — least-privilege notes, Entra ID (stretch goal)

## Status

**Lab 1:** Domain controller, domain join, OUs, users and groups, and
three GPOs are built and verified. Two incident tickets are documented
(RDP authorization, DNS not pointing to the DC). Next: remaining
tickets (domain login failure, service failure) and PowerShell
automation.

**Lab 2:** Complete. Device enrolled, compliance and configuration
policies and a required app deployed, and two incident tickets
documented.
