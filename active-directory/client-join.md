# Domain-Joining CLIENT01

## Summary
CLIENT01 (Windows 11 Enterprise) was joined to the `homelab.local`
domain, authenticating against DC01 as the domain controller.

## Steps taken
1. Deployed CLIENT01 into the same VNet/subnet as DC01
   (vnet-belgiumcentral-1 / snet-belgiumcentral-1), required so the
   client can reach the DC directly on the private network.
2. Attempted domain-join via System Properties → Computer Name →
   Change → Domain: `homelab.local`.
3. **Join failed on first attempt** — see
   [troubleshooting/dns-not-pointing-to-dc.md](../troubleshooting/dns-not-pointing-to-dc.md)
   for the full diagnosis and fix.
4. After fixing DNS, retried the join using domain credentials
   (`HOMELAB\labadmin`) — succeeded, "Welcome to the homelab.local
   domain" confirmation received.
5. Restarted CLIENT01, logged in explicitly as the domain account
   (`HOMELAB\labadmin` via RDP, rather than the machine's local
   `labadmin` account) and confirmed with `whoami`, returning
   `homelab\labadmin`.

## Verification
- `whoami` on CLIENT01 returns `homelab\labadmin`, confirming
  authentication against the domain rather than a local account.
- CLIENT01 appears as a computer object in Active Directory Users and
  Computers, under the default Computers container on DC01.

## Note on account usage
`labadmin` (a Domain Admin account) was used for this login for
simplicity. In a production environment, admin credentials would be
kept separate from day-to-day login accounts to limit the impact of a
compromised account — see `security/least-privilege.md` (planned).
