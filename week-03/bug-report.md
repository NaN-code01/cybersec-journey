# Security Report

**Title**: Missing Current Password Verification Allows Unauthorized Password Changes
**Severity**: Medium (The issue requires the victim to be logged in and to leave an unlocked session accessible to another person)

## Description
The password change functionality on simulasi.untirta.ac.id allows an authenticated student to set a new password without first providing or verifying their current password. This creates a re-authentication weakness because anyone who gains access to an already authenticated, unlocked session can change the account password without knowing the existing password. Although this scenario is sometimes described as an authentication or CSRF-related issue, the primary weakness demonstrated here is **insufficient re-authentication** for a sensitive account action.

## Steps to Reproduce
1. Log in to simulasi.untirta.ac.id using a standard student account.

2. Navigate to the **Change Password** page.

3. Observe that the page does not require the current/old password.

4. Enter a new password in the available password fields and click **Save**.

5. Confirm that the password is changed successfully without requiring the previous password or another form of re-authentication.

6. Log out and attempt to log in using the newly configured password.

## Impact
An attacker who obtains access to an unlocked device with an active student session could change the account password without knowing the original password. This could prevent the legitimate account owner from logging in and may allow the attacker to retain access to the account. Depending on the privileges and information associated with the student account, this could also expose personal or academic information and enable further unauthorized actions.

## Remediation / Recommendation
Require the user's current password before allowing a password change. The server should verify the current password before accepting the new password and **should not rely solely on client-side validation**. For additional protection, consider requiring re-authentication for other sensitive account actions, invalidating existing sessions after a successful password change, and implementing appropriate CSRF protection such as anti-CSRF tokens and SameSite cookie settings where applicable.

---

> sebagai catatan pribadii
> 
> **CVSS v3.1 Score:** 6.5 Medium
> **CVSS v3.1 Vector:** `CVSS:3.1/AV:N/AC:L/PR:L/UI:N/S:U/C:N/I:H/A:N`
> 
> | Metric                       | Value             | Reasoning                                                                                                                                                                                                                                |
> | ---------------------------- | ----------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
> | **Attack Vector (AV)**       | **Network (N)**   | The vulnerable password-change functionality is part of a web application accessible through a network. CVSS considers network-based exploitation as `AV:N`.                                                                 |
> | **Attack Complexity (AC)**   | **Low (L)**       | No unusual race condition, special configuration, or sophisticated preparation is required. The weakness is consistently present in the password-change flow.                                                                            |
> | **Privileges Required (PR)** | **Low (L)**       | The attacker must have access to an authenticated student session. This is not an unauthenticated attack, but it also does not require administrative privileges.                                                                        |
> | **User Interaction (UI)**    | **None (N)**      | In the specific scenario, the attacker operates the already-unlocked session themselves. The victim does not need to perform an action during exploitation.                                                                              |
> | **Scope (S)**                | **Unchanged (U)** | The vulnerability affects the same security authority/account system rather than crossing into a separate security authority.                                                                                                            |
> | **Confidentiality (C)**      | **None (N)**      | The described vulnerability itself does not directly disclose information.                                                                                                                                                               |
> | **Integrity (I)**            | **High (H)**      | The attacker can change a security-sensitive account attribute—the account password—without knowing the existing password.                                                                                                               |
> | **Availability (A)**         | **None (N)**      | CVSS Availability concerns the availability of the vulnerable service/resource, not simply whether a user is temporarily unable to log in. FIRST specifically distinguishes account/data effects from service availability. ([FIRST][2]) |
> 
> **CVSS Rationale:** The vulnerability is rated Medium with a CVSS v3.1 score of 6.5. The attack is considered Network-based because the vulnerable functionality is part of a web application. Attack Complexity is Low because no specialized conditions or sophisticated preparation are required. Privileges Required is Low because the attacker must have access to an authenticated student session, but does not require administrative privileges. User Interaction is None in the described scenario because the attacker can directly operate an already authenticated and unlocked session. Scope is Unchanged because the impact remains within the same application security authority. Confidentiality is rated None because the vulnerability does not directly disclose information, while Integrity is rated High because it allows an attacker to change the account password without verifying the existing password. Availability is rated None because the described impact concerns access to an individual account rather than the availability of the application or service.
> 
> CVSS v3.1 document - https://www.first.org/cvss/v3-1/specification-document