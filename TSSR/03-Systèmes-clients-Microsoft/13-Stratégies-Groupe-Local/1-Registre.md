# Registre

## **📌 Qu'est-ce que la Base de Registre ?**

🔹 **Base de données hiérarchique** stockant toutes les configurations du système.

🔹 **Présente depuis Windows 98**, toujours utilisée aujourd’hui.

🔹 Contient des **paramètres système, logiciels, utilisateurs et matériels**.

🔹 Windows la consulte **en permanence** pour appliquer les réglages.

💡 **Modification avec :** regedit (📌 Attention, manipulation risquée ⚠️).



## **📂 Structure du Registre : Les 5 ruches 🐝**

### 1️⃣ **HKEY_CLASSES_ROOT (HKCR)** 📦

→ Gère **les associations de fichiers** et les informations des applications.

### 2️⃣ **HKEY_CURRENT_USER (HKCU)** 👤

→ Contient les paramètres **de l’utilisateur connecté**.

### 3️⃣ **HKEY_LOCAL_MACHINE (HKLM)** 🖥️

→ Stocke **les paramètres globaux** du système, **valables pour tous les utilisateurs**.

### 4️⃣ **HKEY_USERS (HKU)** 👥

→ Contient les paramètres **de tous les profils utilisateurs**.

### 5️⃣ **HKEY_CURRENT_CONFIG (HKCC)** ⚙️

→ Stocke les **informations matérielles** en cours d’utilisation.



## **🔑 Clés, Sous-clés et Valeurs**

🔹 **Les ruches contiennent des clés**, qui peuvent elles-mêmes contenir **des sous-clés**.

🔹 Chaque clé possède des **valeurs**, qui peuvent être :

- **Valeurs binaires** (0/1) ⚫⚪
- **Valeurs numériques** (DWORD/REG_QWORD) 🔢
- **Chaînes de caractères** (REG_SZ) 📝



## **⚠️ Précautions à prendre**

🔸 **Erreur de manipulation = Risque d’endommager Windows !**

🔸 Toujours **sauvegarder** (Exporter la clé avant modification).

🔸 Éviter de modifier sans **maîtriser l’impact**.

💡 **Utilisation des stratégies de groupe (GPO) pour modifier le registre en toute sécurité** 🛡️.

