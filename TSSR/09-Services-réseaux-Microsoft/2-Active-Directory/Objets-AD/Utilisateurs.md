# Utilisateurs**👤 Utilisateurs dans Active Directory**

Chaque utilisateur AD est un objet riche en propriétés : nom, groupes, profil, etc. Leur création peut se faire via la **console MMC** ou **PowerShell**.

**📄 Propriétés générales**

Un utilisateur possède différentes **informations d’identification** :

- Onglet *Général* : nom complet, description, téléphone
- Adresse postale : localisation physique
- Champs optionnels selon les besoins métier

Ces infos facilitent la gestion et la recherche dans l’annuaire.



**🔐 Informations de connexion et appartenance**

Les paramètres essentiels pour la connexion :

- Nom d'utilisateur + mot de passe
- Statut du compte (actif, expiré, verrouillé)
- **Appartenance à des groupes** = droits et accès hérités

💡 L'intégration à des groupes est **clé pour l'administration des droits**.



**🧰 Modèles d’utilisateurs**

Bonne pratique 💡 : créer des **modèles génériques** (ex : modele_utilisateur) avec :

- Mêmes paramètres de base (OU, groupe, restrictions, etc.)
- Simplifie et accélère la création de comptes
- Réduction d’erreurs humaines

**🗂️ Profils utilisateurs (locaux ou itinérants)**

Chaque utilisateur a un **profil Windows**, qui contient :

- Fichiers persos, configurations, environnement (fond d’écran, raccourcis…)
- Stockés par défaut dans %SystemDrive%\Utilisateurs\UserName%

📌 Sur un domaine, ces profils sont synchronisés avec le serveur.

**🌍 Profils itinérants (Roaming)**

Utile quand l’utilisateur **change souvent de machine** :

- Le profil est **stocké sur un serveur**
- **Téléchargé à la connexion**, pour charger l’environnement habituel
- **Synchronisé à la déconnexion**, pour enregistrer les modifs

⚠️ Plus le profil est lourd (docs, bureau rempli...), plus les temps de chargement/déchargement augmentent.

**📥 Pourquoi importer/exporter des comptes AD ?**

Dans un **AD bien structuré**, automatiser la **création ou l’export de comptes** est un **gain de temps massif** 🕒

👉 Indispensable pour les organisations avec **beaucoup d'utilisateurs/groupes à gérer**

👉 Évite les **saisies manuelles**, réduit **les erreurs**, et permet **l’industrialisation** des déploiements



**💻 PowerShell : outil principal pour l'import**

PowerShell est l’outil **le plus fiable et puissant** pour automatiser l’import d’utilisateurs :

✅ Permet d’importer depuis un fichier CSV 📄

✅ Chaque ligne du CSV devient un compte AD

✅ Création possible dans une OU ciblée, avec mot de passe, login, UPN…

🧠 Le CSV doit être **proprement structuré** : noms de colonnes = paramètres (ex : Prénom, Nom, Login, Email, MotDePasse...)

⚠️ Une erreur dans le CSV = erreur d'import → toujours **vérifier avant d'exécuter**



**🔄 Exemple de scénario typique**

- Tu reçois un fichier RH avec les futurs arrivants
- Tu ajoutes les infos manquantes (login, mot de passe par défaut, etc.)
- Tu lances ton script PowerShell → tous les comptes sont créés en 1 commande
- Tu peux même les **activer, positionner dans la bonne UO**, etc.

👉 Hyper utile pour les **rentrées sco., onboarding entreprise, migration de domaine…**



**🧰 Autres outils d’import : CSVDE (ou LDIFDE)**

🧪 En plus de PowerShell, tu peux utiliser :

- **CSVDE** ➜ utile pour importer/exporter, mais limité (pas de mot de passe, pas de modification d’existant)
- **LDIFDE** ➜ plus complet, mais plus complexe (utilise des fichiers .ldf, peu lisibles)

⚠️ Ces outils sont **anciens et moins souples** que PowerShell

Ils peuvent être pratiques en dépannage ou en environnement très verrouillé.



**📤 Export : toujours utile aussi**

Tu peux aussi faire l’opération inverse → **exporter des objets AD** : Pour audit, Pour migration, Pour archivage, Pour documenter l’existant

PowerShell (encore lui 😄) permet ça facilement avec Get-ADUser ou Get-ADGroup combiné à Export-Csv.
