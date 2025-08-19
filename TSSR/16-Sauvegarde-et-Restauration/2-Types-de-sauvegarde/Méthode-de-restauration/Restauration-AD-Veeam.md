# Restauration AD - Veeam🔍 **Restauration granulaire d’un objet AD**

Veeam permet de restaurer **finement un ou plusieurs objets Active Directory** (utilisateurs, OU, ordinateurs, etc.) sans restaurer l'intégralité du système.

✅ **Conditions nécessaires** :

- Activation de l’option **Application-Aware Processing**
- Utilisation d’un **compte avec droits d’administrateur du domaine**
- 💡 Il est **recommandé d’isoler les Contrôleurs de Domaine** dans un job de sauvegarde dédié avec ce compte

🛡️ Cette approche respecte la **politique du moindre privilège**, en n’accordant que les droits nécessaires à la tâche.



🎯 **Application-Aware Processing – Pourquoi c’est important ?**

- Permet d’avoir une **sauvegarde cohérente** des applications comme Active Directory
- S’appuie sur **VSS (Volume Shadow Copy Service)** pour capturer les données en cours d’utilisation
- 📦 Cible spécifiquement les **bases AD** (NTDS.DIT) et les **objets de l’annuaire**



📂 **Restauration d’une stratégie de groupe (GPO)**

Veeam permet aussi la **restauration granulaire des GPO** (Group Policy Objects), grâce aux mêmes technologies :

- **Application-Aware Processing**
- **VSS** (snapshot cohérent du volume contenant AD et SYSVOL)

🧩 Cette restauration inclut :

- Le **Group Policy Container (GPC)** → stocké dans l’AD
- Les **fichiers associés dans SYSVOL** → scripts, modèles ADM, etc.
