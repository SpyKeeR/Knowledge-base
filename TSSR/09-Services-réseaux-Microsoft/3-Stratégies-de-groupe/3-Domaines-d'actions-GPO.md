# Domaines d'actions GPO

Les GPO permettent de **centraliser la config** de tout l’environnement Windows.

📌 **Domaines principaux** :

- **Déploiement applicatif**
- **Scripts (ouverture/fermeture de session)**
- **Sécurité (mots de passe, UAC, pare-feu...)**
- **Système (paramètres Windows, profils, explorateur)**
- **Composants (impression, mises à jour, etc.)**
- **Applications tierces** (Microsoft Office, navigateurs, etc.)



## **🗂️ Structure des paramètres GPO**

Chaque paramètre possède :

- 🔸 **Nom** (intitulé clair)
- 🔸 **État** : *Activé*, *Désactivé*, ou *Non configuré*
- 🔸 **Support** (OS, version requise)
- 🔸 **Description** (explication du comportement)
- 🔸 **Commentaire** (facultatif pour l’admin)

🛠️ L’application est simple : tu modifies → "Appliquer" → "OK" et c’est bon !

Les descriptions aident à bien choisir entre activer ou désactiver une option.



## **📦 Modèles d’administration (.admx)**

💡 Les paramètres sont basés sur des **fichiers .ADMX/.ADML** (stockés dans C:\Windows\PolicyDefinitions)

Ces fichiers contiennent **les modèles d’administration** = l’ossature de toutes les options proposées dans GPMC. ✅ Ils couvrent Windows, mais aussi Office, Edge, etc.

Exemples :

- Modifier le fond d’écran / menu démarrer / barre des tâches
- Empêcher l’accès au Panneau de config
- Désactiver les mises à jour automatiques

🧠 On peut **ajouter des ADMX tiers** pour des logiciels non-Microsoft (Adobe, antivirus, etc.).



## **🎨 Customisation logicielle avec GPO**

👔 Tu peux personnaliser **l’interface et les fonctionnalités** des applis, surtout Microsoft Office. 🔹 Exemples de custom :

- Masquer des onglets du ruban
- Définir une signature Outlook automatique
- Empêcher l’accès aux macros
- Restreindre l’impression / désactivation des MAJ
- Config réseau (proxy, etc.)

🔒 Très utile aussi pour **bloquer certaines actions utilisateur** comme éteindre/redémarrer la machine, accéder au gestionnaire de tâches, etc.

