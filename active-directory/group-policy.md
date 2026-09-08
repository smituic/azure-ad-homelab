# Group Policy — Finance Control Panel Restriction

## Summary
Created a GPO restricting Control Panel/Settings access, linked to the
Finance OU only, to demonstrate OU-scoped policy enforcement.

## Steps taken
1. Group Policy Management (on DC01) → right-click Finance OU →
   "Create a GPO in this domain, and Link it here..."
2. Named: `Finance - Restrict Control Panel`
3. Edited the GPO: User Configuration → Policies → Administrative
   Templates → Control Panel → "Prohibit access to Control Panel and
   PC settings" → Enabled
4. Forced policy refresh on CLIENT01: `gpupdate /force`

## Verification
- Logged into CLIENT01 as `HOMELAB\supate` (Subham Patel, Finance OU):
  attempting to open Control Panel/Settings returned "This operation
  has been cancelled due to restrictions in effect on this computer."
- Logged into CLIENT01 as `HOMELAB\jpate` (Jyot Patel, Sales OU):
  Control Panel opened normally — confirming the policy did not leak
  outside its linked OU.

## Why this matters
This confirms GPOs apply based on OU membership, not
domain-wide by default — a policy linked to one OU has no effect on
users/computers in a sibling OU. This is the mechanism that lets a
single AD structure enforce different rules for different departments
without per-user configuration.
