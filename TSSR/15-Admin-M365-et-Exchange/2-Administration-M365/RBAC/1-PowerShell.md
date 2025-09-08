# **💻 Commandes PowerShell (Module MgGraph)**

## **Installer le module (si besoin)**
```powershell
Install-Module Microsoft.Graph -Scope CurrentUser
```


## **Se connecter (avec droits adéquats)**
```powershell
Connect-MgGraph -Scopes "RoleManagement.ReadWrite.Directory","User.Read.All"
```

## **Lister tous les modèles de rôles disponibles**
```powershell
Get-MgDirectoryRoleTemplate
```


## **Activer un rôle dans le tenant (ex : Administrateur global)**
```powershell
$roleTemplate = Get-MgDirectoryRoleTemplate | Where-Object {$_.DisplayName -eq "Company Administrator"}
Enable-MgDirectoryRole -RoleTemplateId $roleTemplate.Id
```


## **Lister les rôles activés dans le tenant**
```powershell
Get-MgDirectoryRole
```


## **Voir les membres d’un rôle**
```powershell
Get-MgDirectoryRoleMember -DirectoryRoleId <RoleObjectId>
```


## **Ajouter un utilisateur à un rôle**
```powershell
New-MgDirectoryRoleMemberByRef -DirectoryRoleId <RoleObjectId> -BodyParameter @{
  "@odata.id" = "[https://graph.microsoft.com/v1.0/users/<UserId>](https://graph.microsoft.com/v1.0/users/%3cUserId%3e)"
  }
```


## **Retirer un utilisateur d’un rôle**
```powershell
Remove-MgDirectoryRoleMemberByRef -DirectoryRoleId <RoleObjectId> -MemberId <UserId>
```


## **Lister les rôles assignés à un utilisateur**
```powershell
Get-MgUserAppRoleAssignment -UserId <UserId>
```
