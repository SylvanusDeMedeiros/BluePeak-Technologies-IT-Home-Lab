# CLIENT01 Domain Join and RBAC Access Testing

## Project Overview

This phase of the BluePeak Technologies home lab focused on deploying a Windows 11 Enterprise client workstation and integrating it with the `bluepeak.local` Active Directory domain.

CLIENT01 was configured with a static IPv4 address and configured to use DC01 as its DNS server. After verifying network connectivity, DNS resolution, and Active Directory domain controller discovery, CLIENT01 was successfully joined to the BluePeak domain.

A domain user, Jordan Lee (`jordan.lee`), was then used to test role-based access control (RBAC). Jordan's membership in the `SG-IT` security group allowed access to the IT department network share while access to the Human Resources share was denied.

This demonstrated both authorized access and least-privilege enforcement within the BluePeak Technologies environment.

## CLIENT01 Configuration

CLIENT01 was deployed as the Windows 11 Enterprise workstation for the BluePeak Technologies lab.

### Virtual Machine Configuration

- Computer Name: `CLIENT01`
- Operating System: Windows 11 Enterprise Evaluation
- Hyper-V Generation: Generation 2
- Startup Memory: 4096 MB
- Dynamic Memory: Disabled
- Virtual Processors: 2
- Virtual Hard Disk: 64 GB
- Virtual Switch: `BluePeak-Lab`
- Trusted Platform Module (TPM): Enabled

### Network Configuration

CLIENT01 was assigned the following static IPv4 configuration:

- IP Address: `10.10.10.20`
- Subnet Mask: `255.255.255.0`
- Default Gateway: None
- Preferred DNS Server: `10.10.10.10`

DC01 (`10.10.10.10`) provides DNS and Active Directory Domain Services for the `bluepeak.local` domain.

No default gateway was configured because the BluePeak lab uses an isolated Hyper-V Internal virtual switch and does not currently require Internet routing.

## Network and Active Directory Connectivity Verification

Before joining CLIENT01 to the domain, connectivity between the workstation and DC01 was verified.

### IP Connectivity Test

CLIENT01 successfully communicated with DC01 using its IPv4 address:

`ping 10.10.10.10`

Results:

- 4 packets sent
- 4 packets received
- 0% packet loss

### DNS Resolution Test

CLIENT01 was configured to use DC01 (`10.10.10.10`) as its DNS server.

The `bluepeak.local` domain successfully resolved to:

`10.10.10.10`

The fully qualified domain name (FQDN) of the domain controller was also successfully resolved:

`DC01.bluepeak.local` → `10.10.10.10`

### Domain Controller Discovery

Active Directory domain controller discovery was verified using:

`nltest /dsgetdc:bluepeak.local`

CLIENT01 successfully discovered:

- Domain Controller: `DC01.bluepeak.local`
- Domain: `bluepeak.local`
- Forest: `bluepeak.local`
- Active Directory services including DNS, LDAP, Kerberos, and Global Catalog

The command completed successfully, confirming CLIENT01 could locate the BluePeak domain controller before the domain join was attempted.

## CLIENT01 Domain Join

After network connectivity, DNS resolution, and domain controller discovery were verified, CLIENT01 was joined to the BluePeak Technologies Active Directory domain.

### Domain Join Configuration

- Computer Name: `CLIENT01`
- Active Directory Domain: `bluepeak.local`
- NetBIOS Domain: `BLUEPEAK`
- Domain Controller: `DC01.bluepeak.local`
- Domain Controller IP: `10.10.10.10`

Domain Administrator credentials were used to authorize the computer account creation and domain join.

Windows confirmed the operation with:

`Welcome to the bluepeak.local domain.`

CLIENT01 was then restarted to complete the domain membership change.

After the restart, the Windows sign-in screen displayed:

`Sign in to: BLUEPEAK`

This confirmed that CLIENT01 recognized the BluePeak Active Directory domain.

