# Profils📌 Un **profil utilisateur** est créé automatiquement **lors de la première connexion** d’un utilisateur.

📌 Il contient **ses fichiers personnels**, **ses paramètres**, **son bureau**, **ses favoris**, etc.

📌 Tous les profils sont stockés dans **C:\Users** (ou **C:\Utilisateurs** en français).

Exemple : Un utilisateur **"Max"** a son profil dans **C:\Users\Max**. Un autre utilisateur **"Alice"** aura son propre dossier **C:\Users\Alice**.



**📂 2️⃣️Contenu d’un Profil Utilisateur**

Chaque dossier **C:\Users[NomUtilisateur]** contient :

✔ **Documents**, **Images**, **Musique**, **Vidéos**… ✔ **Bureau** (fichiers et raccourcis personnels).

✔ **Données des navigateurs** (favoris, cookies...). ✔ **Paramètres des applications et préférences Windows**.

💡 **Attention** : Ces fichiers sont liés à l’utilisateur et ne doivent pas être modifiés directement !



**🌍 3️⃣️Profils Particuliers**

Windows gère plusieurs types de profils :

🔹 **Profil Public** (C:\Users\Public)

✅ Contenu commun **à tous les utilisateurs** de l’ordinateur.

✅ Fichiers accessibles par **tous les comptes locaux**.

🔹 **Profil par Défaut** (C:\Users\Default)

✅ Sert de **modèle** pour la création de nouveaux profils.

✅ Si vous modifiez ce profil, **chaque nouvel utilisateur** héritera de ces modifications.

✅ Peut être personnalisé avec des documents, des raccourcis, des paramètres spécifiques.



**⚠️ 4️⃣ Gérer les Profils Proprement**

📌 **Ne pas supprimer ou modifier les dossiers dans C:\Users directement !**

📌 Les **profils utilisateurs sont liés à la base de registre**, donc une suppression manuelle peut causer des erreurs.

📌 Pour **gérer proprement les profils**, utiliser :

1️⃣ **Panneau de configuration** → **Système** → **Paramètres système avancés** → **Profils utilisateurs**.

2️⃣ **Commande PowerShell** pour supprimer un profil proprement :

Remove-WmiObject Win32_UserProfile -Filter "LocalPath='C:\Users\NomUtilisateur'"

3️⃣ **GPO (Stratégies de groupe)** pour les profils itinérants en entreprise.

mardi 1 avril 2025

09:06
