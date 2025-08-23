# Centre de sécuritéLe **Centre de sécurité et de conformité Microsoft 365** permet de renforcer la **sécurité des données** et d'assurer la **conformité réglementaire** (comme le **RGPD**) au sein d'une organisation.

Il regroupe désormais des fonctionnalités jadis réparties dans d'autres consoles, pour une gestion plus centralisée.



🧰 **Fonctionnalités principales**

- **DKIM (DomainKeys Identified Mail)** : Signature d’emails pour authentification du domaine.
- **Stratégies DLP (Data Loss Prevention)** :
  - Détection de fuites de données sensibles (RIB, IBAN, cartes d'identité, IP, etc.)
  - Application de règles sur le contenu des mails ou documents.
- **Stratégies de rétention** : Conservation des données selon des durées définies.
- **Importation de fichiers PST** dans Microsoft 365 :
  - Permet d'intégrer d’anciennes archives mail au cloud.
  - Limité par l’abonnement (50 à 100 Go + archivage en ligne).



📦 **Archivage et gouvernance des données**

- **Archivage en ligne** (activable manuellement) :
  - Libère de l’espace dans les boîtes aux lettres.
  - Utilise le stockage Azure.
- **Limitations** :
  - Accès aux archives parfois lent.
  - Incompatibilité avec les smartphones.
  - Risques de confusion si mal communiqué aux utilisateurs (ex. : "j’ai perdu mes mails").
- **Archivage automatique** : utile mais doit être bien expliqué.



🚨 **Alertes et supervision**

- Le système peut générer **des alertes en cas de fuite de données** ou de comportements suspects (ex. : redirection automatique d’emails).
- ⚠️ La **gestion des alertes peut être chronophage**.
- Possibilité de **surveiller les flux de messagerie** via un tableau de bord intégré.
- Le menu **Révision** permet d’**analyser les quarantaines** (mails bloqués pour certains utilisateurs).



🕵️ **Gestion des menaces**

- Mise en place de **stratégies de détection de menaces**.
- Suivi de comportements anormaux comme :
  - **Transfert automatique de mails** vers l’extérieur.
  - **Utilisation suspecte de l’authentification SMTP**.
  - **État de santé des domaines** (sécurité, réputation, authentification).
