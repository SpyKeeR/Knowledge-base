# M365 HardeningMême si Microsoft 365 est un environnement cloud puissant, avec beaucoup de protections natives, il faut toujours renforcer la sécurité, surtout autour des **mails** et des **données**. Les attaques sur la messagerie (phishing, usurpation, malwares) sont les plus courantes, donc il faut des mécanismes solides pour éviter les faux mails, protéger les identités, et garantir la disponibilité des données.



🌐 **La gestion des enregistrements DNS pour sécuriser les mails**

Les enregistrements DNS sont la base pour prouver que les mails envoyés par un domaine sont légitimes.

**SPF (Sender Policy Framework)**

- Sert à **limiter les adresses IP autorisées à envoyer des mails pour votre domaine**.
- C’est un enregistrement TXT dans votre DNS public.
- Il liste les IP ou serveurs qui ont le droit d’envoyer des mails au nom de votre domaine.
- Exemple : v=spf1 include:spf.protection.outlook.com -all
- Important de bien inclure les serveurs Microsoft 365 ET tout autre serveur tiers (applications métier, serveurs internes…).
- Cela réduit le risque que des spammeurs utilisent votre domaine pour envoyer des mails frauduleux.

**DKIM (DomainKeys Identified Mail)**

- Plus avancé que SPF, DKIM ajoute une **signature cryptée dans l’en-tête du mail**.
- Cela authentifie le mail, prouvant qu’il vient bien du serveur légitime et qu’il n’a pas été modifié.
- Pour l’activer, il faut publier deux enregistrements CNAME dans DNS et configurer M365.
- L’absence de DKIM fait baisser la réputation de vos mails et augmente leur probabilité d’atterrir en spam.

**DMARC (Domain-based Message Authentication, Reporting & Conformance)**

- Combine SPF et DKIM et vérifie que les adresses d’expédition sont alignées (MAIL FROM et FROM).
- Permet de définir une politique en cas d’échec :
  - **None** (pas d’action),
  - **Quarantine** (mettre en quarantaine),
  - **Reject** (rejeter le mail).
- En plus, DMARC offre des rapports pour suivre les tentatives d’usurpation.
- Ensemble, SPF + DKIM + DMARC sont la base d’une défense solide contre l’usurpation d’identité par mail.



🧰 **Solutions tierces pour renforcer la sécurité**

Même si Microsoft 365 propose des protections solides (EOP, ATP), certains domaines peuvent être renforcés par des solutions externes spécialisées :

- **Anti-spam avancé** : Des outils spécialisés souvent plus pointus qu’EOP pour détecter les menaces, gérer finement les quarantaines, et intégrer la sécurité directement dans Outlook sans passer par des consoles externes.
- **Sauvegarde des données** : Très souvent oubliée, la sauvegarde est cruciale.
  - Microsoft 365 ne propose pas de sauvegarde complète et indépendante par défaut.
  - En cas de suppression accidentelle ou malveillante, sans sauvegarde, les données sont perdues.
  - Solutions comme **Skykick**, **Veeam Backup**, ou **Datto** offrent des sauvegardes illimitées, historiques, et restauration rapide.
- **Gestion des identités & MFA (Multi-Factor Authentication)** : Solutions tierces pour renforcer l’authentification au-delà de ce que Microsoft propose, et sécuriser les accès.
- **MDM (Mobile Device Management)** : Pour sécuriser les terminaux mobiles accédant à Microsoft 365.

Ces solutions tierces s’intègrent via API à M365, s’installent rapidement, sans changer les flux MX (donc pas de modification complexe de routage mail), et souvent avec un support spécialisé dédié.



🛡️ **Le rôle de l’anti-spam et des filtres dans Microsoft 365**

- Microsoft 365 active par défaut des filtres anti-malwares et anti-spam.
- Les filtres fonctionnent sur les en-têtes SMTP, les listes blanches (expéditeurs approuvés) et noires (expéditeurs bloqués), ainsi que sur la géolocalisation (bloquer mails de certains pays).
- Plusieurs actions possibles en cas de détection : suppression du mail, suppression des pièces jointes, mise en quarantaine, notifications à l’admin et à l’expéditeur.
- Mais ces filtres ne sont pas infaillibles, d’où l’intérêt d’avoir des solutions tierces complémentaires.
