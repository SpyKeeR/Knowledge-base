# Types de blocs

Un système de fichiers Linux est organisé en plusieurs **types de blocs**, chacun ayant son rôle bien précis :

## 🧩 **Superbloc**
Contient **les infos vitales** du FS.
  - Taille des blocs
  - Taille totale du FS
  - Nombre de montages effectués
  - Nombre max de montages avant forçage de vérification
  - Date du dernier montage
  - Intervalle max entre deux vérifications
  - Pointeur vers l'inode racine

🔥 **Important** : le superbloc est **dupliqué** plusieurs fois pour pouvoir récupérer le FS en cas de pépin !

## - 🧩 **Blocs d'inodes** 
Blocs qui stockent **les inodes**.
  - Chaque **inode** fait **128 octets**
  - 📄 Type de fichier (fichier classique, répertoire, lien symbolique...)
  - 🔒 Mode et **droits d'accès** (lecture, écriture, exécution)
  - 🔗 Nombre de **liens physiques** (si = 0 ➔ fichier supprimé)
  - 👤 UID (propriétaire) et GID (groupe)
  - 📏 Taille du fichier
  - 📆 Dates importantes :
    - **atime** : dernière lecture
    - **mtime** : dernière modification de contenu
    - **ctime** : dernière modification de métadonnées
  - 📍 **15 adresses** pointant vers les blocs de données du fichier
  - Donc, sur un bloc de 4 Ko ➔ **32 inodes** par bloc.

### 🧩 **Bloc d'indirection** 
→ Permet de **rediriger** vers d'autres blocs (très utilisé pour gros fichiers qui ne tiennent pas en un bloc direct).
### 🧩 **Blocs de données** 
→ Stockent **les vraies données** du fichier (ex: le contenu de ton .txt, ton image, ta vidéo...).
### 🧩 **Table des inodes** 
→ Liste de **tous les blocs d'inodes** existants dans le FS.
### 🧩 **Table des inodes libres** 
→ Liste de tous les **inodes disponibles** (libres) pour accueillir de nouveaux fichiers.
### 🧩 **Table des blocs libres** 
→ Liste de tous les **blocs de données disponibles**.

