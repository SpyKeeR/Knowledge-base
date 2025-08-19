# Représentation/Rôles**🧑‍🤝‍🧑 Trois groupes d'utilisateurs**

Chaque fichier ou répertoire a des droits associés à :

- 👤 **User** : l’utilisateur propriétaire
- 👥 **Group** : le groupe propriétaire
- 🌍 **Others** : tous les autres utilisateurs



**🔠 Trois types de droits**

| **Droit** | **Octal** | **Impact sur un fichier** | **Impact sur un répertoire** |
|----|----|----|----|
| r (read) | 4 | Lire le contenu (ex : cat, less) | Lister les fichiers (ex : ls) |
| w (write) (dépend de r) | 2 | Modifier / supprimer le fichier | Créer / supprimer fichiers |
| x (execute) (dépend de r) | 1 | Exécuter un script ou un binaire | Traverser le dossier (ex : cd) |



**🧮 Droits combinés (en octal)**

- rwx = 4 + 2 + 1 = **7**
- rw- = 4 + 2 = **6**
- r-x = 4 + 1 = **5**
- r-- = 4, --x = 1, etc.

Un ensemble complet de droits se lit sur **3 groupes** (user / group / others) : Exemple : rwxr-xr-- = **754**



**📋 Affichage des droits**

Pour afficher les droits d’un fichier ou dossier, on utilise la commande suivante dans le terminal : **ls -l**

Exemple de sortie : **drwxr-xr-x 3 maxime users 4096 avr 24 10:12 Nantes**

- d → c’est un répertoire
- rwx → user a tous les droits
- r-x → group peut lire et entrer
- r-x → others peut lire et entrer aussi



**⚠️ Rappel sur la sécurité**

Donner tous les droits (rwxrwxrwx = **777**) rend le fichier ou dossier accessible à **tout le monde**.

C’est risqué sauf cas spécifiques (ex : scripts de test, partage de fichiers temporaires).
