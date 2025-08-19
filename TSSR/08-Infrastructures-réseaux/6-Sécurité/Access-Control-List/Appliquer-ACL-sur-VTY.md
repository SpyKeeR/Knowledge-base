# Appliquer ACL sur VTY**🛡️ Sécurisation des lignes VTY avec une ACL standard**

Le but est de **restreindre l'accès distant (SSH/Telnet)** à un routeur uniquement à des hôtes autorisés via une **ACL IPv4 standard**.

➡️ Cette méthode permet de **protéger l'équipement** en évitant les connexions non autorisées.



**🏗️ Création de l'utilisateur local**

Avant de poser une ACL, on crée un **utilisateur local** pour l'accès distant sécurisé :

➡️ username admin secret class 🎯 Utilisé pour l’authentification locale via SSH ou Telnet.



**📜 Création de l’ACL**

➡️ On crée une ACL nommée (ici admin host) avec :

- remark pour documenter la règle
- permit pour autoriser un réseau spécifique (ex. 192.168.10.0/24)
- deny any pour bloquer tout le reste

👉 Exemple : **permit 192.168.10.0 0.0.0.255 / deny any** (refus implicite rendu explicite)



**🔌 Application sur les lignes VTY**

➡️ On applique l’ACL en **entrée sur VTY 0 à 4** :

- line vty 0 4 : on cible les lignes de terminal
- logging local : active l’authentification via base locale
- transport input telnet : active Telnet (⚠️ à remplacer par SSH en prod)
- access-class admin host in : applique l’ACL sur trafic entrant

📌 Cela empêche toute connexion externe non autorisée, même si elle connaît le login/mot de passe.



**🧪 Vérification via les statistiques**

➡️ Commande : show access-list

Permet de visualiser :

- le **nombre de correspondances** pour chaque règle
- les tentatives autorisées ou bloquées  
  🎯 Exemple : IP 192.168.10.10 → match permit (connexion autorisée) - Autres IP → match deny (connexion rejetée)



**🚨 Importance de cette ACL**

💡 En appliquant une ACL sur les ports **VTY**, tu verrouilles **l'accès administratif à distance**.

✅ Seuls les hôtes connus peuvent **télécommander** l’équipement, ce qui est **essentiel pour la sécurité réseau** 🔐
