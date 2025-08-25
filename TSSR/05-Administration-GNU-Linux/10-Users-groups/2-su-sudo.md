# su / sudo

## **🔁 su – Se connecter en tant qu’un autre utilisateur**

Commande de base : `su [options] [utilisateur]`

**Par défaut sans argument → root**

**Options importantes :**

- \- ou -l : Lance un shell de login complet → charge les variables d’environnement de l’utilisateur cible (ex : $PATH du root).
- -s /bin/shell : Spécifie un shell différent (ex : /bin/bash, /bin/sh…).
- -c "commande" : Exécute une commande directement sans lancer une session interactive.

⚠️ Nécessite le **mot de passe de l’utilisateur cible**, souvent root.



## **🧷 sudo – Élever ses privilèges temporairement**

Commande de base : `sudo commande`

**Fonctionne uniquement si l’utilisateur est autorisé dans /etc/sudoers ou membre du groupe sudo (Debian/Ubuntu).**

**Caractéristiques :**

- Utilise le **mot de passe de l’utilisateur courant**, pas celui de root.
- Permet d’exécuter **une commande spécifique avec les droits root**, sans changer d’utilisateur.

Ajoute une **trace dans les logs** (/var/log/auth.log).

**Commandes utiles :**

- `sudo commande` : exécute une commande avec les droits root.
- `sudo -i` : ouvre une session shell complète de type root (comme su -).
- `sudo -u utilisateur commande` : exécute une commande en tant qu’un autre utilisateur (pas forcément root).



### **🧑‍💼 Donner l’accès à sudo**

Deux méthodes :

1.  **Ajouter au groupe sudo (Debian/Ubuntu)** : `usermod -aG sudo nom_utilisateur`
2.  **Modifier /etc/sudoers via visudo** (sécurisé, évite les erreurs) : Ajout d’une règle personnalisée, ex : nom_utilisateur ALL=(ALL:ALL) ALL



## 📝 **Fichier sudoers (visudo)**

Format d'une ligne : 1️⃣ User ou %Group 2️⃣ Host (souvent ALL) 3️⃣ User ciblé (souvent ALL) 4️⃣ Commandes autorisées

Exemples :
```bash
- root ALL=(ALL:ALL) ALL # root peut tout faire partout
- %admin ALL=(ALL) ALL # membres du groupe admin peuvent tout faire
- sarah localhost=/usr/bin/shutdown -r now # Sarah peut redémarrer la machine
```

📁 *Directive* `#includedir /etc/sudoers.d` : inclut fichiers additionnels pour gestion plus modulaire




