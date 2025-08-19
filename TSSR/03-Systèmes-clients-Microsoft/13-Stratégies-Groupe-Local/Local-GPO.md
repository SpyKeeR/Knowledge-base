# Local GPO**📌 Qu'est-ce qu'une LGPO ?**

🔹 **Local Group Policy Object** : permet de **gérer le comportement du poste de travail et des utilisateurs** via une console unique.

🔹 Deux outils principaux :

- **Éditeur d’objets de stratégies de groupe** (gpedit.msc) 📜
- **Stratégie de sécurité locale** (secpol.msc) 🔒  
  🔹 Objectif : **standardiser et sécuriser la configuration des postes** 🛡️.



**⚙️ Que peut-on faire avec une LGPO ?**

✅ **Sécurité** : Renforcer les stratégies de mot de passe 🔑.

✅ **Administration** : Définir les privilèges administrateur 🏗️.

✅ **Interface utilisateur** : Uniformiser l'apparence du bureau 🖥️.

✅ **Restrictions** : Bloquer l'accès à certains menus ou paramètres ⚠️.

✅ **Automatisation** : Exécuter des scripts au démarrage/fermeture de session 📝.

💡 **Actions possibles sur les ruches du registre :**

- **HKEY_LOCAL_MACHINE (HKLM)** → Configuration globale du PC.
- **HKEY_LOCAL_USER (HKLU)** → Paramètres spécifiques à un utilisateur.
- **HKEY_USERS (HKU)** → Paramètres multi-utilisateurs.

📌 **Possibilité de gérer des groupes spécifiques** (ex. Administrateurs) mais pas d'autres groupes locaux 👥.



**🛠️ Configuration et Activation des Paramètres**

🔹 **Des centaines de paramètres disponibles** 🏗️.

🔹 Chaque paramètre peut être :

- **Activé** ✅
- **Désactivé** ❌
- **Non configuré** (Windows applique sa valeur par défaut).

⚠️ **Compatibilité !**

📌 Chaque paramètre inclut une section "Prise en charge" précisant **les versions Windows compatibles**.

📌 Certains paramètres offrent **des options avancées** (valeurs binaires, hexadécimales, chaînes de caractères).
