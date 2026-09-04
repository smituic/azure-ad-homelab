# Domain Controller Setup (DC01)

## Summary
DC01 is a Windows Server 2022 VM promoted to a domain controller,
hosting the `homelab.local` Active Directory forest and its DNS zone.

## Steps taken
1. Deployed VM (Standard_B2as_v2, Windows Server 2022 Datacenter: Azure
   Edition) into rg-homelab, Belgium Central.
2. Set the VM's private IP to static (172.16.0.4) via the NIC's IP
   configuration in the Azure portal, before any role installation —
   to guarantee the address DNS/clients depend on never changes.
3. Installed the **Active Directory Domain Services (AD DS)** role via
   Server Manager.
4. Promoted the server to a domain controller:
   - Deployment type: **Add a new forest**
   - Root domain name: `homelab.local`
   - DNS Server and Global Catalog: enabled (default)
   - DNS delegation: skipped — expected/correct for a standalone forest
     with no parent public DNS zone to delegate from.
5. Server rebooted automatically to complete promotion.

## Verification
- `homelab.local` visible in **Active Directory Users and Computers**
  with default containers (Users, Computers, Domain Controllers).
- **DNS** role now listed in Server Manager; `homelab.local` present as
  a forward lookup zone.
- Added a DNS forwarder (8.8.8.8) so the DC can resolve external
  internet names in addition to the internal domain.

## Notes
- DSRM (Directory Services Restore Mode) password set separately from
  the local admin login — used only for AD recovery scenarios.
