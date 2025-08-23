# Configuration

## **🔗 Configuration du partage de ressources**

### 🔹 **Qui peut partager des ressources ?**

✅ Un **administrateur** ou un utilisateur avec les **droits adéquats**

✅ Les ressources ne sont accessibles **qu’aux utilisateurs autorisés** avec **identification requise**



### 🔹 **Vérification des droits d’accès**

🔐 L’identité est vérifiée **deux fois** :

- 1️⃣ Au niveau du **partage**
- 2️⃣ Au niveau des **droits NTFS**

📌 **Les autorisations du partage sont restrictives** → L’autorisation la plus faible est appliquée

👉 Exemple : 🔸 John a **Lecture** sur le partage, mais **Modification** en NTFS → Il n’aura que **Lecture**



## **📂 Accéder aux ressources partagées**

### 📌 **Depuis l’explorateur Windows** :

➡️ 📁 \\\NomServeur\NomPartage ou \\\Adresse_IP\NomPartage

➡️ 📌 Pour un accès permanent : **Connecter un lecteur réseau**



### 📌 **Depuis la ligne de commande** :

➡️ `net use X: \\NomServeur\NomPartage (X = lettre du lecteur)`

➡️ `net view \\NomServeur → Liste les partages disponibles`

➡️ `New-SmbMapping -LocalPath X: -RemotePath \\NomServeur\NomPartage (PowerShell)`



## **⚙️ Créer un partage**

### 📌 **Depuis l’explorateur Windows** :

1️⃣ **Clic droit → Propriétés → Partage**

2️⃣ **Partage avancé** (éviter le partage simplifié)

3️⃣ **Supprimer** l’entité "Tout le monde"

4️⃣ **Ajouter** "Utilisateurs authentifiés" avec **Contrôle total**

5️⃣ **Ajuster les permissions NTFS** pour affiner les droits



### 📌 **Depuis la console "Dossiers partagés"**

✅ Permet de **gérer les partages**, voir **les utilisateurs connectés** et **les fichiers ouverts**

✅ Liste aussi les **partages administratifs** (C$, D$, etc.), cachés et réservés aux admins



### 📌 **Depuis la ligne de commande** :

➡️ `net share MonPartage=C:\Dossier /GRANT:"Utilisateurs authentifiés",FULL`

➡️ `New-SmbShare -Name "MonPartage" -Path "C:\Dossier" -FullAccess "Utilisateurs authentifiés" (PowerShell)`

⚠️ **Bonne pratique** : **Toujours donner "Contrôle total" aux utilisateurs authentifiés** sur le partage et **affiner les droits avec NTFS** 🔐


