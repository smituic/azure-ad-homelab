# Ticket: Domain Join Fails — "AD DC for this domain could not be found"

## Symptom
Attempting to join CLIENT01 to `homelab.local` failed immediately with:
"An Active Directory Domain Controller (AD DC) for the domain
homelab.local could not be contacted."

## Hypothesis
The error occurs at the DNS-lookup stage, before credentials are ever
checked — meaning CLIENT01 could not resolve `homelab.local` to an IP
address at all. Suspected cause: CLIENT01 was using Azure's default
DNS servers rather than DC01, which is the only DNS server that knows
about the `homelab.local` zone.

## Diagnosis
Confirmed the VNet (`vnet-belgiumcentral-1`) was configured with
**Default (Azure-provided)** DNS rather than a custom DNS server
pointing at DC01. Azure's default DNS has no knowledge of the private
`homelab.local` zone, which exists only on DC01.

## Root cause
The virtual network's DNS setting was never updated after DC01 was
promoted to a domain controller. Deploying a VM into a VNet does not
automatically configure that VNet to use a domain controller for DNS
— this has to be set explicitly.

## Fix
1. Azure Portal → `vnet-belgiumcentral-1` → DNS servers.
2. Changed from Default (Azure-provided) to **Custom**, set to DC01's
   static private IP (`172.16.0.4`).
3. Saved, then restarted CLIENT01 (via the Azure portal, not a
   Windows-internal restart) so it picked up the new DNS setting.
4. Retried the domain-join — succeeded.

## Verification
CLIENT01 successfully located DC01, joined `homelab.local`, and
subsequent `whoami` confirmed domain authentication.

## Prevention
When deploying any future client onto this VNet, confirm DNS servers
are set to DC01's static IP *before* attempting a domain-join, rather
than discovering the gap via a failed join.
