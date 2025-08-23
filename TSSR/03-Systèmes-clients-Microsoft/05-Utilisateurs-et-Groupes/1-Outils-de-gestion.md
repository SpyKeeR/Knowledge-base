# Outils de gestion

## **🛠️ 1️⃣️Méthodes de Gestion des Utilisateurs et Groupes**

Il existe plusieurs façons de **créer, modifier ou supprimer** des utilisateurs et des groupes sous Windows :

### **📌 Via l'interface graphique :**

#### 🔹 **Console "Gestion des utilisateurs et groupes locaux" (lusrmgr.msc)**

✅ Disponible uniquement sur **Windows 10 Pro** (pas sur Windows 10 Famille).

✅ Permet une gestion avancée des utilisateurs et groupes locaux.

✅ Accès : Windows + R → tape lusrmgr.msc → Entrée *OU* Gestion de l'ordinateur *OU* mmc.exe

#### 🔹 **Panneau de configuration → Comptes utilisateurs**

❌ **Moins adapté pour les administrateurs**.

✅ Interface simplifiée pour **ajouter/supprimer des comptes standards**.

✅ Accès : Panneau de configuration → Comptes d’utilisateurs.



## **📜 2️⃣️Gestion en Ligne de Commande (CLI)**

Windows propose **plusieurs outils en ligne de commande** pour la gestion des utilisateurs et groupes.

### **📌 CMD (Invite de commandes - cmd.exe)**

✔ Commandes principales :

#### 🖥️ **Utilisateurs locaux**

- Afficher les utilisateurs : ***net user***
- Créer un utilisateur : ***net user NomUtilisateur MotDePasse /add***
- Supprimer un utilisateur : ***net user NomUtilisateur /delete***

#### 🖥️ **Groupes locaux**

- Voir les groupes : ***net localgroup***
- Ajouter un utilisateur à un groupe : ***net localgroup NomGroupe NomUtilisateur /add***
- Supprimer un utilisateur d'un groupe : ***net localgroup NomGroupe NomUtilisateur /delete***

## **⚡ 3️⃣ Gestion avec PowerShell**

### **📌 PowerShell (Invite de commandes - powershell.exe)**

PowerShell permet une **gestion plus avancée** des utilisateurs et groupes locaux avec des **cmdlets dédiées**.

#### 🖥️ **Utilisateurs**

- Lister les utilisateurs : ***Get-LocalUser***
- Créer un utilisateur : ***New-LocalUser -Name "NomUtilisateur" -Password (ConvertTo-SecureString "MonMotDePasse" -AsPlainText -Force)***
- Supprimer un utilisateur : ***Remove-LocalUser -Name "NomUtilisateur"***

#### 🖥️ **Groupes**

- Lister les groupes locaux : ***Get-LocalGroup***
- Ajouter un utilisateur à un groupe : ***Add-LocalGroupMember -Group "Administrators" -Member "NomUtilisateur"***
- Supprimer un utilisateur d’un groupe : ***Remove-LocalGroupMember -Group "Administrators" -Member "NomUtilisateur"***
