# ACL

Un volume formaté en **NTFS** (*New Technology File System*) permet d’appliquer des **règles de sécurité sur chaque fichier et dossier**.



## **📌 1️⃣️ Les ACL : ’est-ce que c’est ?**

Une **ACL (Access Control List)** est une **liste de permissions** associée à un fichier ou un dossier. Elle permet de :

✔ **Définir qui a accès** à un fichier/dossier.

✔ **Préciser quels droits** sont accordés (lecture, écriture, exécution…).

✔ **Empêcher ou restreindre** certains accès (ex. interdiction de suppression).



## **🛠 2️⃣️ Fonctionnement des ACL sous NTFS**

🔹 **Chaque utilisateur possède un jeton d’accès** 🎫

→ Ce jeton est vérifié lorsqu’il tente d’accéder à un fichier ou dossier.

🔹 **Les autorisations sont définies au niveau du système de fichiers NTFS** 📂

→ Elles sont stockées dans l’**index du volume NTFS**.

🔹 **ACL = Liste des droits d’accès pour chaque objet** 📜

→ Exemple :

- *User1* → **Lecture seule** 📖
- *User2* → **Lecture + Écriture** ✍️
- *Administrateur* → **Contrôle total** 🔧

