# Composantes du domaine**🗺️ Sites AD : la logique géographique**

Un **site** = regroupement de machines selon **leur emplacement physique** (et leur plage IP).

Objectifs : **Optimiser la réplication AD, Améliorer la proximité réseau**

Ex. : Site **Nantes** → 10.44.0.0/16 / Site **Rennes** → 10.35.0.0/16

💡 Chaque site peut avoir son propre **DC local**, pour éviter les lenteurs réseau.



**⚙️ Prérequis avant installation AD**

✔️ Serveur Windows proprement installé

✔️ Adresse IP **fixe**

✔️ Plan IP bien pensé

✔️ Nom d’hôte bien défini

✔️ Rôle AD DS + composants installés

✔️ Structure d’entreprise étudiée en amont (domaines, sites, OU…)



**📈 Niveaux fonctionnels d’AD**

Un **niveau fonctionnel** dépend de la version Windows Server utilisée sur les DC.

🔄 Il définit **les fonctionnalités disponibles** (ex. : la corbeille AD n’existe pas avant 2008 R2).

🎯 Toujours choisir le niveau **le plus haut possible**, compatible avec les serveurs existants.



**🧱 Gestion des contrôleurs de domaine**

On peut :

- **Promouvoir** un serveur → DC
- **Rétrograder** un DC → serveur membre

⚠️ Ces actions impactent tout le domaine (voire la forêt), donc à faire **avec planification**.



**🧠 Les rôles FSMO : les rôles maîtres**

Les **FSMO (Flexible Single Master Operations)** sont 5 **rôles critiques** répartis entre forêt et domaine :

**🏰 Niveau forêt (1 seul DC par rôle, dans toute la forêt)**

- **Maître de schéma** 🧬 : modifie la structure des objets AD (ajout attributs, nouveaux types…)
- **Maître de noms de domaine** 🌐 : gère les ajouts/suppressions de domaines dans la forêt

**🏠 Niveau domaine (1 seul DC par rôle, par domaine)**

- **Maître RID** 🆔 : distribue les identifiants uniques (SID) pour chaque objet
- **Maître d’infrastructure** 🔁 : assure la correspondance entre objets inter-domaines (ex. : groupes avec membres externes)
- **Émulateur PDC** 🕘 : compatibilité avec anciens serveurs NT, priorité pour l’authentification, synchronisation de temps, changements de mot de passe

💡 En cas de perte d’un FSMO, certaines fonctions deviennent instables ou impossibles (création d’utilisateurs, modification du schéma...).
