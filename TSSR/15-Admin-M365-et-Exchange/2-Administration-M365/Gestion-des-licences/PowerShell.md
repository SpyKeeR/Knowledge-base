# PowerShell**📦 Installation**

Install-Module Microsoft.Graph -Scope CurrentUser



**🔐 Connexion**

Connect-MgGraph -Scopes "User.ReadWrite.All", "Organization.Read.All"



**🔍 Voir les licences disponibles (SKU)**

Get-MgSubscribedSku



**👤 Attribuer une licence à un utilisateur**

Set-MgUserLicense -UserId "utilisateur@example.com" -AddLicenses @{ SkuId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" } -RemoveLicenses @()

Remplace le SkuId par celui obtenu avec Get-MgSubscribedSku





**🚫 Retirer une licence**

Set-MgUserLicense -UserId "utilisateur@example.com" -RemoveLicenses @("sku-id")



**👥 Attribution groupée par département (exemple)**

Get-MgUser -Filter "Department eq 'Informatique'" | ForEach-Object {

Set-MgUserLicense -UserId $_.Id -AddLicenses @{ SkuId = "sku-id" }  
}
