# Accès par clés**🛡️ Accès SSH : de base → par clé**

Par défaut, la connexion SSH utilise **login + mot de passe**.

Mais → **jamais en root** (interdit par sécurité).

✅ Bonne pratique : passer à un **système de clé SSH** (plus sécurisé, scriptable, sans mot de passe si besoin).

**🧰 Les types de clés SSH**

Différents algos existent : **RSA**, **DSA**, **ECDSA**. 🔒 On utilise généralement **RSA** (le plus répandu et compatible partout).



**🛠️ Générer une paire de clés**

Commande : ssh-keygen

→ Génére **2 fichiers** :

- 🔐 **Clé privée** (id_rsa) → à **garder en local et sécuriser** (backup !)
- 📬 **Clé publique** (id_rsa.pub) → à **copier sur le serveur distant**

📝 Si tu ne précises pas de nom, les fichiers sont créés dans ~/.ssh/.



**🔑 Passphrase ou non ?**

Tu peux protéger ta clé privée par une **passphrase** :

- ✅ Recommandée en cas d'usage manuel (connexion interactive)
- ❌ À éviter si usage **automatisé** (ex : scripts SCP ou rsync entre machines)



**📤 Copier la clé publique sur un serveur**

Commande : ssh-copy-id user@ip

→ Elle ajoute ta clé publique dans ~/.ssh/authorized_keys du serveur.

→ Par défaut, elle utilise la clé id_rsa.pub, sinon précise le chemin avec -i.

⚠️ Le dossier .ssh du serveur doit exister et avoir les bons droits :

- chmod 700 ~/.ssh
- chmod 600 ~/.ssh/authorized_keys



**🚫 Connexion root : interdit sauf par clé**

Par défaut : **interdiction de se connecter en root avec un mot de passe**.

👉 Mais tu peux le faire **via une clé SSH**, en suivant ces étapes sur le serveur :

mkdir /root/.ssh  
chmod 700 /root/.ssh  
touch /root/.ssh/authorized_keys  
chmod 600 /root/.ssh/authorized_keys

**💡 Pourquoi c’est important**

- 🔐 Plus sécurisé : pas de bruteforce possible sur les mots de passe
- 🤖 Pratique : permet d’automatiser les connexions ou transferts
- 🛠️ Pro : c’est la norme dans tous les environnements d’admin sérieuse
