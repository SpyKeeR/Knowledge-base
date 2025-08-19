# Administration Teams🚀 **Croissance et adoption de Teams**

- Teams est devenu un **outil central du travail collaboratif**.
- En 2020, Microsoft a atteint **115 millions d’utilisateurs actifs quotidiens**.
- Cette croissance, initialement prévue sur deux ans, s’est réalisée en **quelques mois** avec la pandémie.
- Teams a démontré la **robustesse des solutions Microsoft 365**.



🛠️ **Fonctionnalités clés**

- Création d’équipes 👥
- Réunions et visioconférences 📅💻
- Chat, appels, collaboration en temps réel
- Synchronisation avec SharePoint et OneDrive

⚠️ **Risques :**

- Création massive de sites SharePoint 📄
- Multiplication d’équipes non contrôlées → **fuites de données**
- **Surcharge de ressources** (RAM) → performances dégradées



🔧 **Panneau d’administration Teams**

🎛️ Objectif : Conserver **la maîtrise** de l’environnement M365

- Accès au **Teams Admin Center**
- **Visualisation et gestion** des équipes créées
- **Paramétrage de la confidentialité**, des autorisations et des canaux
- **Restriction des actions utilisateurs** (modification/suppression de messages)



🔐 **Limiter la création d’équipes**

👨‍💼 Trop d’équipes = chaos. Solution en 3 étapes :

1.  Créer un groupe autorisé à créer des groupes M365
2.  Appliquer un **script PowerShell Microsoft**
3.  Vérifier que seuls les membres de ce groupe ont les droits de création

➡️ But : éviter les débordements en limitant les créateurs

📂 **Gestion des équipes et canaux**

- Ajout de **canaux standards et privés** selon les besoins
- **Segmentation des membres** par canaux
- **Paramétrage fin** des usages disponibles pour chaque équipe

🎥 **6. Paramétrage des réunions**

- Définir des **stratégies de réunion personnalisées**
- Affecter ces stratégies à des **groupes d’utilisateurs**
- Contrôler :
  - 🎙️ Audio / vidéo
  - 📤 Partage de contenu
  - 👥 Gestion des participants



🏢 **Paramètres à l’échelle de l’organisation**

- Définir les **fonctionnalités activées** pour :
  - 📞 Appels
  - 🧑‍🤝‍🧑 Réunions
  - 💬 Messagerie (notamment pour les invités)
- **Restreindre ou autoriser** l’usage d’applications tierces :
  - 📁 Dropbox, Google Drive, etc.
