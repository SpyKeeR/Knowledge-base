# Redirection dossiers utilisateur

La **redirection de dossiers** permet de **délocaliser des éléments du profil utilisateur** (Documents, Bureau, Téléchargements, etc.) vers un **emplacement réseau** 🗂️

🔁 Résultat : **Données centralisées, Sauvegardes facilitées, Postes interchangeables** (flex office, pannes, etc.)



## **🌍 Profils itinérants vs redirection**

👤 Un **profil itinérant** copie **tout le profil utilisateur** sur le réseau → long à charger, plus fragile.

📂 La **redirection**, elle, ne cible **que certains dossiers**, donc plus rapide et plus souple.

🔸 Dossiers redirigeables : Documents, Bureau, Menu Démarrer, Contacts, Téléchargements, Images, Vidéos...

⚠️ Attention à ne pas confondre : 
- **profil itinérant** = tout le profil
- **redirection** = dossiers ciblés



## **🛠️ Redirection via GPO**

Les GPO permettent de **gérer cette redirection en masse**, de façon propre et centralisée :

- 🎯 **GPO ciblant les objets Utilisateurs**
- 🧩 Paramètres de base : même chemin pour tous
- 🧬 Paramètres avancés : redirection selon les groupes (ex : marketing, support, etc.)

🔒 Très important : bien **gérer les permissions NTFS** pour éviter que tous les utilisateurs ne voient les dossiers des autres.



## **🔄 Types de redirection possibles**

1.  📁 **Vers un dossier partagé commun** (ex : \\\srv\dossiers  
    ➕ Simple à mettre en place  
    ➖ Risque de mauvaise gestion des droits

2.  🏠 **Vers le dossier personnel (répertoire d'accueil)** avec sous-dossier %USERNAME%  
    ➕ Sécurité, confidentialité  
    ➕ Structure propre et automatique  
    ➖ Besoin d’un partage bien préparé côté serveur

3.  🔙 **Annulation de redirection** (retour au stockage local)  
    ➕ Utile en cas de restructuration ou déconnexion d’un service réseau  
    ⚠️ Peut entraîner une perte de données si mal géré



## **🔐 Sécurité & bonnes pratiques**

- 🎯 Utiliser des partages administratifs dédiés > \\\SRV01\Redirected
- 🧾 Activer l’option "Créer un dossier pour chaque utilisateur"
- 🔒 Appliquer le modèle de permission : Admin + utilisateur uniquement
- ⚙️ Tester la redirection avec un compte test avant déploiement massif

