# Types de BAL**📧 Boîte aux lettres utilisateur**

- Envoi/réception d’e-mails.
- Attribution de stratégies (comportement, règles…).
- Gestion des restrictions (taille, types de pièces jointes).
- Redirection des e-mails (avec ou sans copie).
- Délégation d’accès à d'autres utilisateurs.

🔄 Exemples de redirection : utile pour un remplacement temporaire

sans partage de mot de passe.



**🏢 Boîte aux lettres de ressource**

- **Salle** : permet de gérer la réservation de lieux physiques.
  - New-Mailbox -Name "Salle 3.10" -DisplayName "Salle 3.10" -Room
- **Équipement** : permet de gérer la réservation de matériel.
  - New-Mailbox -Name "Vidéoprojecteur" -DisplayName "Vidéoprojecteur" -Equipment



**👥 Boîte aux lettres partagée**

- Accès par plusieurs utilisateurs, sans coût de licence (⚠️ conditions à respecter).
- Accès total ou délégation :
  - **Accès total** : lecture/gestion complète.
  - **Envoyer en tant que** : envoi depuis cette boîte avec cette identité.
  - New-Mailbox –Shared –Name "support" –DisplayName "support" –Alias "support"



**🌐 Contacts et utilisateurs de messagerie**

- **Contact** : adresse mail externe, non authentifiable (ex : fournisseur).
- **Utilisateur de messagerie** : adresse externe **authentifiable** (ex : prestataire).



**📦 Boîte aux lettres de groupe**

- **Liste de distribution** : messages envoyés à plusieurs destinataires (copie multiple).
- **Liste de distribution dynamique** : idem, mais basée sur une requête LDAP.
- **Groupe de sécurité avec messagerie** : attribue des permissions **et** reçoit des mails.

**🛠️ Fonctionnalités avancées d’une boîte aux lettres**

**📨 Options de remise**

- Taille maximale des messages.
- Options de redirection.
- Affichage des infos courrier lors de la rédaction d’un nouveau message.

**📬 Gestion des protocoles IMAP4 et POP3**

- Active ou désactive l’accès par clients externes (Thunderbird...).
- ⚠️ Limite l’accès aux fonctionnalités comme **calendriers** et **contacts**.



**📊 Quota (PowerShell requis)**

- Set-Mailbox –Identity "Jean Petit" –IssueWarningQuota 49gb –ProhibitSendQuota 49.5gb –ProhibitSendReceiveQuota 50gb –UseDatabaseQuotaDefaults $false
- Get-Mailbox jpetit@eni-ecole.ovh | fl *quota*



**♻️ Rétention et suppression**

- Conservation des éléments supprimés :
  - Set-Mailbox –Identity "Jean Petit" –RetainDeletedItemsFor 30
  - Get-Mailbox -SoftDeletedMailbox



**🗃️ Archivage et litige**

- **Archivage** : active une boîte archive pour libérer de l’espace.
- **Conservation pour litige** :
  - Inactive par défaut.
  - Conserve les éléments et leurs modifications (contexte juridique).
