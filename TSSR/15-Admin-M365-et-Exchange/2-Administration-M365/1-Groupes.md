# Groupes

## **📦 Groupe Microsoft 365**

- **But** : favoriser la **collaboration**.
- Inclus :
  - 📬 Boîte de réception Outlook partagée
  - 📅 Calendrier partagé
  - 📁 Bibliothèque de documents SharePoint
  - 📋 Planificateur (Planner)
  - 📝 Bloc-notes OneNote
- 🧠 **Groupes dynamiques possibles** via des règles LDAP (Azure AD).
- ✅ Recommandé par défaut pour un usage collaboratif complet.
- ⚙️ Création = génération automatique d’un **site SharePoint**.



## **📬 Groupe de distribution**

- **But** : envoyer un **email collectif** à plusieurs utilisateurs.
- 🔁 Chaque membre reçoit une **copie individuelle**.
- 🔓 Peut accepter des mails d’expéditeurs **externes**.
- ⚠️ Gérer les droits d’adhésion pour **éviter les fuites** d’information.
- 📥 Peut être combiné (hybride) avec un groupe de sécurité → déconseillé pour la clarté des usages.

## **🛡️ Groupe de sécurité**

- **But** : gérer des **droits d'accès** (SharePoint, Teams, etc.).
- 🗝️ Contrôle l’accès à des ressources spécifiques.
- 🎯 N’a **pas** pour vocation l’envoi d’e-mails.



## **📈 Liste de distribution dynamique**

- **But** : ajout/retrait **automatique** des membres selon des règles (ex : service, localisation…).
- 👷‍♂️ Basée sur les **attributs utilisateurs** dans Active Directory.
- 🚫 Pas besoin de gérer manuellement les adhésions.



## **⚙️ Paramètres à configurer :**

- 🏷️ Nom, description, email du groupe
- 👑 Propriétaire(s)
- 👥 Membres
- 🔐 Confidentialité (publique ou privée)
- 🌍 Autoriser ou non les expéditeurs externes
