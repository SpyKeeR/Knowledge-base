- 🧩 Contient un **grand nombre de paramètres personnalisables**

- 🤖 Permet d’**automatiser entièrement** les déploiements Windows avec MDT

- 🎯 Adapte le **comportement du déploiement** aux besoins spécifiques de l’organisation

- 🔁 **Aucune mise à jour du partage de déploiement** n’est requise après modification ➜ Les changements sont **pris en compte lors du prochain déploiement**

>  
>
> 📄 **Exemple basique de configuration**
>
> \[Default\]  
> UserID=admin  
> UserDomain=mon_domaine  
> UserPassword=motdepasse  
> ComputerType=Unique  
> OSDComputerName=MON-PC-001  
> JoinDomain=mon_domaine
>
> 🧠 **Exemples de paramètres avancés classés par thème**
>
> 🎛️ **Interactivité de l’installeur**
>
> SkipTaskSequence=YES  
> TaskSequenceID=ENISALLE09  
> SkipUserData=YES  
> SkipApplications=YES  
> SkipAppsOnUpgrade=YES  
> SkipAdminPassword=YES  
> AdminPassword=ENI demo!local  
> SkipSummary=YES
>
> 🔑 **Activation produit**
>
> SkipProductKey=YES  
> ProductKey=MH37W-N47XK-V7XM9-C7227-GCQG9 ; Clé KMS Windows 10 Pro N
>
>  
>
> 🔐 **Autres paramètres de sécurité**
>
> SkipCapture=YES  
> SkipBitlocker=YES

🌍 **Paramètres régionaux**

SkipLocaleSelection=YES  
UserLocale=fr-FR  
KeyboardLocale=040C:0000040C  
TimeZoneName=Romance Standard Time  
SkipTimeZone=YES

 

🖥️ **Nommage et intégration au domaine**

SkipComputerName=YES  
OSDComputerName=ENI-DEMO  
SkipDomainMembership=YES  
JoinDomain=deploy.eni  
DomainAdmin=mdt  
DomainAdminDomain=deploy.eni  
DomainAdminPassword=Pa\$\$w0rd

