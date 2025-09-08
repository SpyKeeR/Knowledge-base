# PowerShell

## **✅ Installation du module PowerShell moderne** 
```powershell
Install-Module Microsoft.Graph -Scope CurrentUser
```

## **🔐 Connexion à Microsoft 365 (via Graph)** 
```powershell
Connect-MgGraph -Scopes "Directory.AccessAsUser.All", "RoleManagement.ReadWrite.Directory"
```
💡 Tu peux ajouter d’autres scopes selon ce que tu veux gérer (Groupes, Licences, Devices, etc.)



## **📦 Modules spécifiques encore valides à ce jour (2025)**

| **Usage** | **Module encore utilisé** | **Commande** |
|----|----|----|
| SharePoint Online | ✅ SharePointPnPPowerShellOnline ou PnP.PowerShell | `Connect-PnPOnline` |
| Exchange Online | ✅ ExchangeOnlineManagement | `Connect-ExchangeOnline` |
| Teams | ✅ MicrosoftTeams | `Connect-MicrosoftTeams` |
| Sécurité / Conformité | ✅ ExchangeOnlineManagement | `Connect-IPPSSession` |

📌 Ces modules **restent nécessaires pour certains services** qui ne sont pas encore (complètement) accessibles via Microsoft Graph.



## **🧾 ÉQUIVALENTS Microsoft.Graph des commandes MSOnline**

| **Ancienne commande MSOnline** | **Équivalent moderne avec Microsoft Graph** |
|----|----|
| Connect-MsolService | `Connect-MgGraph -Scopes "User.ReadWrite.All"` |
| Get-MsolUser | `Get-MgUser` |
| New-MsolUser | `New-MgUser` |
| Remove-MsolUser | `Remove-MgUser -UserId "user@domaine.com"` |
| Restore-MsolUser | `Restore-MgDirectoryDeletedItem -DirectoryObjectId <ID>` |



### **✅ Créer un utilisateur** 
```powershell
New-MgUser -AccountEnabled -DisplayName "Jean Petit" -UserPrincipalName "jpetit@eni-ecole.ovh" -MailNickname "jpetit" -PasswordProfile @{ Password = "MotDePasseSécurisé"; ForceChangePasswordNextSignIn = $true }
```


### **✅ Affecter un rôle admin**

```powershell
New-MgDirectoryRoleMemberByRef -DirectoryRoleId $role.Id -BodyParameter @{

"@odata.id" = " <https://graph.microsoft.com/v1.0/directoryObjects/$($user.Id)>"

}
```
### **✅ Supprimer un utilisateur** 

```powershell
Remove-MgUser -UserId "jpetit@eni-ecole.ovh"
```


### **✅ Restaurer un utilisateur supprimé** 

```powershell
Restore-MgDirectoryDeletedItem -DirectoryObjectId <ID_utilisateur>
```


**🧠 Bon à savoir**

- Microsoft.Graph est modulaire : plus de 30 sous-modules. Tu peux installer que ce que tu veux avec : `Install-Module Microsoft.Graph.Users/Groups`
- Tu peux utiliser pour retrouver facilement des cmdlets qui remplacent les anciennes : `Find-MgGraphCommand -Command restore`

