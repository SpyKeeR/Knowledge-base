# Fonctionnement**⚙️ Fonctionnement des GPO**

Les **GPO (stratégies de groupe)** s'appliquent aux **ordinateurs et utilisateurs AD**.

✅ Elles **automatisent, sécurisent, normalisent** le fonctionnement des postes.

❗Mais attention, la **richesse des options** rend la configuration parfois **complexe**.

📌 Exemples : fond d’écran unique 🖼️, désactivation du Panneau de config 🛑, déploiement de scripts de login 🚀…



**📉 Limites et contraintes**

- **Dépendance OS** : les stratégies doivent être compatibles avec les OS cibles
- **GPO ≠ stratégie locale** : les GPO l’emportent toujours si conflit
- **Stratégie locale** : utilisable en environnement sans domaine (hors-sujet ici)



**🛠️ Méthodes de configuration**

- **GPO (console MMC)** : administration centralisée 👌, gestion de masse
- **Registre (regedit)** : manuel, lent, uniquement pour des cas très spécifiques 🐢  
  ➡️ En pratique, **on privilégie toujours la MMC GPMC** pour configurer les GPO.



**🔁 Application des GPO**

- **Actualisation automatique** toutes les **90 ±30 min** sur les postes clients
- **Sur les DC** (Domain Controllers), rafraîchissement toutes les **5 minutes**
- **Forcer l’application** : utiliser la commande gpupdate /force (à connaître par cœur)
- **CSE (Client Side Extensions)** : composants du client qui appliquent les paramètres depuis le DC



**🧾 Politiques par défaut**

2 GPO sont **présentes automatiquement** dans un domaine :

- **Default Domain Policy (DDP)** : s’applique **à tous les utilisateurs du domaine**, dès la racine
- **Default Domain Controller Policy (DDCP)** : s’applique **aux DC uniquement**, pour la sécurité AD

⚠️ On évite de **modifier directement ces GPO par défaut**.

Pour des besoins ciblés (groupes, postes, utilisateurs), on **crée une nouvelle GPO** liée à l’**OU concernée**.
