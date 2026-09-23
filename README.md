# Enterprise Active Directory Lab — Full Account Lifecycle & Security Testing

This lab deploys a real Windows Server 2022 domain controller and Windows 10
client in Azure, builds out an Active Directory domain from scratch, provisions
users at scale via PowerShell, and then runs a full account-security lifecycle
test — lockout policy, lockout, unlock, password reset, disable, and re-enable
— with the results verified against real Windows Security event logs.

## Environments and Technologies Used

- Microsoft Azure (Virtual Machines / Compute, Virtual Network)
- Remote Desktop (RDP)
- Active Directory Domain Services (AD DS)
- Group Policy Management (GPO)
- PowerShell (Active Directory module, automation)
- Windows Security Event Log auditing

## Operating Systems Used

- Windows Server 2022 (Domain Controller)
- Windows 10 (Domain-joined client)

## High-Level Steps

1. Deploy Azure infrastructure — VNet, domain controller VM, client VM, static IP, firewall/connectivity checks
2. Install AD DS, promote the domain controller, create a domain admin account, join the client to the domain
3. Enable Remote Desktop access for standard domain users (not just admins), then bulk-provision domain user accounts via a PowerShell script and verify non-admin RDP login
4. Configure account lockout policy via Group Policy, trigger and verify a real account lockout, unlock and reset the account, then test account disable/enable — all confirmed against real Security event log entries

## Deployment and Configuration Steps

### 1. Infrastructure Setup

Deployed a Virtual Network in Azure with two VMs: a Windows Server 2022
instance promoted to a Domain Controller (`DC-1`), and a Windows 10 client
(`Client-1`) joined to the same network. Set `DC-1`'s NIC to a static private
IP, pointed `Client-1`'s DNS at `DC-1`, and verified connectivity end to end
(ping + `ipconfig /all` DNS resolution).

### 2. Active Directory Domain Services & Domain Admin

Installed AD DS on `DC-1` and promoted it to stand up a new forest
(`mydomain.com`). Built out the OU structure (`_EMPLOYEES`, `_ADMINS`,
`_CLIENTS`), created a dedicated domain admin account, added it to Domain
Admins, and joined `Client-1` to the domain — verified in Active Directory
Users and Computers.

### 3. Remote Access & Bulk User Provisioning

Configured `Client-1`'s Remote Desktop settings to allow the `Domain Users`
group (not just local admins) to RDP in, then wrote and ran a PowerShell
script (adapted from a public AD bulk-provisioning pattern, retargeted at the
domain's own `_EMPLOYEES` OU) to create a batch of domain user accounts in
one pass. Verified every account landed correctly in Active Directory, then
did a live, real non-admin RDP login test — signed into `Client-1` as one of
the newly created accounts and confirmed a fresh domain-user profile loaded
and `whoami` returned the correct `DOMAIN\username`, proving the Remote
Desktop access change actually worked end to end.

### 4. Account Lockout, Unlock/Reset, Disable/Enable — Verified Against Real Logs

This is the core security-testing section of the lab: a full account lifecycle
run against one live domain account, using `System.DirectoryServices.AccountManagement`
to drive repeatable authentication attempts and confirm behavior at every
stage.

- **Baseline:** ran 10 failed login attempts against the domain before any
  lockout policy existed — confirmed the account did *not* lock out (default
  domain lockout threshold is 0 / disabled).
- **Policy configuration:** opened Group Policy Management, edited the
  Default Domain Policy's Account Lockout Policy, and set the lockout
  threshold to 5 invalid attempts (10-minute lockout duration, 10-minute
  counter reset, administrator lockout enabled). Applied immediately with
  `gpupdate /force`.
- **Triggered a real lockout:** ran 6 more failed attempts — the account
  hit `LockedOut: True` with `BadPwdCount: 11`, confirmed directly via
  `Get-ADUser`.
- **Unlocked and reset:** ran `Unlock-ADAccount` and `Set-ADAccountPassword
  -Reset`, confirmed `LockedOut: False`, and confirmed the account could
  authenticate again with the new password.
- **Disable / re-enable:** disabled the account with `Disable-ADAccount` and
  confirmed authentication failed even with the correct password — the
  expected behavior for a disabled AD account. Re-enabled it with
  `Enable-ADAccount` and confirmed authentication succeeded again.
- **Log review:** pulled the real Windows Security event log for the domain
  controller and confirmed the matching audit trail — Event ID 4740
  (account locked out), 4625 (failed logon), 4723/4724 (password change/
  reset), 4725 (account disabled), and 4722 (account enabled) — all
  timestamped and tied to the test account, proving the AD audit trail
  captured every step of the exercise correctly.

## Evidence

Real, timestamped screenshots for every step above are in [`evidence/`](./evidence)
— domain and OU setup, the RDP access change plus a live non-admin login
test, the bulk-provisioning script run, the Group Policy lockout
configuration, the real lockout/unlock/reset/disable/enable results, and the
final Security event log review. Environment identifiers (VM public IPs,
tenant references) are visible as they were during the live lab session;
this was a disposable Azure lab environment, deallocated after evidence
capture.

## What This Demonstrates

- Standing up Active Directory Domain Services from a bare Windows Server
- Structuring a domain with OUs and a least-privilege admin account
- Bulk-provisioning users at scale with PowerShell and the AD module
- Configuring and enforcing Group Policy account security controls
- Diagnosing and remediating a real account lockout, including unlock,
  password reset, and disable/enable workflows
- Validating security control behavior against the underlying Windows
  Security event log, not just the administrative UI
