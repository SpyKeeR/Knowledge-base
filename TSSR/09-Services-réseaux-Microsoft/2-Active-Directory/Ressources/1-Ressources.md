# Ressources**🗂️ Qu’est-ce qu’une ressource partagée ?**

Une **ressource partagée** est tout **élément accessible en réseau** dont plusieurs utilisateurs peuvent avoir besoin :

📁 Dossier commun – 🖨️ Imprimante réseau – 💾 Espace disque – 🔗 Application métier

👉 Ces ressources sont **au cœur de l’organisation** car elles **facilitent le travail en équipe**

👉 Elles doivent être **accessibles, sécurisées et correctement administrées**



**🛠️ Le rôle des administrateurs systèmes**

L’admin sys gère **la sécurité, la disponibilité et l’organisation** des ressources partagées :

✅ Attribution des droits d'accès (lecture, écriture, modification…)

✅ Définition des groupes de sécurité pour simplifier la gestion

✅ Application de **stratégies de confidentialité** selon les données (RH, finances, etc.)

💡 Exemple : Les fichiers du service RH ne doivent pas être visibles par les commerciaux.

➡️ On isole ces fichiers dans des partages spécifiques, accessibles uniquement via **des ACL bien définies** (listes de contrôle d’accès)



**🖨️ Périphériques partagés : cas des imprimantes**

Les imprimantes réseau sont aussi **des ressources critiques** dans certains contextes :

- On peut les **assigner à un service** précis
- On peut en **restreindre l’accès par groupe ou utilisateur**
- La gestion centralisée permet aussi le **suivi de l'utilisation** (par logs ou quotas)

👉 Ex : Imprimante réservée à la Direction, **non visible dans la liste globale** des imprimantes.



**💾 Gestion des espaces disques partagés**

L’espace disque partagé (souvent sur un serveur de fichiers) doit être :

✅ Structuré par services (ex : \SRV-FICHIERS\Marketing, \SRV-FICHIERS\Compta…)

✅ Sécurisé avec des **droits NTFS et partages imbriqués**

✅ Sauvegardé régulièrement pour éviter toute perte de données

🎯 L’objectif = permettre un accès rapide et sécurisé aux données de travail sans chevauchement non contrôlé.
