# UAC

## **🔍 1️⃣️Qu’est-ce que l’UAC ?**

📌 Par défaut, **tous les utilisateurs sont traités comme des utilisateurs standards**, même ceux du groupe **Administrateurs**.

📌 Une action **modifiant le système** nécessite une **élevation de privilèges** via **l’UAC (User Account Control)**.

📌 L’UAC empêche les programmes malveillants ou les erreurs humaines de modifier le système sans confirmation.



## **🔑 2️⃣️Comment fonctionne l’UAC ?**

✔ **Icône de bouclier 🛡️** : Symbolise une action nécessitant des droits administratifs.

✔ **Si l’utilisateur est administrateur** : Une **confirmation** est demandée avant l'exécution.

✔ **Si l’utilisateur est standard** : Il doit saisir **un mot de passe d’administrateur**.

✔ Un utilisateur **sans jeton d’accès administrateur** ne peut pas exécuter certaines actions sensibles.

**Exemple :** 
- 🔹 Modifier le registre Windows 
- 🔹 Installer/désinstaller un programme 
- 🔹 Modifier les fichiers système 
- 🔹 Gérer les utilisateurs et les groupes



## **🦠 3️⃣️Pourquoi l’UAC est-il important ?**

✅ **Sécurise le système** contre les **modifications involontaires** ou les **programmes malveillants**.

✅ **Avertit** avant toute action **impactant Windows**.

✅ **Empêche** les virus d’obtenir automatiquement **des droits administrateurs**.

⚠ **Ne désactivez pas l’UAC**, sauf cas très spécifique, car cela expose le système à des **risques de sécurité**.



## **⚙️ 4️⃣ Configurer l’UAC**

📌 **Via le Panneau de configuration** → **Comptes d’utilisateurs** → **Modifier les paramètres de contrôle de compte d’utilisateur**.

📌 **Via la console de stratégie locale** (gpedit.msc) pour un réglage avancé.

🔹 **Niveau par défaut recommandé** : **Avertissement uniquement pour les modifications système**.

🔹 **Pour exécuter une commande en administrateur** :

- **Clic droit → Exécuter en tant qu’administrateur**
- **CMD / PowerShell → "Exécuter en tant qu’administrateur"**


