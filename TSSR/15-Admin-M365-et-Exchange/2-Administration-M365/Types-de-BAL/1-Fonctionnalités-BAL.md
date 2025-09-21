# Fonctionnalités BAL

## **📏 Gestion des quotas**

Uniquement via PowerShell : `Set-Mailbox –Identity "labeni" –IssueWarningQuota 49gb –ProhibitSendQuota 49.5gb –ProhibitSendReceiveQuota 50gb`


## **🗑️ Rétention des éléments supprimés**

- Définir la durée de rétention : `Set-Mailbox –Identity "Lucie Fer" –RetainDeletedItemsFor 30`
- Lister les boîtes aux lettres supprimées (dans la corbeille) : `Get-Mailbox -SoftDeletedMailbox`


## **📆 Stratégie de partage de calendrier**

- Contrôle des infos partagées : heure, objet, lieu, titre
- Applicable aux utilisateurs externes à l'organisation


## **🎛️ Stratégie d’attribution de rôle (RBAC)**

- Attribution de droits via groupes RBAC
- Gestion des rôles de modification et permissions administratives


## **🗂️ Stratégie de rétention et balises**

- Objectif : gestion du cycle de vie des mails, gain de place, besoins juridiques
- S'applique à : SharePoint, Exchange, OneDrive, Groupes M365, dossiers publics
  - 📁 Déplacement vers archive après 2 ans
  - 🕑 Déplacement du dossier éléments récupérables vers archive après 14 jours


## **📚 Stratégie de carnet d'adresses**

- Fournir des carnets d'adresses différenciés selon le contexte (géographique ou stratégique)


## **🌐 Outlook Web App (OWA)**

- Accès messagerie via navigateur (activé par défaut)
- Stratégie OWA : activation/désactivation de certaines fonctionnalités


## **📱 Appareils mobiles**

- **ActiveSync** activé par défaut : accès à la messagerie sur mobile
- Stratégie pour gérer :
  - 🔐 Sécurité des connexions
  - 🔓 Autorisation ou non d'accès selon critères


## **✉️ IMAP4 & POP3**

- Accès via clients compatibles (Outlook, Thunderbird, Eudora, etc.)
- Limitations :
  - ❌ Pas d'accès aux calendriers ni aux contacts
  - ⏱️ Délai de réception dû à l’utilisation de proxy


## **⚖️ Conservation pour litige**

- Désactivé par défaut
- Conserve les éléments et enregistre les modifications pour aspects juridiques


## **📥 Archivage**

- Activation d’une boîte aux lettres d’**archive** pour stocker les anciens messages


## **📬 Options de remise**

- S'appliquent à tous types de destinataires
- Restrictions possibles sur :
  - 📏 Taille des messages
  - 🚫 Conditions de remise


## **📧 Adresses de courrier**

- ⚠️ À ne pas modifier manuellement
- Les infos apparaissent lors de la création de nouveaux mails (ex : "Lucie")
