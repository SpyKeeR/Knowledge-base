# Configuration de base IOS**🏷️ Changer le nom de l’équipement**

Pour personnaliser ton switch ou routeur :

- hostname NomChoisi ➜ définit le nom
- no hostname ➜ remet le nom par défaut (Switch ou Router)

👀 Astuce : le nom apparaît dans le prompt, pratique pour repérer l'équipement où tu bosses.

**🔐 Sécuriser la ligne console**

Accès physique ? Ligne console ! Pour éviter les intrus :

- line console 0 ➜ on entre dans la ligne
- password xxxx ➜ mot de passe requis à l’accès
- login ➜ rend le mot de passe obligatoire à la connexion

🔁 Pour sortir de ce mode ➜ exit ou end



**🧰 Mot de passe administrateur + chiffrement**

Renforce la sécurité avec :

- enable secret xxxx ➜ protège l’accès au mode privilégié
- service password-encryption ➜ chiffre *tous* les mots de passe visibles dans le running-config

📋 Pour vérifier que c’est chiffré ➜ show running-config

**🌐 Configurer les lignes VTY (accès distant)**

Pour les connexions **Telnet/SSH** :

- line vty 0 15 ➜ s’applique à 16 lignes virtuelles
- password xxxx + login ➜ protège l’accès distant

🎯 Toujours mettre un **mot de passe fort** ici, car ces lignes sont exposées en réseau !

**🧱 Mot de passe : bonnes pratiques ANSSI**

➡️ Mots de passe : uniques, sans lien perso, non partagés, changés régulièrement

🧠 Conseil : prépare-toi à appliquer des règles de politique de mot de passe en entreprise, genre 8 caractères, complexité, durée de vie…

**📨 Message du jour (MOTD)**

Pour afficher un message à chaque connexion (ex : avertissement légal ou message de bienvenue) :

- banner motd #Votre message ici#

💡 Délimiteurs personnalisables (#, !, %, etc.), mais doivent être identiques au début et à la fin.
