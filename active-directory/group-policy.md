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

## Group Policy — IT Screen Saver Timeout

### Summary
Created a GPO enforcing a 120-second screen saver timeout with
password protection, linked to the IT OU.

### Verification challenge
Initial visual testing (waiting to see the screensaver activate) showed
no effect, suggesting the GPO had failed. Two diagnostic dead-ends were
ruled out before finding the real explanation:
- Confirmed the GPO settings were correctly configured (Enabled, 120s)
  in the Group Policy Management Editor.
- Confirmed the GPO was linked to the IT OU and the test user (Smit
  Patel) was correctly placed inside that OU.
- Ran `gpresult /h` to check applied settings — but the first attempt
  was run from an elevated ("Run as Administrator") PowerShell window,
  which silently executes under a *different* user context
  (`labadmin`) than the visibly logged-in user (`spate`). The report
  showed "No settings defined" — correctly reflecting labadmin's
  policy (he's not in the IT OU), not evidence of failure.
- Re-ran `gpresult /h` from a non-elevated shell as the actual logged-in
  user. This confirmed the GPO applied correctly: all three settings
  (Enable screen saver, Password protect the screen saver, Screen
  saver timeout = 120s) showed as Enabled with "IT - Screen Timeout"
  as the winning GPO.

### Root cause of the "failure"
Not a GPO failure at all. Visual screensaver activation is unreliable
to test over RDP/in a VM — Windows often suppresses or alters
screensaver behavior during a remote session. The GPO was applying
correctly the entire time; the verification method was flawed, not
the policy.

### Lesson
`gpresult /h` (or `/r` for a quick summary) is the authoritative way
to verify GPO application — never rely on visually observing expected
behavior, especially in RDP/VM environments. Also: elevated
("Run as Administrator") shells execute under a different effective
user context and will report on that account's policy, not the
visibly logged-in user's — always verify `gpresult`'s "User name"
field matches the account you intend to check.
