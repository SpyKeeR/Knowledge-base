# Migration

Lorsque nous installons Windows 10 sur un **nouvel ordinateur** (ex. : renouvellement de parc), il est **essentiel** de recréer un **environnement de travail similaire**.

## **📦 Éléments à Migrer**

✅ **Comptes et profils utilisateurs**

✅ **Configurations des logiciels**

✅ **Outils bureautiques**

✅ **Paramètres système** (pilotes, messagerie, polices)

✅ **Fichiers et dossiers**



## **💾 Solutions de Sauvegarde**

🔹 **Solution centralisée** (NAS, serveur de fichiers, cloud...)

🔹 **Outil de sauvegarde Windows 7** *(intégré dans Windows 10, bien que vieillissant)*



## **⚙️ Migration des Paramètres et Profils**

Utilisation de **USMT** (User State Migration Tool) pour transférer :

📌 **Profils utilisateurs**

📌 **Paramètres système**

📌 **Configurations logicielles**

📌 **Exemple de script USMT** : USMT /capture /i:migration.xml /l:logfile.log



## **✅ Pourquoi Migrer Correctement ?**

💡 **Évite la perte de productivité**

💡 **Garantit un environnement fonctionnel rapidement**

💡 **Facilite le passage à un nouveau poste**

