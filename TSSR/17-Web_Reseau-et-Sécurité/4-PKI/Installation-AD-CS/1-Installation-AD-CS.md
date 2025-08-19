# Installation AD CS**⚙️ Étape 1 : Installation du rôle AD CS**

- 📦 Lancer l’installation du **rôle "Service de certificats Active Directory"** via le gestionnaire de serveur.
- ➕ Ajouter les **fonctionnalités nécessaires** (la sélection par défaut est généralement suffisante).
- 🧩 Choisir les **services à installer** :
  - ✅ **Autorité de certification**.

**🧰 Étape 2 : Configuration post-déploiement**

- 🛠️ Lancer la **configuration des services de certificats AD CS**.
- 👤 Vérifier que le **compte administrateur** utilisé dispose des droits nécessaires.
- 🎯 Sélectionner les **rôles de service** à configurer.



**🏛️ Étape 3 : Configuration du rôle AD CS**

- 🔧 Spécifier le **type d’installation** :
  - 🏢 **Autorité d’entreprise** (nécessite ADDS).
  - 🏠 **Autorité autonome** (fonctionne sans ADDS).
- 🧱 Définir le **type d'autorité de certification** :
  - 🌳 **AC racine**
  - 🌿 **AC intermédiaire.**
- 🔐 Déterminer la **gestion de la clé privée** :
  - 🆕 Création d’une nouvelle clé.
  - ♻️ Réutilisation d’une clé existante.



**🔒 Étape 4 : Paramètres de sécurité**

- 🛡️ Choisir les paramètres de **chiffrement** :
  - 🔑 **Taille de clé**.
  - 🧮 **Algorithme de hashage**.
- 🏷️ Spécifier le **nom de l’AC** :
  - 🏢 Avec ADDS : rempli automatiquement.
  - 🏠 Sans ADDS : suffixe à définir manuellement.
- 📆 Définir la **période de validité** du certificat racine :
  - ⏳ Période en jours, semaines, mois ou années.

**✅ Étape 5 : Finalisation**

- 📋 Vérifier le **résumé de la configuration** avant de valider.
- 🏁 Lancer l’**installation** de l’autorité de certification.
- 🎉 **Installation terminée**, la PKI est désormais en place.
