# Help Desk Access Incident — Human Resources Shared Folder

## Incident Overview

A simulated Help Desk incident was created for BluePeak Technologies to practice troubleshooting an employee access issue in an Active Directory environment.

### Reported Issue

**User:** Jordan Lee  
**Username:** `jordan.lee`  
**Computer:** `CLIENT01`  
**Resource:** `\\DC01\Human Resources`

Jordan reported that he needed access to the Human Resources shared folder but received an access-denied message when attempting to open the resource.

The issue was reproduced from CLIENT01 while Jordan was authenticated with his BluePeak domain account.

### Objective

The objective of the troubleshooting process was to:

- Verify the affected user's identity.
- Reproduce and confirm the reported issue.
- Review the user's current security-group membership.
- Identify the cause of the access denial.
- Apply an authorized access change.
- Verify that the updated permissions were reflected in the user's Windows security token.
- Confirm that access to the requested resource was successfully restored.

## Troubleshooting and Diagnosis

### Step 1 — Verify User Identity

The affected user was verified from CLIENT01 using:

`whoami`

Result:

`bluepeak\jordan.lee`

This confirmed that the correct BluePeak domain account was being used during troubleshooting.

### Step 2 — Review Current Security Group Membership

Jordan's active Windows security token was reviewed using:

`whoami /groups`

The initial results showed:

- `BLUEPEAK\SG-IT`
- `BLUEPEAK\SG-HR` was not present

This matched the observed behavior because access to the Human Resources shared folder was controlled through the `SG-HR` security group.

### Step 3 — Identify Root Cause

Jordan was authorized for IT resources through membership in `SG-IT`, but he did not have the required `SG-HR` membership for the Human Resources shared folder.

The access denial was therefore caused by Jordan's account not having the required security-group membership.

### Root Cause

**Missing Active Directory security-group membership (`SG-HR`).**

The NTFS and share permissions themselves did not require modification.

## Resolution

### Step 4 — Confirm Authorized Access Change

For this simulated incident, access to the Human Resources shared folder was treated as an approved business request.

Rather than modifying NTFS permissions directly for the individual user, Jordan was added to the existing Active Directory security group:

`SG-HR`

This maintained the BluePeak Technologies RBAC design by granting access through group membership instead of assigning permissions directly to an individual account.

### Step 5 — Verify Active Directory Membership

Jordan's account was verified in Active Directory Users and Computers and showed membership in:

- `SG-IT`
- `SG-HR`

The domain membership was also verified from CLIENT01 using:

`net user jordan.lee /domain`

Active Directory correctly reported both security groups.

### Step 6 — Refresh the User Security Token

Although Active Directory showed the updated `SG-HR` membership, Jordan's existing Windows security token initially did not contain the new group.

A complete sign-out and sign-in was performed on CLIENT01.

After the new session was created, the security token was checked again using:

`whoami /groups`

The new token contained both:

- `BLUEPEAK\SG-IT`
- `BLUEPEAK\SG-HR`

This demonstrated that Active Directory group-membership changes may require a new user logon session before the updated authorization information is reflected in the user's security token.

### Step 7 — Verify Access

Jordan attempted to access:

`\\DC01\Human Resources`

The folder opened successfully.

A test file was then created:

`Jordan-HR-Access-Test.txt`

Successful file creation confirmed that Jordan received the intended Modify access to the Human Resources shared folder.

### Evidence

- `19-Jordan-HR-Access-Denied.png` — Initial access-denied condition.
- `20-Jordan-SG-IT-Membership.png` — Jordan's original IT security-group membership.
- `21-Jordan-HR-Access-Restored.png` — Successful HR share access after the approved group-membership change.

## Incident Outcome

The Help Desk incident was successfully resolved without modifying the existing NTFS or SMB share permissions.

The troubleshooting process identified that the user lacked the Active Directory security-group membership required for the requested resource.

After authorization was confirmed, access was granted through the existing `SG-HR` security group. A fresh Windows logon session updated the user's security token, and access to the Human Resources shared folder was successfully verified.

### Final Status

**Issue:** User unable to access Human Resources shared folder  
**Root Cause:** Missing `SG-HR` security-group membership  
**Resolution:** Added the user to the approved `SG-HR` security group and refreshed the user's logon session  
**Verification:** User successfully accessed the HR share and created a test file  
**Status:** Resolved

## Help Desk and IAM Skills Demonstrated

- Incident reproduction
- User identity verification
- Active Directory account troubleshooting
- Security-group membership analysis
- Windows security-token troubleshooting
- RBAC administration
- Principle of least privilege
- Access-request validation
- SMB and NTFS permission troubleshooting
- Root-cause analysis
- Post-change verification
- Technical incident documentation

## Ticketing Lab Reuse

This incident will later be recreated inside the BluePeak Technologies ticketing system to practice the complete ticket lifecycle:

**Ticket creation → Categorization → Priority → Assignment → Troubleshooting notes → Resolution → User verification → Ticket closure**


## Temporary Access Revocation

After the simulated Help Desk incident was resolved and testing was completed, Jordan's temporary access to the Human Resources shared folder was removed to restore least-privilege access.

### Step 8 — Remove Temporary Group Membership

In Active Directory Users and Computers, Jordan Lee was removed from:

`SG-HR`

His normal `SG-IT` membership was retained.

No NTFS or SMB share permissions were modified.

### Step 9 — Refresh the User Security Token

Jordan was fully signed out of CLIENT01 and signed back in to create a new Windows logon session.

The updated security token was verified using:

`whoami /groups`

The results showed:

- `BLUEPEAK\SG-IT` was present.
- `BLUEPEAK\SG-HR` was no longer present.

This confirmed that the temporary HR group membership had been removed from Jordan's active security token.

### Step 10 — Verify Access Revocation

Jordan attempted to access:

`\\DC01\Human Resources`

Windows returned an access-denied message.

This confirmed that removing Jordan from `SG-HR` successfully revoked his access to the Human Resources shared folder.

### IAM Lifecycle Demonstrated

This incident demonstrated the complete access lifecycle:

**Request → Authorization → Provision Access → Verify Access → Revoke Access → Verify Revocation**

The process maintained role-based access control and the principle of least privilege throughout the incident.

### Additional Evidence

- `22-Jordan-HR-Access-Revoked.png` — HR share access denied after temporary `SG-HR` membership was removed.