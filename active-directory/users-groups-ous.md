# Users, Groups, and OUs

## Organizational Units
Created under `homelab.local`:
- `IT`
- `Finance`
- `Sales`

OUs organize users and computers for Group Policy targeting — a GPO
linked to an OU applies to every user/computer inside it automatically.

## Security Groups
- `IT-Helpdesk` (Global, Security) — created inside the IT OU.
  Intended to hold users who need helpdesk-level permissions (e.g.,
  password resets), separate from IT-wide policy via the OU itself.

## Users
- Smit Patel (`spate`) — created in the IT OU, added to `IT-Helpdesk`.

## Design note: OUs vs Groups
OUs and groups solve different problems and are not interchangeable:
- **OUs** determine which Group Policies apply to a user/computer.
  A user belongs to exactly one OU.
- **Groups** determine what a user is permitted to do (e.g., RDP
  access, password-reset rights). A user can belong to many groups,
  and groups can span multiple OUs.

Example: a user can sit in the IT OU (inheriting IT-wide desktop
policy) while only belonging to some of IT's functional groups (e.g.,
IT-Helpdesk but not Security or IAM), reflecting real least-privilege
access control.
