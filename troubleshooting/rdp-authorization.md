# Ticket: Domain User Cannot RDP Into a Domain-Joined Machine

## Symptom
A domain user (Smit Patel, `HOMELAB\spate`) could not establish an RDP
session to CLIENT01. Connection failed with:
"We couldn't connect to the remote PC. You might not have permission
to sign in remotely. Contact your network administrator for
assistance." (Error code: 0x2407)

The same credentials authenticated successfully as a domain identity
elsewhere (confirmed valid via `whoami` after signing in locally),
ruling out a bad password or disabled account.

## Hypothesis
RDP access is not granted by domain membership alone. Windows checks
membership in a specific **local** group on the target machine —
Remote Desktop Users — separately from Active Directory authentication.
A valid domain user with no local rights on a given machine will be
authenticated but still denied the remote session.

## Diagnosis
On CLIENT01, checked local group membership:
```powershell
Get-LocalGroupMember -Group "Remote Desktop Users"
```
`HOMELAB\spate` was not listed. Only local admin accounts (which get
implicit RDP rights via local Administrators) had access.

## Root cause
Newly created domain users are not automatically added to any
machine's Remote Desktop Users group. RDP access must be explicitly
granted per machine (or via GPO/group at scale), separate from
creating the account itself.

## Fix
Logged into CLIENT01 as a local/domain admin, then:
1. Computer Management → Local Users and Groups → Groups →
   Remote Desktop Users
2. Add... → `HOMELAB\spate` → OK

Alternative via PowerShell:
```powershell
Add-LocalGroupMember -Group "Remote Desktop Users" -Member "HOMELAB\spate"
```

## Verification
- Reconnected via RDP as `HOMELAB\spate` — succeeded.
- `whoami` confirmed domain authentication: `homelab\spate`
- `whoami /priv` confirmed reduced privilege level compared to a
  Domain Admin session (most privileges disabled — shutdown, docking
  station removal, working-set changes — versus an admin account
  which holds these enabled), demonstrating least-privilege access
  is working as intended.

## Prevention / at scale
For more than a handful of users, granting RDP access per-machine via
local group membership doesn't scale. The correct approach is a GPO
that adds a domain group (e.g., IT-Helpdesk) to the Remote Desktop
Users group on target machines automatically — see
`active-directory/group-policy.md` (planned).
