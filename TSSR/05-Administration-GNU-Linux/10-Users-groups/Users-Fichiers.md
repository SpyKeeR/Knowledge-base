# Users - FichiersQuand on parle des utilisateurs sur un système Linux, deux fichiers sont fondamentaux :
- /etc/passwd ➜ **infos générales sur les comptes utilisateurs**
- /etc/shadow ➜ **infos sensibles et sécurité des mots de passe**

Ces fichiers ne doivent **jamais être modifiés à la main**. On utilise des commandes comme useradd, usermod, passwd pour faire ça proprement.



**📄 /etc/passwd : Infos de base sur les comptes**

Chaque ligne représente un **utilisateur** et contient plusieurs champs séparés par : > Voici à quoi correspond chaque champ :

1.  **username** : Le nom de l’utilisateur (login)
2.  **passwd** : Contient généralement un x, signifiant que le vrai mot de passe est dans /etc/shadow
3.  **UID** : Identifiant unique de l’utilisateur (ex : 0 pour root, >=1000 pour les comptes normaux)
4.  **GID** : Groupe principal de l’utilisateur (correspond à un groupe défini dans /etc/group)
5.  **GECOS** : Informations libres sur l’utilisateur (nom complet, téléphone, etc.)
6.  **homedir** : Répertoire personnel (ex : /home/maxime)
7.  **shell** : Shell par défaut (ex : /bin/bash)

📝 Exemple : maxime:x:1001:1001:Maxime Dupont:/home/maxime:/bin/bash



**🔐 /etc/shadow : Mots de passe et sécurité**

Ce fichier est lisible **uniquement par root** et contient des champs critiques pour la sécurité. Chaque ligne correspond à un utilisateur.

Voici les champs, toujours séparés par : :

1.  **username** : Le nom de l’utilisateur
2.  **password** : Le mot de passe chiffré (souvent en SHA-512).

    - **Si vide : pas de mot de passe requis**
    - **Si * ou ! : compte verrouillé (* > Aucun mot de passe autorisé pour la connexion | ! > Compte verrouillé, les anciens mot de passe ne fonctionnent plus)**

3.  **lastchg** : Date du dernier changement de mot de passe (en jours depuis le 01/01/1970)
4.  **min** : Délai minimum (en jours) avant de pouvoir changer le mot de passe
5.  **max** : Durée maximale de validité du mot de passe (au-delà : changement obligatoire)
6.  **warn** : Jours avant expiration où un **avertissement est affiché**
7.  **inactive** : Délai (en jours) après l’expiration du mot de passe avant désactivation du compte
8.  **expire** : Date d’expiration du compte (toujours en jours depuis 1970)
9.  **reserved** : Champ réservé (généralement vide)

🔐 Exemple : maxime:$6$salt$hashDuMotDePasse:19000:0:90:7:14:



**✳️ Signification de * ou ! dans le champ mot de passe (/etc/shadow) :**

| * | Aucun mot de passe autorisé → le compte ne peut pas être utilisé pour se connecter via un mot de passe |
|----|----|
| ! | Compte **verrouillé** → même un ancien mot de passe valide ne permet plus la connexion |
