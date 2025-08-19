# Exchange Online🌐 **Domaines acceptés**

- Un **domaine accepté** = domaine DNS sur lequel **nous avons autorité**.
- Il permet aux utilisateurs d’envoyer/recevoir des mails pour ce domaine.
- ⚠️ **Ne pas ajouter un domaine externe** comme orange.fr → sinon, Exchange croira que nous gérons ce domaine → pas de requête DNS → échec de remise.

**Types :**

- ✅ **Faisant autorité** : domaine géré depuis Microsoft 365.
- 🔄 **Relais interne** : transfert vers un autre domaine ; nécessite des **connecteurs d’envoi/réception**.



🔎 **Suivi des messages**

- Permet de **tracer les envois/réceptions** de mails dans l’organisation.
- 👀 On peut voir **expéditeur, destinataire, objet**, mais **pas le contenu**.
- 🚨 À utiliser uniquement à des fins de dépannage, pas de surveillance abusive.
- Possibilité de générer des **rapports détaillés ou synthétiques**, filtrés par plage horaire.



🛡️ **Règles de transport (Transport Rules)**

- Appliquées par les serveurs Exchange pour :
  - 🔒 **Limiter les flux** (ex : bloquer envois entre utilisateurs spécifiques).
  - ✂️ **Modifier ou supprimer** des messages (ex : pièces jointes interdites).
  - 📋 **Journaliser ou rediriger** les courriers pour analyse.
  - 🧼 **Filtrer le contenu** (ex : expressions régulières pour détecter des données sensibles).

**Fonctionnement :**

- 🔁 Basées sur :
  - **Conditions** (contenu, en-têtes, expéditeurs, destinataires, etc.)
  - **Actions** (supprimer, rediriger, journaliser, etc.)
  - **Exceptions**
- 🧠 Attention aux **chevauchements** → comportements inattendus !
- 🥇 Ordre de priorité crucial (règle en haut = priorité 0).
- 🔍 Possibilité d’utiliser des **regex** (ex : numéros de téléphone ou CB).

🔌 **Connecteurs**

- **Pas nécessaires par défaut** → Exchange Online est fonctionnel immédiatement.
- 🔗 Utiles pour :
  - Relier un **Exchange On-Premise** à Microsoft 365.
  - Autoriser des **équipements tiers** (copieurs, sites web, applis métiers).
  - Appliquer des comportements spécifiques selon les **domaines cibles**.
  - Accepter du courrier **entrant depuis des partenaires**.

**Utilisation d’un relais SMTP interne :**

- 🎯 Permet une **authentification simplifiée** pour les équipements internes.
- 🔐 **Sécurise** le lien entre le relais SMTP et Microsoft 365.
- 🔥 **Réduit la surface d’attaque** en limitant les machines SMTP autorisées.
- 📉 **Allège l’entrée SPF** (moins d’IP à inscrire).
