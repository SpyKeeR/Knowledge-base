# PowerShell

## **📦 Installation**
```powershell
Install-Module Microsoft.Graph -Scope CurrentUser
```


## **🔐 Connexion**
```powershell
Connect-MgGraph -Scopes "User.ReadWrite.All", "Organization.Read.All"
```


## **🔍 Voir les licences disponibles (SKU)**
```powershell
Get-MgSubscribedSku
```


## **👤 Attribuer une licence à un utilisateur**
```powershell
Set-MgUserLicense -UserId "utilisateur@example.com" -AddLicenses @{ SkuId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" } -RemoveLicenses @()
```
Remplace le SkuId par celui obtenu avec Get-MgSubscribedSku





## **🚫 Retirer une licence**
```powershell
Set-MgUserLicense -UserId "utilisateur@example.com" -RemoveLicenses @("sku-id")
```


## **👥 Attribution groupée par département (exemple)**
```powershell
Get-MgUser -Filter "Department eq 'Informatique'" | ForEach-Object {

Set-MgUserLicense -UserId $_.Id -AddLicenses @{ SkuId = "sku-id" }  
}
```
