# Lab 2: Cloud Endpoint Management with Microsoft Intune

A Windows 11 VM joined to Microsoft Entra ID and managed by Intune. Lab 1 (`active-directory/`) covers on-prem management with Group Policy; this lab covers the cloud equivalent, where devices are managed over the internet with no domain controller.

## What this lab demonstrates

- Enrolled a Windows 11 endpoint into Intune via Entra ID join with automatic MDM enrollment
- Deployed a compliance policy (firewall, Defender, minimum OS) and a configuration profile (screen lock, block Microsoft accounts)
- Deployed a required app (VLC) with no user interaction
- Diagnosed policies that were assigned but never delivered (user vs. device targeting)
- Broke the device on purpose (firewall off), watched it go noncompliant, fixed it, and confirmed recovery

## Architecture

| Component | Detail                                                                                                |
| --------- | ----------------------------------------------------------------------------------------------------- |
| Tenant    | `smitlab7.onmicrosoft.com` (Intune + Entra ID P2 trials, separate from the Azure subscription tenant) |
| Users     | Two test users (Alice Rivera, Bob Lee) plus a tenant admin                                            |
| Groups    | `IT-Managed-Devices` (users), `IT-Managed-Devices-Win` (devices)                                      |
| Endpoint  | `win11-intune`: Windows 11 Enterprise 24H2, Azure VM (Standard_B2as_v2, Belgium Central)              |
| Join type | Entra ID joined, auto-enrolled in Intune (MDM user scope: All)                                        |

## Policies deployed

| Type          | Name                            | What it does                                                                           |
| ------------- | ------------------------------- | -------------------------------------------------------------------------------------- |
| Compliance    | Win - Baseline Compliance       | Requires firewall, Defender antimalware, real-time protection, minimum OS 10.0.22631.0 |
| Configuration | Win - Screen Lock and MSA Block | Locks the session after 300 seconds idle; blocks Microsoft account sign-in             |
| App           | VLC (Required)                  | Installed automatically on the device group                                            |

## Lab 1 vs Lab 2

|                    | Lab 1: Active Directory          | Lab 2: Intune                       |
| ------------------ | -------------------------------- | ----------------------------------- |
| Directory          | On-prem domain controller (DC01) | Entra ID (cloud)                    |
| Policy tool        | Group Policy                     | Intune                              |
| Targeting          | OUs                              | Groups (user or device)             |
| Device needs       | Network path to the DC           | Internet access                     |
| Compliance grading | None built in                    | Compliant / Noncompliant per policy |

## Documentation

- [Enrollment walkthrough](enrollment.md)
- [Ticket: policies assigned but not applying](troubleshooting/policy-not-applying.md)
- [Ticket: noncompliant device (firewall off)](troubleshooting/noncompliant-device.md)

## Notes

- Screenshots are in `screenshots/`. The VM clock is Pacific time and the Intune portal is Central, so timestamps differ by two hours for the same event.
- The Intune and Entra ID P2 trials were set to not renew and expire on 11/24/2026.
