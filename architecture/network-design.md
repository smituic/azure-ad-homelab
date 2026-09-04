# Network Design

## Overview
Single-VNet lab environment hosting one domain controller and one
domain-joined client, in Azure's Belgium Central region.

## Components
| Resource | Type | Value |
|---|---|---|
| Resource group | rg-homelab | Belgium Central |
| Virtual network | vnet-belgiumcentral-1 | 172.16.0.0/24 |
| Subnet | snet-belgiumcentral-1 | shared by DC01 and CLIENT01 |
| DC01 | Windows Server 2022, Standard_B2as_v2 | private IP 172.16.0.4 (static) |
| CLIENT01 | Windows 11 Enterprise, Standard_B2as_v2 | private IP 172.16.0.5, joined to homelab.local |

## Design decisions
- **Static private IP on DC01**: DNS records and domain lookups resolve
  to the DC's private IP. A dynamic IP could change on VM restart,
  silently breaking domain authentication for every client — so it's
  pinned before any client joins.
- **Single VNet/subnet for both VMs**: domain-join requires CLIENT01 to
  reach DC01 directly on the private network. Different VNets would be
  network-isolated from each other without extra peering — avoided
  entirely by keeping both VMs on the same VNet from the start.
- **Region constraint**: Azure for Students subscription is locked to a
  fixed allow-list of deployment regions (West US, Belgium Central,
  Central US, Norway East, Canada Central) via subscription policy.
  Belgium Central was selected after Central US, West US, and Canada
  Central all hit VM-size capacity exhaustion at time of deployment.
- **VNet DNS servers set to DC01's static IP (172.16.0.4)**: required
  so domain-joined clients can resolve `homelab.local` to locate the
  domain controller. Azure's default DNS has no knowledge of private
  AD DNS zones — this must be set explicitly, and was the root cause
  of an initial domain-join failure (see
  troubleshooting/dns-not-pointing-to-dc.md).
