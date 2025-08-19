# Intégration / Stratégies**🖥️ Intégration d’un poste Windows 10/11 à Intune**

**🛠️ Méthode 1 : via les paramètres du compte**

1.  Paramètres → Comptes → Accès professionnel ou scolaire
2.  Sélectionner **S’inscrire uniquement à la gestion des périphériques**
3.  🔐 S’identifier avec un **compte Microsoft 365 avec licence Intune**
4.  ✅ Le poste apparaît dans la **console Intune**

**📱 Méthode 2 : via l’application *Portail d’entreprise***

1.  Télécharger l'app depuis le **Microsoft Store**
2.  Ouvrir l’application, cocher « Autoriser mon organisation à gérer mes appareils »
3.  🔐 Se connecter avec un **compte Microsoft 365 avec licence Intune**

**Étapes :**

- 🔹 Étape 1 : Ajouter un compte pro à l’appareil
- 🔹 Étape 2 : Connecter l’appareil à l’entreprise  
  ➡️ L’appareil s’affiche dans la section « home » de l’application et dans le **portail Intune**



**🧾 Profils de configuration**

➡️ Utilisés pour **appliquer des stratégies** aux appareils

**✏️ Création :**

- 📱 Plateformes : Android, iOS/iPadOS, macOS, Windows 10+, Windows 8.1+
- 🧰 Type de profil :
  - Catalogue des paramètres (tous les paramètres)
  - Modèles (actions groupées par thématique)

**🔐 Exemples de stratégies :**

- Mot de passe sécurisé
- Effacement après X tentatives d’échec
- Temps d’inactivité avant verrouillage
- Chiffrement obligatoire
- Blocage de la synchro de photos
- Interdiction des supports amovibles
- Règles similaires aux **GPO classiques**

📌 Appliquer la stratégie :

- Définir les **groupes inclus/exclus**
- Choisir les **règles d’applicabilité**
- 📊 Suivre l’état du déploiement et les **erreurs éventuelles**


