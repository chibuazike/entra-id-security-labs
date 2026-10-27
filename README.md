Microsoft Entra ID Lab: Groups, Dynamic Membership & External (Guest) Collaboration
It walks through managing identities in a Microsoft Entra ID tenant: security groups, dynamic membership rules, custom domains, bulk user operations, and inviting external guest users.

OBJECTIVES
- Create and manage security groups (assigned and dynamic)
- Write dynamic membership rules using the rule builder and rule syntax
- Add and verify a custom domain name
- Bulk create users and bulk invite guest users via CSV
- Invite external users with the Microsoft Graph PowerShell SDK
- Review and restrict external collaboration settings

  
ENVIRONMENT
- Item	Detail
- Platform - Microsoft Entra admin center
- Tenant	Trial / lab tenant (`TROTTLE.onmicrosoft.com`)
- Tooling	Entra admin center, Microsoft Graph PowerShell SDK, Gmail (invitee mailbox)
- Exam alignment	SC-300

  LAB WALKTHROUGH
1. Groups
Reviewed existing groups under Entra ID > Groups > All groups and created a security group (`sg-SC300-O365`) with Assigned membership.
![All groups](screenshots/01-all-groups.png)
2. Dynamic membership rules
Created a dynamic security group and configured its rule with the rule builder / rule syntax editor.
Example rule used (matches all users that have an object ID):
```
user.objectId -ne null
```
For the guest group, a more useful rule targets guest accounts:
```
(user.userType -eq "Guest")
```
> Note: `MemberOf` in dynamic rules is in preview and not recommended for production.
![Dynamic membership rules](screenshots/02-dynamic-membership-rules.png)
The resulting group (`SC300-myDynamicGroup- Guest`) shows Membership type: Dynamic and Dynamic rules processing status: Succeeded.
![Dynamic group overview](screenshots/05-dynamic-group-overview.png)
3. Custom domain names
Checked domains under Entra ID > Custom domain names: the default `onmicrosoft.com` domain and an additional verified subdomain.
![Custom domains](screenshots/03-custom-domains.png)
4. Bulk operations
Used Users > Bulk operations to create users from `SC300BulkUser.csv` and invite guests from `UserInviteTemplate.csv`, then checked the outcome under Bulk operation results.
![Bulk operation results](screenshots/07-bulk-operation-results.png)
5. Inviting an external user (Microsoft Graph PowerShell)
```powershell
Import-Module Microsoft.Graph.Identity.SignIns

$params = @{
    invitedUserEmailAddress = "guest@example.com"
    inviteRedirectUrl       = "https://myapp.example.com"
}

New-MgInvitation -BodyParameter $params
```
The command returns an invitation `Id` and an `InviteRedeemUrl`. Connect first with:
```powershell
Connect-MgGraph -Scopes "User.Invite.All"
```
![Graph PowerShell invitation](screenshots/06-powershell-invitation.png)
6. Receiving the invitation
The invited user receives an email from `invites@<tenant>.onmicrosoft.com`. In this lab it landed in Spam, which is a common gotcha worth checking when testing B2B invitations.
![Invitation email](screenshots/05-invitation-email.png)
7. External collaboration settings
Under External Identities > External collaboration settings, guest user access was set to the most restrictive option: guests can only see properties and memberships of their own directory objects.
![External collaboration settings](screenshots/08-external-collaboration-settings.png)

KEY TAKEAWAYS
- Dynamic groups are evaluated automatically; check Dynamic rules processing status if membership looks wrong.
- Guest invitations can land in spam. Test with a mailbox you can check.
- Restrictive guest access is the safest default for external collaboration.
- Bulk CSV operations are efficient but have service limits (one-hour run time).

RESOURCES
- Microsoft Entra documentation
- Dynamic membership rules
- B2B collaboration overview

License
MIT