### Evidence

- `17-CLIENT01-Domain-Join-Success.png` — Windows confirmation that CLIENT01 successfully joined `bluepeak.local`.
## Domain User Authentication Test

After CLIENT01 joined the `bluepeak.local` domain, the workstation was used to test authentication with an Active Directory user account.

Jordan Lee signed in using the domain account:

`BLUEPEAK\jordan.lee`

Because the account had been configured with **User must change password at next logon**, Windows required Jordan to change the temporary password before completing the first sign-in.

After the password change, Windows created Jordan's local user profile and successfully loaded the desktop.

The authenticated identity was verified from Command Prompt using:

`whoami`

Result:

`bluepeak\jordan.lee`

This confirmed that Jordan was authenticated using the Active Directory domain account rather than the local CLIENT01 administrator account.

## RBAC Network Share Access Testing

Role-Based Access Control (RBAC) was tested using Jordan Lee's Active Directory account.

Jordan is assigned to the IT department and is a member of the following security group:

`SG-IT`

### Authorized Access Test — IT Share

Jordan attempted to access:

`\\DC01\IT`

Access was successful.

Jordan was also able to create:

`Jordan-Access-Test.txt`

inside the IT shared folder, confirming that the assigned permissions allowed modification of department resources.

### Unauthorized Access Test — Human Resources Share

Jordan then attempted to access:

`\\DC01\Human Resources`

Windows denied access and displayed a message stating that Jordan did not have permission to access the network resource.

Jordan is not a member of `SG-HR`, so the denied access demonstrated that the department security controls were enforcing least privilege.

### RBAC Validation

The completed access tests demonstrated:

- `Jordan Lee → SG-IT → IT Share → Access Allowed`
- `Jordan Lee → Not a member of SG-HR → HR Share → Access Denied`

This confirmed that access to BluePeak department resources was controlled through Active Directory security-group membership rather than being granted directly to individual users.

### Evidence

- `18-Jordan-IT-Share-Access.png` — Jordan successfully accessed the IT share and created a test file.
- `19-Jordan-HR-Access-Denied.png` — Jordan was denied access to the Human Resources share.
- `20-Jordan-SG-IT-Membership.png` — Active Directory group membership showing Jordan assigned to `SG-IT`.

## Troubleshooting Notes

During domain-user testing, CLIENT01 initially attempted to use Hyper-V Enhanced Session Mode.

Enhanced Session Mode uses Remote Desktop Services functionality. Because Jordan Lee is a standard domain user and was not granted Remote Desktop logon rights, Windows displayed a message indicating that the account was not authorized for remote sign-in.

Rather than granting Jordan unnecessary Remote Desktop permissions, Enhanced Session Mode was disabled and the VM was accessed through the standard Hyper-V console session.

Jordan was then able to sign in successfully.

This preserved the principle of least privilege by avoiding unnecessary privilege changes simply to accommodate the management interface.

## Skills Demonstrated

This phase of the project demonstrated hands-on experience with:

- Windows 11 Enterprise deployment
- Hyper-V virtual workstation configuration
- Static IPv4 addressing
- DNS client configuration
- Network connectivity testing
- DNS name resolution
- Active Directory domain controller discovery
- Windows domain joining
- Active Directory user authentication
- First-logon password changes
- Security group-based authorization
- NTFS and SMB share access testing
- Role-Based Access Control (RBAC)
- Principle of least privilege
- Access-denied troubleshooting
- Hyper-V Basic vs. Enhanced Session troubleshooting
- Technical documentation and evidence collection

## Project Result

CLIENT01 was successfully integrated into the `bluepeak.local` Active Directory environment.

A BluePeak employee account was able to authenticate from the domain-joined workstation and access resources authorized through department security-group membership.

Unauthorized access to another department's resources was successfully denied.

The completed tests demonstrated a functional Active Directory environment where authentication and resource authorization are controlled through centralized identity and group-based access management.