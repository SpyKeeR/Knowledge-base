# PowerShell**💻 Commandes PowerShell (Module MgGraph)**

**Installer le module (si besoin)**

Install-Module Microsoft.Graph -Scope CurrentUser



**Se connecter (avec droits adéquats)**

Connect-MgGraph -Scopes "RoleManagement.ReadWrite.Directory","User.Read.All"



**Lister tous les modèles de rôles disponibles**

Get-MgDirectoryRoleTemplate



**Activer un rôle dans le tenant (ex : Administrateur global)**

$roleTemplate = Get-MgDirectoryRoleTemplate | Where-Object {$_.DisplayName -eq "Company Administrator"}

Enable-MgDirectoryRole -RoleTemplateId $roleTemplate.Id



**Lister les rôles activés dans le tenant**

Get-MgDirectoryRole



**Voir les membres d’un rôle**

Get-MgDirectoryRoleMember -DirectoryRoleId <RoleObjectId>



**Ajouter un utilisateur à un rôle**

New-MgDirectoryRoleMemberByRef -DirectoryRoleId <RoleObjectId> -BodyParameter @{

"@odata.id" = "[https://graph.microsoft.com/v1.0/users/<UserId>](https://graph.microsoft.com/v1.0/users/%3cUserId%3e)"

}



**Retirer un utilisateur d’un rôle**

Remove-MgDirectoryRoleMemberByRef -DirectoryRoleId <RoleObjectId> -MemberId <UserId>



**Lister les rôles assignés à un utilisateur**

Get-MgUserAppRoleAssignment -UserId <UserId>
