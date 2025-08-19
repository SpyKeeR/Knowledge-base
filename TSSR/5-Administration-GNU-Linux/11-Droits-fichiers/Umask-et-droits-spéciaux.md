# Umask et droits spéciaux**🎭 umask – Masque de permissions par défaut**

Le **umask** détermine les **droits de base** attribués automatiquement aux **nouveaux fichiers** et **nouveaux dossiers**.

Il soustrait des droits maximaux : **Fichier** : base = 666 (rw-rw-rw-) / **Dossier** : base = 777 (rwxrwxrwx)

📌 Exemple avec umask 022 : Fichier : 666 - 022 = **644** → rw-r--r-- / Dossier : 777 - 022 = **755** → rwxr-xr-x



**🔧 Où définir le umask selon le besoin ?**

| **Dans un terminal (umask 022)** | Session utilisateur en cours (temporaire) |
|----|----|
| **~/.bashrc, ~/.profile, etc.** | Toutes les sessions futures de l’utilisateur |
| **/etc/profile** | Tous les utilisateurs (session shell) |
| **/etc/login.defs** | Valeur par défaut pour les nouveaux utilisateurs |
| **/etc/default/useradd** | Affecte les nouveaux comptes créés |
| **Fichiers ou scripts de services** | Umask spécifique à un service ou une application |
|  |  |

**🧨 Les droits spéciaux**

| **Droit spécial** | **Valeur octale** | **Effet sur fichiers** | **Effet sur dossiers** |
|----|----|----|----|
| **Setuid** | 4 | Le fichier s’exécute avec les droits du **propriétaire** | Aucun effet |
| **Setgid** | 2 | Le fichier s’exécute avec les droits du **groupe** | **Fichiers et sous-dossiers(setGID aussi) créés** héritent du **groupe parent** |
| **Sticky Bit** | 1 | Maintenait un binaire en swap pour accès rapide (obsolète) | Seul le **propriétaire du fichier ou root** peut supprimer ses fichiers |

Appliquer ces droits spéciaux soit par la valeur octale : *4*644 ou symboliquement : u+s, g+s, o+t



**🔎 Représentation dans ls -l**

Quand un droit spécial est activé, il est visible à la place du x dans les permissions :

- **Lettre minuscule (s ou t)** : le droit spécial est actif *et* x est présent
- **Lettre majuscule (S ou T)** : le droit spécial est actif *mais* x est **absent**

📌 Exemples :

- -rwsr-xr-x → setuid actif
- drwxrwsr-x → setgid sur dossier
- drwxrwxrwt → sticky bit sur /tmp
